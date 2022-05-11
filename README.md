# paddle-ocr-with-docker

## 环境说明

![image](https://user-images.githubusercontent.com/41496773/143843841-ea220aab-848f-48b3-bb01-69fd666fe666.png)
![lADPJxRxPVo435LNAVTNBDg_1080_340](https://user-images.githubusercontent.com/41496773/143843429-6ed55641-6c36-4334-9ccb-e975a7967a85.jpg)

> 因此强烈建议在linux环境下开发，或者通过本项目的 **Dockerfile** 构建镜像使用

## QuickStart

> 因家境贫寒，电脑没GPU，故以CPU版示例
> 此处运行环境为windows

### 通过命令行运行

- 克隆本项目

```shell
# 同时克隆子模块
git clone --recurse-submodules https://github.com/Sknp1006/paddle-ocr-with-docker

# 安装python环境（需要有pipenv模块）
# 注：colorama模块需要windows环境
pipenv install
```

- 下载模型

> PaddleOCR：[OCR模型列表（V2.1，2021年9月6日更新）](https://github.com/PaddlePaddle/PaddleOCR/blob/release/2.3/doc/doc_ch/models_list.md) 
>
> 项目用到的推理模型（后缀为infer）：
>
> - [ch_ppocr_server_v2.0_det_infer（文字检测模型）](https://paddleocr.bj.bcebos.com/dygraph_v2.0/ch/ch_ppocr_server_v2.0_det_infer.tar) 
> - [ch_ppocr_mobile_v2.0_cls_infer（方向分类器）](https://paddleocr.bj.bcebos.com/dygraph_v2.0/ch/ch_ppocr_mobile_v2.0_cls_infer.tar) 
> - [ch_ppocr_server_v2.0_rec_infer（文字识别模型）](https://paddleocr.bj.bcebos.com/dygraph_v2.0/ch/ch_ppocr_server_v2.0_rec_infer.tar) 

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

- 识别结果

![example](https://user-images.githubusercontent.com/41496773/143424123-c8f70f25-73bd-49a0-a017-3e2f959c02f2.jpg)

### 通过docker运行

```shell
cd example

# 构建docker
docker build --rm -t {image_name:tag} .

# 启动服务
docker run -p 8866:8866 --name {container_name} -d {image_name:tag}
```

> 初次启动服务会下载 `chinese_ocr_db_crnn_server` 推理模型

**调用服务**

```python
import base64
import json

import cv2
import requests


def cv2_to_base64(image):
    data = cv2.imencode('.jpg', image)[1]
    return base64.b64encode(data.tobytes()).decode('utf8')


def main():
    for i in range(1):
        # 发送HTTP请求
        data = {'images': [cv2_to_base64(cv2.imread("./image/example.jpg"))]}
        headers = {"Content-type": "application/json"}
        url = "http://127.0.0.1:8866/predict/chinese_ocr_db_crnn_server"
        r = requests.post(url=url, headers=headers, data=json.dumps(data))
        print(r.json())
```

**返回示例**

```json
{"msg": "", "results": [{"data": [{"confidence": 0.9911051392555237, "text": "又不是不能用", "text_box_position": [[138, 270], [389, 270], [389, 310], [138, 310]]}], "save_path": ""}], "status": "000"}
```

