# 使用TF-Hub进行迁移学习（图像分类）

## 机器环境

* Win10
* Python3.6

## 安装依赖

```text
pip install tensorflow
pip install tensorflow-hub
```

## Github地址

* [https://github.com/ns2250225/tensorflow-hub-image-classify](https://github.com/ns2250225/tensorflow-hub-image-classify)

## 准备分类图片素材

* 每一类至少100张图片
* 图片分类好，放到photos文件夹里面

## 训练模型（默认用Inception V3）

* 先清空 output 文件夹
* 这里训练20步

```text
python .\retrain.py --image_dir .\photos\ --saved_model_dir .\model\ --bottleneck_dir .\bottleneck\ --how_many_training_steps 20 --output_labels .\output\output_labels.txt --output_graph .\output\retrain.pb 
```

## 使用模型

```text
python .\label_image.py --graph .\output\retrain.pb --labels .\output\output_labels.txt --input_layer=Placeholder --output_layer=final_result --image .\test.jpg
```

## 备注

* 使用其它模型来训练，比如 mobilenet\_v2\_100\_224

```text
# 在训练模型时加上：
–tfhub_module https://tfhub.dev/google/imagenet/mobilenet_v2_100_224/feature_vector/1
```

