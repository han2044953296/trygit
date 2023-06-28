---
title: project
date: 2-1 8.40
categories: 日志
comments: "true"
tag: project
---
# jdbctohive

```
package project


import java.util

import org.apache.spark.sql.catalog.Catalog
import org.apache.spark.sql.{DataFrame, Dataset, Row, SparkSession}
import tool.sqlUtils
import tool.getmysqldf
import tool.savefile
import tool.readfile
object jdbctohive {
  System.setProperty("HADOOP_USER_NAME","hadoop")
  val spark = SparkSession.builder().appName("sqoop").master("local[4]").enableHiveSupport().getOrCreate()
  spark.sparkContext.setCheckpointDir("/tmp/checkpoint")

  val getmysqldf = new readfile
  val sqlUtils = new sqlUtils
  val saveFile = new savefile
  private val catalog: Catalog = spark.catalog
  var changecolunm = false
  import spark.implicits._
  import org.apache.spark.sql.functions._


  def main(args: Array[String]): Unit = {
    if (args.length==0){
      println(
        """
          |欢迎使用本程序
          |参数详情 mysql hive
          |-------------------------mysql
          |url 例子 ： jdbc:mysql://bigdata2:3306/try
          |user 例子 ： root
          |password 例子 ： liuzihan010616
          |tablename => 支持谓词下压  例子 ： emp 或者 select * from emp 等
          |driver => com.mysql.jdbc.Driver
          |---------------------------hive
          |mode模式 overwrite append 等
          |hive中的table 例子 bigdata.emp
          |可选参数 分区字段 自动开启的是动态分区 例子 deptno
          |""".stripMargin)
    }

    val url = args(0)
    val user = args(1)
    val password = args(2)
    val table = args(3)
    val driver = args(4)
    // 获取jdbc的df
    val mysqlconnect = getmysqldf.getmysqldataframe(spark, url, user, password, table , driver)
    // 验证指示
    mysqlconnect.show()
    // 生成hive参数数组
    var hiveconf = new Array[String](args.length-5)
    hiveconf = util.Arrays.copyOfRange(args, 5, args.length)
    //hiveconf.foreach(println(_))
    jdbctohive(args,catalog,mysqlconnect,hiveconf)
    spark.stop()
  }



  def changecolnums(args:Array[String],hiveconf:Array[String],resourcesql:DataFrame) ={
    var finallyresult:Dataset[Row] = null // 最终结果集
    var frame:DataFrame = null // 中间变量
    var hiveconclumns = spark.table(args(6)).columns // hive的列数
    hiveconclumns.foreach(println((_))) // 验证hive的列数
    var mysqlconnect:DataFrame = resourcesql // 设置数据源的resource

    // 判断分区字段在不在jdbc的数据里，如果不在，则在jdbc的数据源中先添加上分区字段
    if (args.length > 7){
      if (!resourcesql.columns.contains(args(7))){
        mysqlconnect = resourcesql.withColumn(args(7),lit(args(8)))
      }
    }

    val jdbcconclumns = mysqlconnect.columns // jdbc的列数


    var jdbcoldsource:Dataset[Row] = null // 源数据库的数据 checkpoint是为了破坏数据均衡，以后能编写变读取

    if (args.length == 10){
      jdbcoldsource = spark.sql(
        s"""
          |select * from ${hiveconf(1)} where ${hiveconf(2)} != ${hiveconf(3)}
          |""".stripMargin).checkpoint()
    }else{
      jdbcoldsource =  spark.sql(
        s"""
           |select * from ${hiveconf(1)}
           |""".stripMargin).checkpoint()
    }

    var existcolunms: Array[String] = null  // 设置hive或者mysql的额外列
    var resultdf: DataFrame = jdbcoldsource // 获取hive的数据原始数据

    // 判断是hive的列多，还是数据源的列数多
    if (hiveconclumns.length >= jdbcconclumns.length){
      // 判断额外列的存在
      existcolunms= hiveconclumns.filter(hivecol => {
        val bool = jdbcconclumns.map(jdbccol => {
          jdbccol == hivecol
        }).contains(true)
        !bool
      })
      // 判断两个列数是不是相等
        if (existcolunms.isEmpty) {
          frame = mysqlconnect.selectExpr(hiveconclumns: _*)
          frame
        }else{
        // 列数不相等的时候让列数少的加列
        resultdf = mysqlconnect
        for (elem <- existcolunms){
          resultdf = resultdf.withColumn(elem, lit(null))
        }
        // 对字段进行排序 ， 让分区数据的分区字段在最后一列
        frame = resultdf.selectExpr(hiveconclumns: _*)
        // 验证数据
        frame.show()
        // 整合历史数据
        finallyresult = jdbcoldsource.union(frame)
        // 验证数据
        finallyresult.show()
        changecolunm = true
        finallyresult
      }
    }else{
      // 数据的列多
      existcolunms= jdbcconclumns.filter(jdbccol => {
        val bool = hiveconclumns.map(hivecol => {
          jdbccol == hivecol
        }).contains(true)
        !bool
      })

      if (existcolunms.isEmpty) {
        frame = mysqlconnect.selectExpr(hiveconclumns: _*)
        frame
      }else{
        for (elem <- existcolunms){
          resultdf = resultdf.withColumn(elem, lit(null))
        }
        frame = resultdf.selectExpr(jdbcconclumns: _*)
        finallyresult = resultdf.union(mysqlconnect)
        changecolunm = true
        finallyresult
      }
    }
  }






  def jdbctohive(args:Array[String],catalog: Catalog,mysqlconnect: DataFrame, hiveconf: Array[String])={
    // 分割字符串获取hive的 表和数据库
    val strings = hiveconf(1).split("\\.")

// catalog的方法 获取表存不存在的方法
//    catalog.listTables(strings(0)).show()
//    val empty = catalog.listTables(strings(0)).filter(x => {
//      x.name == strings(1)
//    }).isEmpty
    val empty = catalog.tableExists(strings(0),strings(1))
//-----------------------------------------------------------------------------
//    sql的方法
//    val empty1 = spark.sql(
//      """
//        |show tables in hivedb
//        |""".stripMargin).filter("tableName = 'hivetablename'").isEmpty
// --------------------------------------------------------------------------


    // 判断列数是不是相等
    var frameresult:DataFrame = null
    // 先判断表存不存在 ，因为判断列数的方法要求表存在
      empty match {
          // 表不存在
      case false => {
        // 判断输入的变量个数执行 判断分区表还是普通表
        if (args.length > 7) {
          println("-----------------分区表")
          // 判断分区的参数在不在列中 如果不在 ，则加上 ，在的话就自动往下走
          if (!mysqlconnect.columns.contains(args(7))){
            frameresult = mysqlconnect.withColumn(args(7),lit(args(8)))
            frameresult.show()
          }
        }else{
          println("-----------普通表")
          frameresult = mysqlconnect
          mysqlconnect.show()
        }
      }

      case true => {
        // 表存在
        // 判断是不是分区表
        frameresult = changecolnums(args, hiveconf, mysqlconnect)
//        if (args.length > 7) {
//          println("-----------------分区表")
//          if (!mysqlconnect.columns.contains(args(7))){
//            frameresult = changecolnums(args, hiveconf, mysqlconnect)
//          }
//        }else{
//          println("-----------普通表")
//          frameresult = mysqlconnect
//        }
        frameresult.show()}
    }









    spark.conf.set("hive.exec.dynamic.partition.mode","nonstrict")
    spark.conf.set("hive.exec.dynamic.partition","true")
    spark.conf.set("spark.sql.parquet.writeLegacyFormat", "true")
    println(empty)
    hiveconf.foreach(println(_))
    saveFile.savetohiveapi(empty,frameresult,hiveconf,changecolunm)
}

}

```

