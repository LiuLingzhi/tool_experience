<!--
 * @Author: your name
 * @Date: 2021-01-18 17:12:39
 * @LastEditTime: 2021-01-18 20:05:48
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /learn/笔记/工具使用经验总结/spark/spark生成tfrecord.md
-->
spark转https://cf.jd.com/pages/viewpage.action?pageId=235993258

在 https://mvnrepository.com/artifact/org.tensorflow/spark-tensorflow-connector 中下载对应版本的包，并在pyspark代码中通过
```
.config("spark.jars", "/home/liulingzhi1/ali_exp/spark-tensorflow-connector_2.11-1.15.0.jar")
```
引入

整体代码为。注意必须手动表明每个字段的数据类型，以便于转换正确的tfrecord格式
```
from pyspark.sql import SparkSession
import pickle

spark = (SparkSession
    .builder
    .appName("gps-test-DockerLinuxContainer")
    .enableHiveSupport()
    .config("spark.executor.instances", "50")
    .config("spark.executor.memory","4g")
    .config("spark.executor.cores","2")
    .config("spark.driver.memory","4g")
    .config("spark.sql.shuffle.partitions","500")
    .config("spark.jars", "/home/liulingzhi1/ali_exp/spark-tensorflow-connector_2.11-1.15.0.jar")
    .config("spark.yarn.appMasterEnv.yarn.nodemanager.container-executor.class","DockerLinuxContainer")
    .config("spark.executorEnv.yarn.nodemanager.container-executor.class","DockerLinuxContainer")
    .config("spark.yarn.appMasterEnv.yarn.nodemanager.docker-container-executor.image-name","bdp-docker.jd.com:5000/wise_mart_bag:latest")
    .config("spark.executorEnv.yarn.nodemanager.docker-container-executor.image-name","bdp-docker.jd.com:5000/wise_mart_bag:latest")
    .getOrCreate())


with open('dataset/merged.pkl', 'rb') as f:
    data = pickle.load(f)


schema = StructType(
    [StructField("label", StringType(), True)] + \
    [StructField("I%d"%i, IntegerType(), True) for i in range(1,14)] + \
    [StructField("C%d"%i, StringType(), True) for i in range(1,27)]
)



df = spark.createDataFrame(data,schema)

df.fillna(0, subset=dense_feature_names)
df.fillna('unk', subset=sparse_feature_names)

tf_output_dir = "liulingzhi1/ali/dataset/"
df.repartition(1000).write.mode("overwrite").format("tfrecords").option("recordType", "Example").save(tf_output_dir)
```
