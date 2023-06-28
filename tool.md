---
title: tool
date: 1-16 17:00
categories: 日志
comments: "true"
tag: tool
---
这里放的都是我自己写的各种简单的应用包

# spark

## tool.readfiles

```
package tool

import org.apache.spark.sql.{DataFrame, SparkSession}
import com.crealytics.spark.excel._
import org.apache.spark.sql
class readfile {

  def getmysqldataframe(sparkSession: SparkSession,string: String*) ={

    val sql = string(3)
    val frame: DataFrame = sparkSession.read.format("jdbc").options(Map("url" -> string(0), "user" -> string(1), "password" -> string(2), "dbtable" -> s"($sql) as tmp","driver"->string(4))).load()
    frame
    //    if (string(3).contains(" ")) {
    //      val sql = string(3)
    //      val frame = sparkSession.read.format("jdbc").options(Map("url" -> string(0), "user" -> string(1), "password" -> string(2), "dbtable" -> s"$sql")).load()
    //      frame
    //    }else {
    //      val frame = sparkSession.read.format("jdbc").options(Map("url" -> string(0), "user" -> string(1), "password" -> string(2), "dbtable" -> string(3))).load()
    //      frame
    //    }
  }

  def getmyqsldffromMap(sparkSession: SparkSession,map: Map[String,String])={
    sparkSession.read.format("jdbc").options(map).load()
  }

  def readexcel(sparkSession: SparkSession,string: String*)={
    sparkSession.read.excel(header = string(0).toBoolean,inferSchema = string(1).toBoolean).load(string(3))
  }

  def readxxxfile(sparkSession: SparkSession,string: String*) ={
    sparkSession.read.format(string(0)).load(string(1))
  }

}

```

## tool.savefile

```
package tool

import org.apache.spark.sql.{DataFrame, DataFrameWriter, SparkSession}
import com.crealytics.spark.excel._
import org.apache.poi.ss.formula.functions.T
import tool.readfile
import tool.getjdbcconnect
class savefile(){
  private val jdbcconnect = new getjdbcconnect

  private val mysqldf = new getmysqldf
  def saveexcel(spark: DataFrame, string: Array[String]) = {
    spark.write.mode(string(2)).excel(header = string(4).toBoolean,string(5),string(6),string(7),string(8)).save(string(3))
  }




  def savetojdbc(spark: SparkSession,df: DataFrame, url:String,user:String,password:String,dbtable:String,driver:String,mideng:String,mode:String)={
    val map = Map("url" -> url,
      "user" -> user,
      "password" -> password,
      "dbtable" -> dbtable,
      "driver"-> driver)

//    df.write.mode(string(2)).format(string(1)).options(map).save()
    // -------------------------------------幂等性
    val connection = jdbcconnect.getconncet(driver,url,user,password)
    try{
      val bool = connection.createStatement().executeQuery(s"show tables like '${dbtable}'").next()
      if (!bool){
        throw new NullPointerException(s"写入的结果表${dbtable} 尚未创建！！！")
      }else{
        var flag:Any = null
        val flagbool = mysqldf.getmyqsldffromMap(spark, map).select(mideng).isEmpty
        if (!flagbool){
         flag = mysqldf.getmyqsldffromMap(spark, map).select(mideng).tail(1)(0)(0)
        }
        val tmpresult = df.select(mideng).filter(line => {
          line.getString(0) != flag
        })

        if (df.isEmpty){
          println("数据集为空")
        }else{
          if (tmpresult.isEmpty){
            println("你的数据已经插入过")
            df.show(false)
          }else {
//            df.show()
//            println(df.count())
            //val insertresult = tmpresult.join(df, string(5))
            tmpresult.show()
            println(tmpresult.count())
//            insertresult.show()
//            println(insertresult.count())
            tmpresult.write.mode(mode).format("jdbc").options(map).save()
          }
        }
      }
    }finally {
      connection.close()
    }


  }


  def savetohiveapi(sparkSession: SparkSession,boolean: Boolean,spark: DataFrame,hivetable:String,mode:String,hivepartition:String,changecolnums:Boolean) = {




    if (!boolean){
      if (hivepartition != null){
        spark.write.partitionBy(hivepartition.split(","):_*).mode(mode).format("hive").saveAsTable(hivetable)
      }else {

        println(hivetable)
        println(hivepartition)
        spark.write.mode(mode).format("hive").saveAsTable(hivetable)
      }

    }else{
      changecolnums match {
        case true =>  {
          if (hivepartition != null){
            if(sparkSession.table(hivetable).columns.length != spark.columns.length){
             sparkSession.sql(
               s"""
                 |drop table ${hivetable}
                 |""".stripMargin)
            }
            spark.write.partitionBy(hivepartition.split(","):_*).mode(mode).format("hive").saveAsTable(hivetable)
          }else {
            spark.write.mode(mode).format("hive").saveAsTable(hivetable)
          }
        }
        case false => spark.write.mode(mode).format("hive").insertInto(hivetable)
      }

      spark.show()
      println(spark.count())

    }
  }

  def savetohivelocalsource ={

  }


  def saveastextOrcsvOrjson(spark: DataFrame, string: Array[String],map: Map[String,String])= {

      map.isEmpty match {
        case true => spark.write.mode(string(2)).format(string(1)).save(string(3))
        case false => spark.write.mode(string(2)).options(map).format(string(1)).save(string(3))
      }
  }

}

```

