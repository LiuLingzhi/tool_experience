<!--
 * @Author: your name
 * @Date: 2020-08-11 18:10:23
 * @LastEditTime: 2020-08-11 18:33:02
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /baojie_userportrait/bdp平台使用经验/spark_submit.md
-->
nohup \
spark-submit \
--master yarn \
--deploy-mode client \
--name $1_$2_$3 \
--num-executors 60 \
--executor-cores 4 \
--executor-memory 7g \
--driver-memory 8g \
--conf spark.shuffle.service.enabled=true \
--conf spark.debug.maxToStringFields=1000 \
--conf spark.sql.hive.filesourcePartitionFileCacheSize=524288000 \
--conf spark.driver.maxResultSize=8G \
--conf spark.sql.shuffle.partitions=1600 \
--conf spark.network.timeout=1200s \
--conf spark.default.parallelism=1600 \
--conf spark.shuffle.io.maxRetries=12 \
--conf spark.sql.broadcastTimeout=-1 \
--queue bdp_jmart_ai_union.bdp_jmart_ai_aipd \
--py-files model.zip \
click_stat.py \
--start_date $2 \
--end_date $3 \
--cross_obj cate1 \
>log/$1_$2_$3.log 2>&1 &



nohup sh gen_click_stat.sh cate2 2020-01-01 2020-02-01 >log/pid.log 2>&1 &