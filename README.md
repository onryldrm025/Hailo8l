
# How to Setup Raspberry Pi 5 with Hailo8l AI Kit using yolov8n on Windows (WSL2 Ubuntu)

## WSL Ubuntu

### Get Guide
```bash
git clone https://github.com/BetaUtopia/Hailo8l.git
```

### Training
```bash
cd Hailo8l
sudo apt-get update
sudo apt-get install libpython3.11-stdlib libgl1-mesa-glx
sudo apt install python3.11 python3.11-venv
python3.11 -m venv venv_yolov8
source venv_yolov8/bin/activate
pip install ultralytics
```

```bash
cd model
```

```bash
yolo detect train data=data.yaml model=yolov8n.pt name=retrain_yolov8n project=./runs/detect epochs=1000 batch=16
```

### Convert to ONNX
```bash
cd runs/detect/retrain_yolov8n/weights   
```

```bash
yolo export model=./best.pt imgsz=640 format=onnx opset=11 
```

```bash
cd ~/Hailo8l && deactivate
```

### Install Hailo
```bash
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update
sudo apt-get install python3.8 python3.8-venv python3.8-dev
```

```bash
python3.8 -m venv venv_hailo
source venv_hailo/bin/activate
sudo apt-get update
sudo apt-get install build-essential python3-dev graphviz graphviz-dev python3-tk
pip install pygraphviz
```

```bash
pip install whl/hailo_dataflow_compiler-3.27.0-py3-none-linux_x86_64.whl
pip install whl/hailo_model_zoo-2.11.0-py3-none-any.whl
```

```bash
git clone https://github.com/hailo-ai/hailo_model_zoo.git
```

### Install Coco dataset
```bash
python hailo_model_zoo/hailo_model_zoo/datasets/create_coco_tfrecord.py val2017
python hailo_model_zoo/hailo_model_zoo/datasets/create_coco_tfrecord.py calib2017
```

```bash
cd model/runs/detect/retrain_yolov8n/weights
```

### Parse
```bash
hailomz parse --hw-arch hailo8l --ckpt ./best.onnx yolov8n
```

### Optimize
-Use your own username /home/USER

```bash
hailomz optimize --hw-arch hailo8l --har ./yolov8n.har \
    --calib-path /home/sam/.hailomz/data/models_files/coco/2023-08-03/coco_calib2017.tfrecord \
    --model-script /home/sam/Hailo8l/hailo_model_zoo/hailo_model_zoo/cfg/alls/generic/yolov8n.alls \
    yolov8n
```

### Compile
```bash
hailomz compile yolov8n --hw-arch hailo8l --har ./yolov8n.har
```

## Raspbery Pi 5

```bash
cd hailo8l
git clone https://github.com/hailo-ai/hailo-rpi5-examples.git
pip install setproctitle

```

```bash
cd hailo-rpi5-examples
source setup_env.sh
cd ..
```

```bash
python hailo-rpi5-examples/basic_pipelines/detection.py -i rpi --hef yolov8n.hef
```

# How to Setup Raspberry Pi 5 with Hailo8l AI Kit using yolov8n on Windows (WSL2 Ubuntu) Custom Objects and Labels

## WSL Ubuntu

### Get Guide
```bash
git clone https://github.com/BetaUtopia/Hailo8l.git
```

### Training
```bash
cd Hailo8l
sudo apt-get update
sudo apt-get install libpython3.11-stdlib libgl1-mesa-glx
sudo apt install python3.11 python3.11-venv
python3.11 -m venv venv_yolov8
source venv_yolov8/bin/activate
pip install ultralytics
```

```bash
cd model
```

```bash
yolo detect train data=config.yaml model=yolov8n.pt name=retrain_yolov8n project=./model/runs/detect epochs=1000 batch=16
```

### Convert to ONNX
```bash
cd model/runs/detect/retrain_yolov8n/weights   
```

```bash
yolo export model=./best.pt imgsz=640 format=onnx opset=11 
```

```bash
cd ~/Hailo8l && deactivate
```

### Install Hailo
```bash
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update
sudo apt-get install python3.8 python3.8-venv python3.8-dev
```