## tool.sqlUtils

```
package tool

import java.lang.System
import java.util

import org.apache.spark.sql.types.{ArrayType, BooleanType, ByteType, CharType, DataType, DateType, DecimalType, DoubleType, FloatType, IntegerType, LongType, MapType, ShortType, StringType, StructField, StructType, TimestampType}
import org.apache.spark.sql.{DataFrame, SparkSession}
import org.apache.spark.sql.functions._

class sqlUtils {
  def hivesqlchoose(hiveconclumns:String,hivetable:String,hiveoptions:String)={

    "select" + " " + hiveconclumns + " " + "from" + " " +  hiveconclumns + " " + hiveoptions
  }

  def descfunctionsql(string: String)={
    s"""
       |desc function extended $string
       |""".stripMargin
  }

  def checksql(spark:SparkSession, string: String)={
    spark.sql(string)
}

  def mkcreatesql(source:DataFrame,tablename:String,fileformat:String,otheroptions:String*) ={

    var arraycolunmstmp:Seq[StructField]= null
    var arrayconcloumnspartition:Seq[StructField] = null
    if (otheroptions.length > 1){
      arraycolunmstmp=  source.schema.filter(x=>{
        !otheroptions(1).split(",").contains(x.name)
      })
      arrayconcloumnspartition = source.schema.filter(x=>{
        otheroptions(1).split(",").contains(x.name)
      })
    }else{
      arraycolunmstmp = source.schema.filter(x=>{
        !otheroptions(0).split(",").contains(x.name)
      })
      arrayconcloumnspartition = source.schema.filter(x=>{
        otheroptions(0).split(",").contains(x.name)
      })
    }

    def schematohivedatatype(rraycolunmstmp:Seq[StructField]) ={
      rraycolunmstmp.map(total => {
        println(total.name)
        println(total.dataType)
        val resultString1 = total.dataType match {
          case StringType => "string"
          case BooleanType => "boolean"
          case TimestampType => "timestamp"
          case ByteType => "byte"
          case DoubleType => "Double"
          case FloatType => "Float"
          case IntegerType => "Int"
          case LongType => "bigint"
          case ShortType => "smallint"
          case DecimalType() => "decimal(38,2)"
          case DateType => "datetime"

          case ArrayType(BooleanType, true) => "array<boolean>"
          case ArrayType(IntegerType, true) => "array<int>"
          case ArrayType(TimestampType, true) => "array<timestamp>"
          case ArrayType(DoubleType, true) => "array<double>"
          case ArrayType(FloatType, true) => "array<float>"
          case ArrayType(LongType, true) => "array<bigint>"
          case ArrayType(ShortType, true) => "array<smallint>"
          case ArrayType(ByteType, true) => "array<byte>"
          case ArrayType(StringType, true) => "array<string>"
          case ArrayType(DateType, true) => "array<datetime>"

          case MapType(StringType, StringType, true) => "map<string,string>"
          case MapType(StringType, IntegerType, true) => "map<string,int>"
          case MapType(IntegerType, StringType, true) => "map<int,string>"
          case MapType(StringType, BooleanType, true) => "map<string,boolean>"
          case MapType(IntegerType, BooleanType, true) => "map<int,boolean>"


          case MapType(BooleanType, BooleanType, true) => "map<boolean,boolean>"
          case MapType(BooleanType, StringType, true) => "map<boolean,string>"
          case MapType(BooleanType, IntegerType, true) => "map<boolean,int>"
          case MapType(BooleanType, FloatType, true) => "map<boolean,float>"
          case MapType(BooleanType, LongType, true) => "map<boolean,bigint>"
          case MapType(BooleanType, ShortType, true) => "map<boolean,smallint>"
          case MapType(BooleanType, TimestampType, true) => "map<boolean,timestamp>"
          case MapType(BooleanType, ByteType, true) => "map<boolean,byte>"
          case MapType(BooleanType, DoubleType, true) => "map<boolean,double>"
          case MapType(BooleanType, DateType, true) => "map<boolean,datetime>"

          case MapType(BooleanType, ArrayType(BooleanType, true), true) => "map<boolean,array<boolean>"
          case MapType(BooleanType, ArrayType(TimestampType, true), true) => "map<boolean,array<timestamp>"
          case MapType(BooleanType, ArrayType(DoubleType, true), true) => "map<boolean,array<double>"
          case MapType(BooleanType, ArrayType(FloatType, true), true) => "map<boolean,array<Float>"
          case MapType(BooleanType, ArrayType(LongType, true), true) => "map<boolean,array<bigint>"
          case MapType(BooleanType, ArrayType(ShortType, true), true) => "map<boolean,array<smallint>"
          case MapType(BooleanType, ArrayType(ByteType, true), true) => "map<boolean,array<byte>"
          case MapType(BooleanType, ArrayType(StringType, true), true) => "map<boolean,array<string>"
          case MapType(BooleanType, ArrayType(IntegerType, true), true) => "map<boolean,array<int>"
          case MapType(BooleanType, DateType, true) => "map<boolean,array<datetime>"
          case MapType(BooleanType, StructType(fields: Array[StructField]), true) => s"map<boolean,struct<${fields}>>"


          case MapType(IntegerType, BooleanType, true) => "map<int,boolean>"
          case MapType(IntegerType, StringType, true) => "map<int,string>"
          case MapType(IntegerType, IntegerType, true) => "map<int,int>"
          case MapType(IntegerType, FloatType, true) => "map<int,float>"
          case MapType(IntegerType, LongType, true) => "map<int,bigint>"
          case MapType(IntegerType, ShortType, true) => "map<int,smallint>"
          case MapType(IntegerType, TimestampType, true) => "map<int,timestamp>"
          case MapType(IntegerType, ByteType, true) => "map<int,byte>"
          case MapType(IntegerType, DoubleType, true) => "map<int,double>"
          case MapType(IntegerType, DateType, true) => "map<int,datetime>"
          case MapType(IntegerType, ArrayType(BooleanType, true), true) => "map<int,array<boolean>"
          case MapType(IntegerType, ArrayType(TimestampType, true), true) => "map<int,array<timestamp>"
          case MapType(IntegerType, ArrayType(DoubleType, true), true) => "map<int,array<double>"
          case MapType(IntegerType, ArrayType(FloatType, true), true) => "map<int,array<Float>"
          case MapType(IntegerType, ArrayType(LongType, true), true) => "map<int,array<bigint>"
          case MapType(IntegerType, ArrayType(ShortType, true), true) => "map<int,array<smallint>"
          case MapType(IntegerType, ArrayType(ByteType, true), true) => "map<int,array<byte>"
          case MapType(IntegerType, ArrayType(StringType, true), true) => "map<int,array<string>"
          case MapType(IntegerType, ArrayType(IntegerType, true), true) => "map<int,array<int>"
          case MapType(IntegerType, ArrayType(DateType, true), true) => "map<int,array<datetime>"
          case MapType(IntegerType, StructType(fields: Array[StructField]), true) => s"map<int,struct<${fields}>>"


          case MapType(FloatType, BooleanType, true) => "map<float,boolean>"
          case MapType(FloatType, StringType, true) => "map<float,string>"
          case MapType(FloatType, IntegerType, true) => "map<float,int>"
          case MapType(FloatType, FloatType, true) => "map<float,float>"
          case MapType(FloatType, LongType, true) => "map<float,bigint>"
          case MapType(FloatType, ShortType, true) => "map<float,smallint>"
          case MapType(FloatType, TimestampType, true) => "map<float,timestamp>"
          case MapType(FloatType, ByteType, true) => "map<float,byte>"
          case MapType(FloatType, DoubleType, true) => "map<float,double>"
          case MapType(FloatType, DateType, true) => "map<float,datetime>"
          case MapType(FloatType, ArrayType(BooleanType, true), true) => "map<float,array<boolean>"
          case MapType(FloatType, ArrayType(TimestampType, true), true) => "map<float,array<timestamp>"
          case MapType(FloatType, ArrayType(DoubleType, true), true) => "map<float,array<double>"
          case MapType(FloatType, ArrayType(FloatType, true), true) => "map<float,array<Float>"
          case MapType(FloatType, ArrayType(LongType, true), true) => "map<float,array<bigint>"
          case MapType(FloatType, ArrayType(ShortType, true), true) => "map<float,array<smallint>"
          case MapType(FloatType, ArrayType(ByteType, true), true) => "map<float,array<byte>"
          case MapType(FloatType, ArrayType(StringType, true), true) => "map<float,array<string>"
          case MapType(FloatType, ArrayType(IntegerType, true), true) => "map<float,array<int>"
          case MapType(FloatType, ArrayType(DateType, true), true) => "map<float,array<datetime>"
          case MapType(FloatType, StructType(fields: Array[StructField]), true) => s"map<float,struct<${fields}>>"


          case MapType(TimestampType, BooleanType, true) => "map<timestamp,boolean>"
          case MapType(TimestampType, StringType, true) => "map<timestamp,string>"
          case MapType(TimestampType, IntegerType, true) => "map<timestamp,int>"
          case MapType(TimestampType, FloatType, true) => "map<timestamp,float>"
          case MapType(TimestampType, LongType, true) => "map<timestamp,bigint>"
          case MapType(TimestampType, ShortType, true) => "map<timestamp,smallint>"
          case MapType(TimestampType, TimestampType, true) => "map<timestamp,timestamp>"
          case MapType(TimestampType, ByteType, true) => "map<timestamp,byte>"
          case MapType(TimestampType, DoubleType, true) => "map<timestamp,double>"
          case MapType(TimestampType, DateType, true) => "map<timestamp,datetime>"
          case MapType(TimestampType, ArrayType(BooleanType, true), true) => "map<timestamp,array<boolean>"
          case MapType(TimestampType, ArrayType(TimestampType, true), true) => "map<timestamp,array<timestamp>"
          case MapType(TimestampType, ArrayType(DoubleType, true), true) => "map<timestamp,array<double>"
          case MapType(TimestampType, ArrayType(FloatType, true), true) => "map<timestamp,array<Float>"
          case MapType(TimestampType, ArrayType(LongType, true), true) => "map<timestamp,array<bigint>"
          case MapType(TimestampType, ArrayType(ShortType, true), true) => "map<timestamp,array<smallint>"
          case MapType(TimestampType, ArrayType(ByteType, true), true) => "map<timestamp,array<byte>"
          case MapType(TimestampType, ArrayType(StringType, true), true) => "map<timestamp,array<string>"
          case MapType(TimestampType, ArrayType(IntegerType, true), true) => "map<timestamp,array<int>"
          case MapType(TimestampType, ArrayType(DateType, true), true) => "map<timestamp,array<datetime>"
          case MapType(TimestampType, StructType(fields: Array[StructField]), true) => s"map<timestamp,struct<${fields}>>"

          case MapType(StringType, BooleanType, true) => "map<string,boolean>"
          case MapType(StringType, StringType, true) => "map<string,string>"
          case MapType(StringType, IntegerType, true) => "map<string,int>"
          case MapType(StringType, FloatType, true) => "map<string,float>"
          case MapType(StringType, LongType, true) => "map<string,bigint>"
          case MapType(StringType, ShortType, true) => "map<string,smallint>"
          case MapType(StringType, TimestampType, true) => "map<string,timestamp>"
          case MapType(StringType, ByteType, true) => "map<string,byte>"
          case MapType(StringType, DoubleType, true) => "map<string,double>"
          case MapType(StringType, DateType, true) => "map<string,datetime>"
          case MapType(StringType, ArrayType(BooleanType, true), true) => "map<string,array<boolean>"
          case MapType(StringType, ArrayType(TimestampType, true), true) => "map<string,array<timestamp>"
          case MapType(StringType, ArrayType(DoubleType, true), true) => "map<string,array<double>"
          case MapType(StringType, ArrayType(FloatType, true), true) => "map<string,array<Float>"
          case MapType(StringType, ArrayType(LongType, true), true) => "map<string,array<bigint>"
          case MapType(StringType, ArrayType(ShortType, true), true) => "map<string,array<smallint>"
          case MapType(StringType, ArrayType(ByteType, true), true) => "map<string,array<byte>"
          case MapType(StringType, ArrayType(StringType, true), true) => "map<string,array<string>"
          case MapType(StringType, ArrayType(IntegerType, true), true) => "map<string,array<int>"
          case MapType(StringType, ArrayType(DateType, true), true) => "map<string,array<datetime>"
          case MapType(StringType, StructType(fields: Array[StructField]), true) => s"map<string,struct<${fields}>>"

          case MapType(DoubleType, BooleanType, true) => "map<Double,boolean>"
          case MapType(DoubleType, StringType, true) => "map<Double,string>"
          case MapType(DoubleType, IntegerType, true) => "map<Double,int>"
          case MapType(DoubleType, FloatType, true) => "map<Double,float>"
          case MapType(DoubleType, LongType, true) => "map<Double,bigint>"
          case MapType(DoubleType, ShortType, true) => "map<Double,smallint>"
          case MapType(DoubleType, TimestampType, true) => "map<Double,timestamp>"
          case MapType(DoubleType, ByteType, true) => "map<Double,byte>"
          case MapType(DoubleType, DoubleType, true) => "map<Double,double>"
          case MapType(DoubleType, DateType, true) => "map<Double,datetime>"
          case MapType(DoubleType, ArrayType(BooleanType, true), true) => "map<Double,array<boolean>"
          case MapType(DoubleType, ArrayType(TimestampType, true), true) => "map<Double,array<timestamp>"
          case MapType(DoubleType, ArrayType(DoubleType, true), true) => "map<Double,array<double>"
          case MapType(DoubleType, ArrayType(FloatType, true), true) => "map<Double,array<Float>"
          case MapType(DoubleType, ArrayType(LongType, true), true) => "map<Double,array<bigint>"
          case MapType(DoubleType, ArrayType(ShortType, true), true) => "map<Double,array<smallint>"
          case MapType(DoubleType, ArrayType(ByteType, true), true) => "map<Double,array<byte>"
          case MapType(DoubleType, ArrayType(StringType, true), true) => "map<Double,array<string>"
          case MapType(DoubleType, ArrayType(IntegerType, true), true) => "map<Double,array<int>"
          case MapType(DoubleType, ArrayType(DateType, true), true) => "map<Double,array<datetime>"
          case MapType(DoubleType, StructType(fields: Array[StructField]), true) => s"map<Double,struct<${fields}>>"

          case MapType(LongType, BooleanType, true) => "map<bigint,boolean>"
          case MapType(LongType, StringType, true) => "map<bigint,string>"
          case MapType(LongType, IntegerType, true) => "map<bigint,int>"
          case MapType(LongType, FloatType, true) => "map<bigint,float>"
          case MapType(LongType, LongType, true) => "map<bigint,bigint>"
          case MapType(LongType, ShortType, true) => "map<bigint,smallint>"
          case MapType(LongType, TimestampType, true) => "map<bigint,timestamp>"
          case MapType(LongType, ByteType, true) => "map<bigint,byte>"
          case MapType(LongType, DoubleType, true) => "map<bigint,double>"
          case MapType(LongType, DateType, true) => "map<bigint,datetime>"
          case MapType(LongType, ArrayType(BooleanType, true), true) => "map<bigint,array<boolean>"
          case MapType(LongType, ArrayType(TimestampType, true), true) => "map<bigint,array<timestamp>"
          case MapType(LongType, ArrayType(DoubleType, true), true) => "map<bigint,array<double>"
          case MapType(LongType, ArrayType(FloatType, true), true) => "map<bigint,array<Float>"
          case MapType(LongType, ArrayType(LongType, true), true) => "map<bigint,array<bigint>"
          case MapType(LongType, ArrayType(ShortType, true), true) => "map<bigint,array<smallint>"
          case MapType(LongType, ArrayType(ByteType, true), true) => "map<bigint,array<byte>"
          case MapType(LongType, ArrayType(StringType, true), true) => "map<bigint,array<string>"
          case MapType(LongType, ArrayType(IntegerType, true), true) => "map<bigint,array<int>"
          case MapType(LongType, ArrayType(DateType, true), true) => "map<bigint,array<datetime>"
          case MapType(LongType, StructType(fields: Array[StructField]), true) => s"map<bigint,struct<${fields}>>"

          case MapType(ShortType, BooleanType, true) => "map<smallint,boolean>"
          case MapType(ShortType, StringType, true) => "map<smallint,string>"
          case MapType(ShortType, IntegerType, true) => "map<smallint,int>"
          case MapType(ShortType, FloatType, true) => "map<smallint,float>"
          case MapType(ShortType, LongType, true) => "map<smallint,bigint>"
          case MapType(ShortType, ShortType, true) => "map<smallint,smallint>"
          case MapType(ShortType, TimestampType, true) => "map<smallint,timestamp>"
          case MapType(ShortType, ByteType, true) => "map<smallint,byte>"
          case MapType(ShortType, DoubleType, true) => "map<smallint,double>"
          case MapType(ShortType, DateType, true) => "map<smallint,datetime>"
          case MapType(ShortType, ArrayType(BooleanType, true), true) => "map<smallint,array<boolean>"
          case MapType(ShortType, ArrayType(TimestampType, true), true) => "map<smallint,array<timestamp>"
          case MapType(ShortType, ArrayType(DoubleType, true), true) => "map<smallint,array<double>"
          case MapType(ShortType, ArrayType(FloatType, true), true) => "map<smallint,array<Float>"
          case MapType(ShortType, ArrayType(LongType, true), true) => "map<smallint,array<bigint>"
          case MapType(ShortType, ArrayType(ShortType, true), true) => "map<smallint,array<smallint>"
          case MapType(ShortType, ArrayType(ByteType, true), true) => "map<smallint,array<byte>"
          case MapType(ShortType, ArrayType(StringType, true), true) => "map<smallint,array<string>"
          case MapType(ShortType, ArrayType(IntegerType, true), true) => "map<smallint,array<int>"
          case MapType(ShortType, ArrayType(DateType, true), true) => "map<smallint,array<datetime>"
          case MapType(ShortType, StructType(fields: Array[StructField]), true) => s"map<smallint,struct<${fields}>>"

          case MapType(ByteType, BooleanType, true) => "map<byte,boolean>"
          case MapType(ByteType, StringType, true) => "map<byte,string>"
          case MapType(ByteType, IntegerType, true) => "map<byte,int>"
          case MapType(ByteType, FloatType, true) => "map<byte,float>"
          case MapType(ByteType, LongType, true) => "map<byte,bigint>"
          case MapType(ByteType, ShortType, true) => "map<byte,smallint>"
          case MapType(ByteType, TimestampType, true) => "map<byte,timestamp>"
          case MapType(ByteType, ByteType, true) => "map<byte,byte>"
          case MapType(ByteType, DoubleType, true) => "map<byte,double>"
          case MapType(ByteType, DateType, true) => "map<byte,datetime>"
          case MapType(ByteType, ArrayType(BooleanType, true), true) => "map<byte,array<boolean>"
          case MapType(ByteType, ArrayType(TimestampType, true), true) => "map<byte,array<timestamp>"
          case MapType(ByteType, ArrayType(DoubleType, true), true) => "map<byte,array<double>"
          case MapType(ByteType, ArrayType(FloatType, true), true) => "map<byte,array<Float>"
          case MapType(ByteType, ArrayType(LongType, true), true) => "map<byte,array<bigint>"
          case MapType(ByteType, ArrayType(ShortType, true), true) => "map<byte,array<smallint>"
          case MapType(ByteType, ArrayType(ByteType, true), true) => "map<byte,array<byte>"
          case MapType(ByteType, ArrayType(StringType, true), true) => "map<byte,array<string>"
          case MapType(ByteType, ArrayType(IntegerType, true), true) => "map<byte,array<int>"
          case MapType(ByteType, ArrayType(DateType, true), true) => "map<byte,array<datetime>"
          case MapType(ByteType, StructType(fields: Array[StructField]), true) => s"map<byte,struct<${fields}>>"

          case StructType(fields: Array[StructField]) => s"struct<${fields}>"
          case _ => println("未知的数据类型")
        }

        s"${total.name} ${resultString1}"
      }).toArray
    }

    val arraycolunms = schematohivedatatype(arraycolunmstmp)
    val partitionconclunms = schematohivedatatype(arrayconcloumnspartition)

    var fileformatted:String = null
    var partitionstring:String = null
    fileformat match {
      case "text" =>
        makepartitionsql(otheroptions)
        fileformatted = s"""
          |row format delimited fields terminated by ${otheroptions(0)}
          |stored as textfile
          |""".stripMargin
      case "orc" =>
        makepartitionsql(otheroptions)
        fileformatted ="""
          |stored as orc
          |""".stripMargin

      case "parquet" =>
        makepartitionsql(otheroptions)
        fileformatted ="""
          |stored as Parquet
          |""".stripMargin
      case "rcfile" =>
        makepartitionsql(otheroptions)
        fileformatted ="""
          |stored as rcfile
          |""".stripMargin
      case "sequencefile" =>
        makepartitionsql(otheroptions)
        fileformatted ="""
          |stored as sequencefile
          |""".stripMargin
      case _ => println("错误的存储格式")
    }


    def makepartitionsql(array: Seq[String]) ={

      array.foreach(println(_))
      println(array.length)

      if (otheroptions.isEmpty){
        print("---------------正在创建普通表")
      }else{
        partitionstring = fileformatted match {
          case text =>
            {
              val strings = array(1).split(",")
              var bool:Boolean  = false
              var tmppartString:String = null
              if (!partitionconclunms.isEmpty){
                tmppartString = partitionconclunms.mkString(",")
                if (otheroptions.length != 2){
                  tmppartString = tmppartString + ","
                }
              }
              for (elem <- strings){
                bool = source.columns.contains(elem)
                if (!bool){
                  if (elem == strings(strings.length-1)){
                    tmppartString = tmppartString + elem +" "+ "string"
                  }else{
                    tmppartString = tmppartString + elem +" "+ "string" + ","
                  }
                }
              }
              s"""
                |partitioned by (${tmppartString})
                |""".stripMargin
            }

          case _ =>
          {
            val strings = array(0).split(",")
            var bool:Boolean  = false
            var tmppartString:String = null
            if (!partitionconclunms.isEmpty){
              tmppartString = partitionconclunms.mkString(",")
              if (otheroptions.length != 2){
                tmppartString = tmppartString + ","
              }
            }
            for (elem <- strings){
              bool = source.columns.contains(elem)
              if (!bool){
                if (elem == strings(strings.length-1)){
                  tmppartString = tmppartString + elem +" "+ "string"
                }else{
                  tmppartString = tmppartString + elem +" "+ "string" + ","
                }
              }
            }
            s"""
               |partitioned by (${tmppartString})
               |""".stripMargin
          }
        }
      }
    }



    val createsql =
      s"""
        |create table
        |${tablename}
        |(${arraycolunms.mkString(",\n")})${partitionstring}${fileformatted}
        |""".stripMargin
    println(createsql)
    createsql
  }

  def insertmake(sparkSession: SparkSession,dataFrame: DataFrame,tablename:String,otheroptions:String*) ={

    var strings:Array[String] = null

      dataFrame.selectExpr(sparkSession.table(tablename).columns:_*).createOrReplaceTempView("tmp")

   // val partitionstring = sparkSession.table(tablename).columns.tail(sparkSession.table(tablename).columns.length - 2)
    otheroptions.length match {
      case 0 => {
        sparkSession.sql(
          s"""
             |insert overwrite ${tablename}
             |select * from tmp
             |""".stripMargin)
      }
      case _ => {

        if (otheroptions.length > 1){
          strings = otheroptions(1).split(",").filter(conclunms => {
            !dataFrame.columns.contains(conclunms)
          })
          val fuzhiarray:Array[String] = util.Arrays.copyOfRange(otheroptions.toArray, 2, otheroptions.length)
          fuzhiarray.foreach(println(_))
          strings.isEmpty match {
            case true => {

          sparkSession.sql(
          s"""
            |insert overwrite ${tablename} partition(${otheroptions(1).split(",").map(conclunms => {s"${conclunms}"}).mkString(",")})
            |select * from tmp
            |""".stripMargin)
          }
            case false => {
              var tmpdf:DataFrame = dataFrame
              for (i <- 0 to strings.length-1){
                tmpdf = tmpdf.withColumn(strings(i),lit(fuzhiarray(i)))
              }
              tmpdf.show()
              tmpdf.printSchema()
              tmpdf = tmpdf.selectExpr(sparkSession.table(tablename).columns: _*)
              tmpdf.show()
              tmpdf.printSchema()
              val str = tmpdf.columns.mkString(",\n")
              tmpdf.createOrReplaceTempView("smp")
              sparkSession.sql(
                s"""
                   |insert overwrite ${tablename} partition(${otheroptions(1).split(",").map(conclunms => {s"${conclunms}"}).mkString(",")})
                   |select ${str} from smp
                   |""".stripMargin)
            }
          }



        }else{
          strings = otheroptions(0).split(",").filter(conclunms => {
            !dataFrame.columns.contains(conclunms)
          })
          val fuzhiarray:Array[String] = util.Arrays.copyOfRange(otheroptions.toArray, 1, otheroptions.length)

          strings.isEmpty match {
            case true => {
              sparkSession.sql(
                s"""
                   |insert overwrite ${tablename} partition(${otheroptions(0).split(",").map(conclunms => {s"${conclunms}"}).mkString(",")})
                   |select * from tmp
                   |""".stripMargin)
            }

            case false => {
              var tmpdf:DataFrame = dataFrame

              for (i <- 0 to strings.length-1){
                tmpdf = tmpdf.withColumn(strings(i),lit(fuzhiarray(i)))
              }
              tmpdf.show()
              tmpdf.printSchema()
              tmpdf = tmpdf.selectExpr(sparkSession.table(tablename).columns: _*)
              val str = tmpdf.columns.mkString(",\n")
              tmpdf.createOrReplaceTempView("smp")
              sparkSession.sql(
                s"""
                   |insert overwrite ${tablename} partition(${otheroptions(0).split(",").map(conclunms => {s"${conclunms}"}).mkString(",")})
                   |select ${str} from smp
                   |""".stripMargin)
            }
        }
      }
    }
  }
  }

  def maskmake(sparkSession: SparkSession,tablename:String)={
    sparkSession.sql(
      s"""
        |msck repair table ${tablename}
        |""".stripMargin)
  }

  def changecolunms(sparkSession: SparkSession , dataFrame: DataFrame,tablename:String) ={

    val strings = dataFrame.columns.filter(!sparkSession.table(tablename).columns.contains(_))
    println(strings)
    strings.foreach(println(_))

    val str = strings.map(x => {
      s"${x} string"
    }).mkString(",")

    val changesonslunms =
      s"""
        |alter table ${tablename} add COLUMNS (${str})
        |""".stripMargin
    println(changesonslunms)
    sparkSession.sql(changesonslunms)
  }

}

```

