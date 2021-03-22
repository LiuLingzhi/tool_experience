<!--
 * @Author: your name
 * @Date: 2021-02-24 14:55:38
 * @LastEditTime: 2021-02-24 15:42:10
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /learn/笔记/工具使用经验总结/spark/xgb.md
-->
https://github.com/303844828/PyXGBoost/releases/tag/1.0

spark-submit --master yarn-cluster --num-executors 100 \
--jars pyspark-xgboost-1.0.jar  \
--py-files pyspark-xgboost-1.0.jar \
--files test.py  


```
###############################################################################
# import依赖包
###############################################################################
import time
import os    # 必需的包，勿删除
import sys   # 必需的包，勿删除
import glob  # 必需的包，勿删除
# from kuai.reader import hive2file, hive2dir
import uuid
import pandas as pd
import numpy as np
import pickle
import datetime
from numpy import allclose
 
from pyspark.conf import SparkConf
from pyspark.context import SparkContext
from pyspark.sql import SparkSession
from pyspark.sql import HiveContext
from pyspark.ml import Pipeline, PipelineModel
from pyspark.ml.classification import DecisionTreeClassifier, DecisionTreeModel, DecisionTreeClassificationModel
from pyspark.ml.evaluation import MulticlassClassificationEvaluator
from pyspark.ml.feature import VectorAssembler
from pyspark.sql.functions import col,udf
from pyspark.ml.classification import GBTClassifier
from pyspark.ml.feature import StringIndexer
from pyspark.sql.types import *
from pyspark.ml.classification import RandomForestClassifier
import pyspark.sql.functions as fn
from pyspark.sql import Row
from pyspark.sql.types import IntegerType
from pyspark.sql.types import DoubleType
 
os.environ['PYSPARK_SUBMIT_ARGS'] = '--jars xgboost4j-spark-0.72.jar,xgboost4j-0.72.jar pyspark-shell'
import findspark
findspark.init()
 
 
 
 
 
 
 
 
sparkConf = SparkConf()
# 设置Driver进程的内存
sparkConf.set('spark.driver.memory', '32G')
# 设置Driver的CPU core数量
sparkConf.set('spark.driver.cores', '16')
# 设置Spark作业总共要用多少个Executor进程来执行
sparkConf.set("spark.executor.instances", "8")
# 设置每个Executor进程的CPU core数量
sparkConf.set("spark.executor.cores", "12")
# 设置每个Executor进程的内存
sparkConf.set("spark.executor.memory", "32G")
# 设置Spark应用的名称
sparkConf.set("spark.app.name", "pyspark-big-feature-xgboost")
# 设置Executor进程的CPU core数量
# 注意：请确保"spark.kubernetes.executor.limit.cores"参数值 >= "spark.executor.cores"参数值，否者spark executor启动不起来
sparkConf.set("spark.kubernetes.executor.limit.cores", "12")
sparkConf.set("spark.debug.maxToStringFields","100")
 
 
 
 
print("start:")
spark_conf = SparkSession.builder.config(conf=sparkConf).enableHiveSupport().getOrCreate()
sc = spark_conf.sparkContext
spark = HiveContext(sc)
 
 
sc.addPyFile("sparkxgb.zip")
 
 
print("start:.....sparksql...")
 
spark.sql('use dmc_dev')
data_df = spark.sql("select * from (\
(select * from dmc_bc.dmcbc_ust_dmc_bc_app_jr_ic_531_recmd_realtm_pre_train_data_i_d where dt='2019-10-10' and label = 0 distribute by rand() sort by rand() limit 1500) union all \
(select * from dmc_bc.dmcbc_ust_dmc_bc_app_jr_ic_531_recmd_realtm_pre_train_data_i_d where dt='2019-10-10' and label = 1 distribute by rand() sort by rand() limit 1500))t")
df_columns = data_df.columns
data_df=data_df.select(*[fn.col('pin'),fn.col('title')]+[fn.col(c).cast("float") for c in ['label']+data_df.columns[3:]])
data_df=data_df.fillna(0.0)
 
 
feature_names= [x for x in data_df.columns if x not in ['pin','title','label','dt']]
vecAssembler = VectorAssembler(inputCols=feature_names, outputCol="features")
dataAssembler = vecAssembler.transform(data_df)
 
(trainData, testData) = dataAssembler.randomSplit([0.8, 0.2],seed=100)
 
 
from sparkxgb import XGBoostEstimator
xgboost = XGBoostEstimator(featuresCol="features", labelCol="label", predictionCol="prediction")
xgb_model = xgboost.fit(trainData)
```
