# paddle-ocr-with-docker
基于paddle推理模型的docker文字识别服务端，绝赞开发中

## QuickStart

> 因家境贫寒，电脑没GPU，故以CPU版示例

- 克隆本项目

```shell
git clone https://github.com/Sknp1006/paddle-ocr-with-docker.git
cd paddle-ocr-with-docker
git submodule add https://github.com/Sknp1006/PaddleOCR

# 安装python环境（需要有pipenv模块）
pipenv install
```

- 下载模型

> PaddleOCR：[OCR模型列表（V2.1，2021年9月6日更新）](https://github.com/PaddlePaddle/PaddleOCR/blob/release/2.3/doc/doc_ch/models_list.md) 

```shell
# 在项目根目录新建models文件夹，将下载的模型解压缩到此处（以文件夹为单位）
mkdir models
```

- 运行一个简单的demo

```shell
# 进入python环境
pipenv shell
cd PaddleOCR

# 运行文字检测模型
python tools/infer/predict_det.py --image_dir="../example/image/example.jpg" --det_model_dir="../models/ch_ppocr_server_v2.0_det_infer/" --use_gpu=False

# 运行文字识别模型
python tools/infer/predict_rec.py --image_dir="../example/image/example.jpg" --rec_model_dir="../models/ch_ppocr_server_v2.0_rec_infer/" --use_gpu=False

# 使用方向分类器
python tools/infer/predict_system.py --image_dir="../example/image/example.jpg" --det_model_dir="../models/ch_ppocr_server_v2.0_det_infer/" --cls_model_dir="../models/ch_ppocr_mobile_v2.0_cls_infer/" --rec_model_dir="../models/ch_ppocr_server_v2.0_rec_infer/" --use_angle_cls=true --use_gpu=False

# 使用多进程
python tools/infer/predict_system.py --image_dir="../example/image/example.jpg" --det_model_dir="../models/ch_ppocr_server_v2.0_det_infer/" --cls_model_dir="../models/ch_ppocr_mobile_v2.0_cls_infer/" --rec_model_dir="../models/ch_ppocr_server_v2.0_rec_infer/" --use_angle_cls=true --use_gpu=False --use_mp=True --total_process_num=6
```