```bash
python3.8 -m venv venv_hailo
source venv_hailo/bin/activate
sudo apt-get update
sudo apt-get install build-essential python3-dev graphviz graphviz-dev python3-tk
pip install pygraphviz
```

```bash
pip install whl/hailo_dataflow_compiler-3.27.0-py3-none-linux_x86_64.whl
pip install whl/hailo_model_zoo-2.11.0-py3-none-any.whl
```

```bash
git clone https://github.com/hailo-ai/hailo_model_zoo.git
```

### Install Custom dataset
```bash
python steps/2_install_dataset/create_custom_tfrecord.py val
python steps/2_install_dataset/create_custom_tfrecord.py train
```
### Parse
```bash
python steps/3_process/parse.py
```
### Optimize
-Use your own username /home/USER
```bash
python steps/3_process/optimize.py
```
### Compile
```bash
python steps/3_process/compile.py
```

## Raspbery Pi 5

```bash
cd hailo8l
git clone https://github.com/hailo-ai/hailo-rpi5-examples.git
pip install setproctitle

```

```bash
cd hailo-rpi5-examples
source setup_env.sh
cd ..
```

### Run Object Detection
```bash
python hailo-rpi5-examples/basic_pipelines/detection.py -i rpi --hef best_quantized_model.hef --labels-json labels.json
```

# How to Setup Raspberry Pi 5 with Hailo8l AI Kit using yolov11n on Windows (WSL2 Ubuntu 24.04) Custom Objects and Labels

## WSL Ubuntu 24.04

### Get Guide
```bash
git clone https://github.com/BetaUtopia/Hailo8l.git
```

### Labeling with Label Studio
```bash
sudo apt install python3-pip
python3 -m venv venv_labelstudio
source venv_labelstudio/bin/activate
pip install label-studio
label-studio start
```

```bash
cd ~/Hailo8l && deactivate
```

### Training
```bash
cd Hailo8l
sudo apt-get update
sudo apt-get install libpython3.12-stdlib libgl1-mesa-glx
sudo apt install python3.12 python3.12-venv
python3.12 -m venv venv_yolov11
source venv_yolov11/bin/activate
pip install ultralytics
```

```bash
yolo detect train data=config_yolov11n.yaml model=yolo11n.pt name=retrain_yolov11n project=./model/runs/detect epochs=1000 batch=16
```
### Convert to ONNX
```bash
cd model/runs/detect/retrain_yolov11n/weights   
```

```bash
yolo export model=./best.pt imgsz=640 format=onnx opset=11 
```

```bash
cd ~/Hailo8l && deactivate
```

### Install Hailo
```bash
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update
sudo apt-get install python3.10 python3.10-venv python3.10-dev
```

```bash
python3.10 -m venv venv_hailo
source venv_hailo/bin/activate
sudo apt-get update
sudo apt-get install build-essential python3-dev graphviz graphviz-dev python3-tk
pip install pygraphviz scipy==1.9.3
```

Install hailo_dataflow_compiler 
```bash
sudo reboot
```

Install hailo_model_zoo 
```bash
pip install whl/hailo_dataflow_compiler-3.30.0-py3-none-linux_x86_64.whl
pip install whl/hailo_model_zoo-2.14.0-py3-none-any.whl
```
-Use your own username /home/USER

Parse
```bash
python steps/3_process/parse_yolo11n.py
```
Optimize
```bash
python steps/3_process/optimize_yolo11n.py
```
Compile
```bash
python steps/3_process/compile_yolo11n.py
```

### Raspbery Pi

```bash
sudo apt update
sudo apt full-upgrade
sudo apt install hailo-all
sudo reboot
```

Check Versions

```bash
hailortcli fw-control identify
```

```bash
git clone https://github.com/hailo-ai/hailo-rpi5-examples.git
cd hailo-rpi5-examples
```

```bash
./install.sh
```

```bash
source setup_env.sh
```

```bash
python basic_pipelines/detection.py -i rpi --hef /home/pi/Hailo8l/best.hef --labels-json /home/pi/Hailo8l/labels_yolov11n.json
```

### Raspbery Pi with picamera2

```bash
pip uninstall opencv-python --break-system-packages
pip install opencv-python-headless --break-system-packages
```

```bash
python steps/4_test/detect_picamera2.py
```
