# collab uses bionic
FROM ubuntu:bionic-20221019

ADD sources.list .
ADD sources.list.d.zip .

RUN apt update && apt install -y wget gnupg2 zip
RUN unzip sources.list.d.zip -d /

ADD installed_pckgs.txt .
ADD repo-keys.txt .

# download and set ca-certificates
# source: https://serverfault.com/a/1093530
RUN wget --no-check-certificate http://security.ubuntu.com/ubuntu/pool/main/c/ca-certificates/ca-certificates_20211016~18.04.1_all.deb
RUN dpkg -r --force-depends ca-certificates
RUN dpkg -i ca-certificates_20211016~18.04.1_all.deb

# apply sources from collab ubuntu
RUN cp sources.list /etc/apt/sources.list

RUN wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-keyring_1.0-1_all.deb
RUN dpkg -i cuda-keyring_1.0-1_all.deb

RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 51716619E084DAB9
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys F60F4B3D7FA2AF80
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FCAE110B1118213C
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys B3CF35C315B55A9F
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys F142A4D99F16EB04
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys A4B469963BF863CC
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA6932366A755776

# source: https://askubuntu.com/a/1092572
RUN apt-get update --fix-missing  && apt-get install -y dselect gnupg2 dctrl-tools
RUN apt-key add repo-keys.txt

# fix for annoying apt bug
RUN echo "Acquire {\nhttp::Pipeline-Depth \"0\";\n};" > /etc/apt/apt.conf

ARG DEBIAN_FRONTEND=noninteractive
ENV TZ=Europe/Prague

# source: https://askubuntu.com/a/1013210
RUN sync-available && dpkg --set-selections < installed_pckgs.txt
RUN apt-get dselect-upgrade -y

RUN apt install -y python3.7 python3-distutils-extra python-apt
RUN update-alternatives --install /usr/bin/python python /usr/bin/python3.7 1 && update-alternatives --config python
RUN curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
RUN python get-pip.py

ADD requirements.txt .
RUN pip install -r requirements.txt

# source: https://github.com/NVIDIA/nvidia-docker/issues/1551#issuecomment-938794181
RUN rm -rf /usr/lib/x86_64-linux-gnu/libnvidia-ml.so.1 /usr/lib/x86_64-linux-gnu/libcuda.so.1

CMD ["bash", "-c", "source /etc/bash.bashrc && jupyter notebook --notebook-dir=/notebooks --ip 0.0.0.0 --no-browser --allow-root"]
