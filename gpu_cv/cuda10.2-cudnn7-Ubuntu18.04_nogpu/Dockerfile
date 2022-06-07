FROM nvidia/cuda:10.2-cudnn7-devel-ubuntu18.04

# Update GPG key
RUN distro=$(. /usr/lib/os-release; echo $ID$VERSION_ID | tr -d ".") \
 && arch=$(/usr/bin/arch) \
 && apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/$distro/$arch/3bf863cc.pub \
 && apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/machine-learning/repos/$distro/$arch/7fa2af80.pub

# Default setting
RUN apt update && apt-get -y upgrade
RUN apt install -y --no-install-recommends
RUN apt install -y python3
RUN apt install python3-dev python3-pip python3-setuptools -y
RUN python3 -m pip install --upgrade pip setuptools
RUN python3 -m pip install numpy
RUN python3 -m pip install --upgrade pip
# install OpenCV
RUN apt-get install -y libgl1-mesa-glx libglib2.0-0 libsm6 libxrender1 libxext6
RUN python3 -m pip install opencv-python opencv-contrib-python

RUN python3 -m pip install sklearn pandas pillow matplotlib
# ffmpeg
RUN pip install ffmpeg-python

# jupyterを使用する場合は以下をインストール
#RUN wget https://repo.continuum.io/achive/Anaconda3-2019.10-Linux-x86_64.sh && \
#    sh Anaconda3-2019.10-Linux-x86_64.sh -b -p /opt/anaconda3 && \
#    rm -f Anaconda3-2019.10-Linux-x86_64.sh
#ENV PATH /opt/anaconda3/bin:$PATH

#自分が自由に操作できるディレクトリを作成
#WORKDIR /work/

# コマンド指定
CMD ["bin/bash"]
~                 
