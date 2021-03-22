<!--
 * @Author: your name
 * @Date: 2020-12-24 10:00:00
 * @LastEditTime: 2021-01-28 10:50:36
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /learn/笔记/工具使用经验总结/tensorflow/tf-serving.md
-->

## GPU
1. 设置`--runtime=nvidia`
2. 选择GPU镜像`ai-image.jdcloud.com/neuhub-pai/tfserving:1.11.0-gpu`
docker run -t --rm \
    --runtime=nvidia \    
    -p 8501:8501 \
    -v /root/liulingzhi/ssd_saved_model/:/models/ssd_saved_model/ \
    -e MODEL_NAME=ssd_saved_model \
    ai-image.jdcloud.com/neuhub-pai/tfserving:1.11.0 &
## CPU
1. 选择CPU镜像`ai-image.jdcloud.com/neuhub-pai/tfserving:1.11.0`
docker run -t --rm \
    -p 8501:8501 \
    -v /root/liulingzhi/ssd_saved_model:/models/ssd_saved_model/ \
    -e MODEL_NAME=ssd_saved_model \
    ai-image.jdcloud.com/neuhub-pai/tfserving:1.9.0 &



docker run -t --rm \
    -p 8501:8501 \
    -v /Users/liulingzhi5/Desktop/learn/java/SparrowRecSys-master/savedmodel:/models/ssd_saved_model/ \
    -e MODEL_NAME=ssd_saved_model \
    tensorflow/servin2.1.0

## 检查服务状态
http://localhost:8501/v1/models/${MODEL_NAME}
http://localhost:8501/v1/models/${MODEL_NAME}/metadata


docker run -t  \
    -p 8501:8501 \
    -v /root/liulingzhi/ssd_saved_model/:/models/ssd_saved_model/ \
    -e MODEL_NAME=ssd_saved_model \
    ai-image.jdcloud.com/neuhub-pai/tfserving:1.11.0 &

docker run -t \
    -p 8501:8501 \
    -v /Users/liulingzhi5/Desktop/project/jd/服务发布/目标检测/:/models/ssd_saved_model/ \
    -e MODEL_NAME=ssd_saved_model \
    tensorflow/serving &

docker run -t --rm \
    -p 8501:8501 \
    -v /root/liulingzhi/ssd_saved_model/:/models/ssd_saved_model/ \
    -e MODEL_NAME=ssd_saved_model \
    ai-image.jdcloud.com/neuhub-pai/tfserving:1.11.0 &



curl -d '{"instances": [[[2, 5, 1], [4, 1, 2]], [[2, 8, 1], [6, 4, 5]]]}' \
    -X POST http://localhost:8501/v1/models/ssd_saved_model:predict

curl -d '{"instances": [[[2, 5, 1], [4, 1, 2]], [[2, 8, 1], [6, 4, 5]]]}' \
    -X POST http://localhost:8500/v1/models/model:predict
curl -d '{"instances": [[[2, 5, 1], [4, 1, 2]], [[2, 8, 1], [6, 4, 5]]]}' \
    -X POST 192.168.228.239:8500/v1/models/model:predict

curl -d '{"instances": [[[[2, 5, 1], [4, 1, 2]], [[2, 8, 1], [6, 4, 5]]]]}' \
    -X POST 192.168.228.219:8501/v1/models/model:predict

curl -d '{"instances": [[[[2, 5, 1], [4, 1, 2]], [[2, 8, 1], [6, 4, 5]]]]}' \
    -X POST 192.168.228.247:8501/v1/models/model:predict

curl -d '{"instances": ["0"]}' -X POST 192.168.228.247:8501/v1/models/model:predict






curl -d '{"instances": [[[[2, 5, 1], [4, 1, 2]], [[2, 8, 1], [6, 4, 5]]]]}' \
    -X POST 192.168.228.240:8501/v1/models/model:predict


curl -d '{"inputs": [{"b64":"eyJpbnB1dF9pZHMiOiBbMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMF0sICJpbnB1dF9tYXNrIjogWzAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDAsIDBdLCAibGFiZWxfaWRzIjogWzBdLCAiaXNfcmVhbF9leGFtcGxlIjogWzFdLCAic2VnbWVudF9pZHMiOiBbMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMCwgMF19"}]}' -X POST 192.168.228.221:8501/v1/models/model:predict


<!-- 对example进行cure需要手动base64编码 -->
curl -d '{"inputs": [{"input":{"input_ids":[0,0,0],"input_mask":[0,0,0],"label_ids":[0],"is_real_example":[1],"segment_ids":[0,0,0]}}]}' -X POST 192.168.228.221:8501/v1/models/model:predict

{
    "inputs": [
        "input_example_string:0":{
            "input":{
                "input_ids":[0,0,0],
                "input_mask":[0,0,0],
                "label_ids":[0],
                "is_real_example":[1],
                "segment_ids":[0,0,0]
            }
        }
    ]
}



curl -d '{"instances": [{"input":{"input_ids":[0,0,0],"input_mask":[0,0,0],"label_ids":[0],"is_real_example":[1],"segment_ids":[0,0,0]}}]}' -X POST 192.168.228.247:8501/v1/models/model:predict

curl -d '{"instances": [{"input_ids":[0,0,0],"input_mask":[0,0,0],"label_ids":[0],"is_real_example":[1],"segment_ids":[0,0,0]}]}' -X POST 192.168.228.247:8501/v1/models/model:predict




curl -d '{"instances": [{"input":"0"}]}' -X POST 192.168.228.247:8501/v1/models/model:predict


<!-- curl -d '{"image_tensor:0": [[[2, 5, 1], [4, 1, 2]], [[2, 8, 1], [6, 4, 5]]]}' \
    -X POST http://localhost:8502/v1/models/ssd_saved_model:serving_default -->


saved_model_cli run --dir ./ --tag_set serve --signature_def serving_default --input_expr 'inputs=[np.zeros((2,2,3), dtype=int)]'


docker run -t --rm -p 8501:8501 \
    -v $TESTDATA/saved_model_half_plus_two_cpu:/models/half_plus_two \
    -e MODEL_NAME=half_plus_two \
    tensorflow/serving &



# k8s
## 通过yaml文件起pod
1. 把配置存在ConfigMap（也是一个Yaml文件）里面，起ConfigMap
`kubectl create -f ***.yaml`
1. 把起容器的命令写在Yaml里
`docker create -f ***.yaml`
## 查看pod
1. 查看所有pod
`kubectl get pods --all-namespaces | grep liulingzhi`
2. 查看指定namespace下pod（namespace是什么可以在yaml文件中查看）
`kubectl get pods --namespace quickai | grep liulingzhi`
`kubectl describe pod --namespace quickai liulingzhi-tfserving`
3. 查看指定namespace下configmap
`kubectl get configmaps --namespace quickai | grep liulingzhi `
`kubectl describe configmaps --namespace quickai liulingzhi-tfserving`
 
## 进入pod进行校验
`kubectl exec -it liulingzhi-tfserving --namespace quickai -- /bin/bash`

## 访问服务
1. 获取容器IP
`kubectl get pods -n quickai  -o wide`


## 结束pod
1. deployment才是本体，删除本体后才会结束服务，否则会根据设置重启很多歌副本，这也是pod后缀的原因
`kubectl delete deployment -n quickai ****`
2. 删除pod
`kubectl delete pods -n quickai ****`