# hivetojdbcs

```
package project

import java.util

import org.apache.spark.sql.SparkSession
import org.apache.spark.sql.catalog.Catalog
import project.jdbctohive.spark
import tool.{getmysqldf, savefile, sqlUtils,readfile}

object hivetojdbc {
  val spark = SparkSession.builder().appName("sqoop").master("local[4]").enableHiveSupport().getOrCreate()
  val getmysqldf = new readfile
  val sqlUtils = new sqlUtils
  val saveFile = new savefile
  private val catalog: Catalog = spark.catalog

  def main(args: Array[String]): Unit = {

    if (args.length==0){
      println(
        """
          |欢迎使用本程序
          |参数说明
          |总体参数种类 hive mysql
          |---------------------------hive
          |hive中要选择的字段 例子 ： "sal,big  / *  "
          |hive的table的名字 例子 ： bigdata_hive3.emp
          |hive中的 条件可以为空 例子 ： where sal > '300'
          |---------------------------mysql
          |savemode overwrite append 等
          |url 例子 ： jdbc:mysql://bigdata2:3306/try
          |user 例子 ： root
          |password 例子 ： liuzihan010616
          |dbtable 例子 ： emp
          |幂等性的列 ： 例子 ： sal
          |驱动名称 ： 例子 com.mysql.jdbc.Driver
          |""".stripMargin)
    }
    val frame = sqlUtils.checksql(spark, sqlUtils.hivesqlchoose(args))

      var mysqlconf = new Array[String](args.length-3)
      mysqlconf = util.Arrays.copyOfRange(args, 2, args.length)
      saveFile.savetojdbc(spark,frame,mysqlconf)


  }

}

```
