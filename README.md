# Ubuntu 18.04, Nvidia GeForce RTX 2080 Ti, tensorflow, OpenCV, Jupyter Installation

Nvidia 에서 최근에 RTX 2080 Ti를 출시하였습니다. 가성비가 상당히 좋기 때문에 많은 분들이 관심을 갖고 있습니다. 저는 이번에 이것을 설치해보려고 합니다. 인터넷에 많은 설치 튜토리얼이 나와 있음에도 불구하고 많은 어려움과 Trouble shooting이 있었습니다. 그래서 이번 설치 튜터리얼을 만들었으니, 도움이 되기를 바랍니다. ^^

## 1. Ubuntu 18.04 Install 하기
Ubuntu 18.04.3(LTS) 는 Ubuntu 공식홈페이지 (https://ubuntu.com/download/desktop) 에서 다운을 받아 설치합니다. 저는 아래의 과정을 통해 진행하였습니다.
* Ubuntu 공식홈페이지에서 iso 파일로 다운받고,
* ODD 드라이에 넣어 USB 로 부팅함( 부팅시 부팅옵션을 선택하여 ODD로 부팅)
* Ubuntu 18.04 를 설치
설치가 완료된후 재부팅 한 다음  아래의 명령어를 터미널 화면에 입력합니다. 이를 통해 Ubuntu를 업데이트 합니다.
```
sudo apt-get update
sudo apt-get upgrade
```
이제부터 본 설치 튜토리얼은 시작합니다.


## 2. Nvidia Driver Install 하기
가장 먼저 할것은 RTX 2080 Ti 가 물리적으로 제대로 설치되었는지 확인 하는 것입니다. 이것은 아래의 명령어를 통해 확인할 수 있습니다.
```
lspci | grep -i nvidia  
```
제대로 설치가 되었으면 GPU 들이 터미널 화면에 등장합니다. 그렇지 않다면 물리적으로 설치가 안된것이니 확인하기 바랍니다.


Nvidia 드라이버를 설치하는 쉽지않습니다. 그 원인은 xserver와의 원인미상의 충돌때문입니다. 그래서 아래 3단계를 통해 진행합니다.
* Secure boot disable
* Nouveau driver disable
* Nvidia driver install


### 2.1 Secure boot disable 하기
Secure boot disable 하기는 아래의 명령어를 통해 진행할 수 있습니다. 저는 mokutil 를 설치하여 진행했는데, 혹시 부팅간 부팅설정에서 secure boot를 이미 disable 하셨다면 이것은 불필요 하겠습니다.
```
sudo apt install mokutil -y
sudo mokutil --disable-validation
```

### 2.2 Nouveau driver disable 하기
이것은 마찬가지로 아래 명령어를 통해 진행됩니다. 터미널 화면에 아래와 같은 명령어를 입력합니다.
```
sudo bash -c "echo blacklist nouveau > /etc/modprobe.d/blacklist-nvidia-nouveau.conf"
sudo bash -c "echo options nouveau modeset=0 >> /etc/modprobe.d/blacklist-nvidia-nouveau.conf"
cat /etc/modprobe.d/blacklist-nvidia-nouveau.conf
  # blacklist nouveau
  # options nouveau modeset=0
sudo update-initramfs -u
```
다 완료가 되면 재부팅을 하기 바랍니다.
```
reboot
```


### 2.3 Nvidia Driver Install 하기
진행하기 앞서, gcc 및 make 의 2가지 프로그램을 설치해야 합니다. 아마 Nvidia Driver를 설치할때 이 2가지 프로그램이 설치 안되어 있으면 에러 메세지가 나타날 수 있습니다. 저는 Ubuntu 18.04를 처음부터 하는데, 설치가 안되어 있더라구요. 아래의 명령어를 통해 설치하였습니다.
```
sudo apt install gcc
sudo apt install make
```
이것이 완료되면, Nvidia 홈페이지에 (https://www.nvidia.com/Download/index.aspx) 가서 드라이버를 다운 받습니다. 옵션이 여러가지가 등장하는데 다음과 같습니다.
* Product Type: GeForce
* Product Series: GeForce RTX 20 series
* Product: GeForce RTX 2080 Ti
* Operating System: Linux 64-bit
* Download Type: Game Ready Driver(GRD)
* Language: English
이렇게 입력을 하시면, 2019년 8월 29일 올라온 435.21 버전이 보이실 겁니다. 저는 이것으로 진행하겠습니다. 다운을 받습니다. 다운받는 위치는 Download 폴더로 합니다. 이것이 default 값이어서 그냥 save 를 누르면 되겠습ㄴ디ㅏ.

Nvidia drivier 를 설치하기 위해선 기존의 Ubuntu 화면( Ubuntu Desktop 및 Terminal 화면이 보이는 ) 이 아닌, 완전 터미널이 보이는 화면에서 진행을 해야합니다. 저의 경우에는 ctrl+alt+F2 를 사용하였는데, 환경마다 다를수 있으니 ctrl+alt+ F3, 4, 5, ...10 등을 사용하기 바랍니다. 
ctrl+alt+F2 를 누르면 완전 터미널 화면이 등장하면서 id 및 password 를 물어봅니다. 그다음에 아래와 같은 명령어를 입력합니다. 이때 마지막 명령어에는 꼭 --no-x-check을 넣기 바랍니다.(외국 친구의 추천)

```
cd Download
sudo chmod +x NVIDIA-Linux-x86_64-435.21.run
sudo ./NVIDIA-Linux-x86_64-435.21.run --no-x-check
```
설치할 때 에러메세지 처럼 보이는 것들이 등장하는데, 디폴트 값으로 진행하기 바랍니다. 혹시 xsever 등이 등장하면 no 클릭 하기 바랍니다.
설치가 완료되면 재부팅을 하십시요
```
reboot
```
재부팅을 한 다음, 드라이버가 제대로 설치가 되었는지 다음 명령어를 터미널창에 실행합니다. 제대로 설치가 되었으며, 설치된 GPU 목록과 드라이버 버전이 등장할 것입니다. 
```
nvidia-smi
```

## 3. CUDA Install 하기
지금까지 성공적으로 설치하셨다면 이제부터 CUDA를 설치하겠습니다. 웹사이트에 가서 https://developer.nvidia.com/cuda-zone CUDA를 다운받습니다.
2019년 9월 1일 기준으로 텐서플로에서는 CUDA 10.0 버전을 지원하기 때문에 웹사이트 우측 하단에 있는 legacy release를 클릭하고,  2018년 9월에 업로드된 CUDA Toolkit 10.0 을 클릭합니다. 다운로드 조건은 저는 아래와 같이 하였습니다.
* Linux
* x86_64
* Ubuntu
* 18.04
* runfile(local)
다운용량이 2.0GB 정도 되네요. 한 20분 걸린것 같습니다. 이것을 동일하게 Download 폴더에 저장하고 아래의 명령을 실행합니다.
```
cd Download
sudo sh cuda_10.0.130_410.48_linux.run
```
그러면 상당히 많은 텍스트들이 등장합니다. --More--(0%) 에서 시작을 하실텐데, Enter 를 누르면 한줄씩 진행되는데 Space를 누르면 쭈~욱하고 금방지나가니 참고하십시요. 
--More--(100%) 가 되면 다음과 같은 질문이 등장합니다. 아래와 같이 진행하기 바랍니다.
* Do you accept the previsouly read EULA? accept
* Install Nvidia Accelerted Graphics Driver for Linux-x86_64 410.48? no  # 이미 우리는 설치했습니다.
* Install the CUDA 10.0 Toolkit? yes
* Enter Toolkit Location?  Enter  # 디폴트로 하겠습니다. 그냥 엔터치십시요
* Do you want to install a symbolic link at /usr/local/cuda? yes
* Install the CUDA 10.0 smaples? yes
* Enter CUDA samples location? Enter  # 디폴트로 하겠습니다. 그냥 엔터치십시요

이렇게 옵션을 설치하면, CUDA Toolkit 이 설치됩니다.

설치를 마쳤으면, 항상 실행이 되도록 경로를 bashrc에 추가하도록 하겠습니다. 먼저 아래와 같이 터미널 화면에 명령어를 실행합니다. 저는 gedit을 주로 사용하는데, 편하신 데로 하면 됩니다.
```
sudo gedit ~/.bashrc
```
그러면 gedit 환경에서 bashrc에 있는 글자들이 주루룩 나올겁니다. 아무튼 맨 아래에 두개의 경로를 추가합니다.

```
export PATH=/usr/local/cuda-10.0/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-10.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```
저장을 하고 나옵니다. 확실히 환경설정을 위해서 아래의 명령어를 한번더 입력합니다.
```
source ~/.bashrc
```
설치가 제대로 되었는지 확인하기 위해, 아래의 명령어를 터미널 화면에서 실행합니다.
```
nvcc --version
```
그러면 아마 설치된 버전이 나타납니다. 제대로 설치된 것이죠 ^^

## 4. cuDNN Install 하기
지금까지 수고많으셨습니다. 이제부터는 cuDNN을 설치하겠습니다. 홈페이지(https://developer.nvidia.com/rdp/cudnn-download) 가셔서 CUDA Toolkit 10.0 에 맞는 파일을 다운받습니다. 처음이시면 Join을 하셔야 합니다. 동의를 한다음에 아래의 옵션으로 진행합니다.
* Download cuDNN v7.6.3(August 23, 2019), for CUDA 10.0을 클릭합니다. 
* cuDNN Library for Linux
다운로드를 합니다. 동일하게 Download 폴더에 저장합니다. 터미널 화면에서 아래의 명령을 실행하여 압축을 해제시킵니다.
```
cd Donwload
tar -xzvf cudnn-10.0-linux-x64-v7.6.3.30.tgz
```
압축이 해제되면 동일한 폴더(~Download/)의 터미널 화면에서 다음과 같은 명령어를 실행합니다.
```
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```
이러한 명령어를 입력하면 cuDNN 설치가 종료됩니다. ^^

## 5. Tensorflow Install 하기
저는 가상환경을 만들어 텐서플로를 설치하겠습니다. 우선 터미널 화면에서 아래와 같은 명령어를 입력하여 기초적인 프로그램을 설치합니다.
```
sudo apt update
sudo apt install python3-dev python3-pip
sudo pip3 install -U virtualenv  
```
그런 다음, 이름이 'tf'인 가상환경을 만들어 보겠습니다. 아래와 같이 터미널 화면에서 명령어를 실행합니다.
```
virtualenv --system-site-packages -p python3 ./tf
```
이번에는 방금 만든 'tf' 이름의 가상환경을 실행해 보겠습니다. 아래와 같이 터미널 화면에서 명령어를 실행합니다.
```
source ./tf/bin/activate
```
자 이번에는 pip 를 업데이트 하겠습니다.
```
pip install --upgrade pip
```
그리고 tensorflow-gpu를 설치하겠습니다. 지금 설치된 것은 tensorflow v1.14 에 적합합니다. 따라서 아래와 같이 실행합니다.
```
pip install tensorflow-gpu==1.14.0
```
제대로 설치 되었는지 다음과 같이 실행해 봅니다.
```
python -c "import tensorflow as tf;print(tf.reduce_sum(tf.random.normal([1000, 1000])))"
```

## 6. OpenCV Install 하기
아래에 다음과 같은 명령어를 터미널 화면에 입력합니다.
```
pip install opencv-python
```

## 7. Jupyter Install 하기
저는 개인적으로 Jupyter notebook 과 Jupyter lab을 즐겨 사용합니다. Jupyter 설치는 다음과 같습니다.
```
pip install jupyter
```