## tool.mysqlutils

```
package tool

import java.sql.{Connection, DriverManager}

class mysqlutils {

  def getconnect(string: String*)={
    var connect=null
    try{
      Class.forName("com.mysql.jdbc.Driver")
      try {
        DriverManager.getConnection(string(0),string(1),string(2))
      }
    }
  }
  def closeconnect(connect:Connection) ={
    if (connect != null){
      connect.close()
    }
  }
}

```

## tool.getmysqldf

```
package tool

import org.apache.spark.sql.SparkSession
import tool._
class getmysqldf {


  def getmysqldataframe(sparkSession: SparkSession,string: String*) ={

      val sql = string(3)
      val frame = sparkSession.read.format("jdbc").options(Map("url" -> string(0), "user" -> string(1), "password" -> string(2), "dbtable" -> s"$sql","driver"->string(4))).load()
      frame
//    if (string(3).contains(" ")) {
//      val sql = string(3)
//      val frame = sparkSession.read.format("jdbc").options(Map("url" -> string(0), "user" -> string(1), "password" -> string(2), "dbtable" -> s"$sql")).load()
//      frame
//    }else {
//      val frame = sparkSession.read.format("jdbc").options(Map("url" -> string(0), "user" -> string(1), "password" -> string(2), "dbtable" -> string(3))).load()
//      frame
//    }
  }

  def getmyqsldffromMap(sparkSession: SparkSession,map: Map[String,String])={



    sparkSession.read.format("jdbc").options(map).load()

  }

}

```

## tool.getjdbcconnect

```
package tool

import java.sql.{Connection, DriverManager}

class getjdbcconnect {

  def getconncet(drivername:String,url:String,user:String,password:String)={
    Class.forName(drivername)
    val connection = DriverManager.getConnection(url, user, password)
    connection
  }

}

```

## tool.streamingcontext

```
package tool

import org.apache.spark.SparkConf
import org.apache.spark.streaming.{Seconds, StreamingContext}

class streamingcontext {

  def getstreamcotext(mode: String="local[4]",name:String="example",checkpoint:String="file:///D:\\checkpoint")={
    val conf = new SparkConf().setMaster(mode).setAppName(name)
    val ssc = new StreamingContext(conf, Seconds(5))
    ssc.checkpoint(checkpoint)
    ssc
  }
}

```
