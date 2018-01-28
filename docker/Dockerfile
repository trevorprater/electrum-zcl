FROM arwineap/docker-ubuntu-python3.6

RUN apt-get update
RUN apt-get install -y software-properties-common vim pwgen
RUN apt-get update

RUN apt-get install -y build-essential python3 python3-dev python3-pip python3-venv 
RUN apt-get install -y git wget

RUN python3.6 -m pip install pip --upgrade
RUN python3.6 -m pip install wheel

# Generate config with random password and necessary changes.
RUN wget https://github.com/z-classic/zclassic/releases/download/Config/zclassic.conf
RUN mkdir ~/.zclassic && mv zclassic.conf ~/.zclassic/zclassic.conf
RUN sed -e '1,4d' < ~/.zclassic/zclassic.conf > ~/.zclassic/zclassic.conf.tmp 
RUN echo "rpcuser=zcl" >> ~/.zclassic/zclassic.conf.tmp.tmp
RUN echo "rpcpassword=$(pwgen 12 1)" >> ~/.zclassic/zclassic.conf.tmp.tmp
RUN echo "rpcport=8232" >> ~/.zclassic/zclassic.conf.tmp.tmp
RUN echo "txindex=1" >> ~/.zclassic/zclassic.conf.tmp.tmp
RUN cat ~/.zclassic/zclassic.conf.tmp >> ~/.zclassic/zclassic.conf.tmp.tmp
RUN mv ~/.zclassic/zclassic.conf.tmp.tmp ~/.zclassic/zclassic.conf && rm ~/.zclassic/zclassic.conf.tmp

# Clone, install, and run Zclassic daemon.
RUN git clone https://github.com/z-classic/zclassic
WORKDIR zclassic
RUN apt-get install -y pkg-config libc6-dev m4 g++-multilib
RUN apt-get install -y autoconf libtool ncurses-dev unzip
RUN apt-get install -y zlib1g-dev wget bsdmainutils automake
RUN ./zcutil/build.sh
RUN ./zcutil/fetch-params.sh

# Electrumx stuff
COPY ./bin /usr/local/bin
COPY ./VERSION /tmp

RUN chmod a+x /usr/local/bin/* && apt-get install openssl libsnappy-dev
RUN git clone https://github.com/google/leveldb.git
WORKDIR leveldb
RUN make
RUN mv libleveldb.* /usr/local/lib
WORKDIR include
RUN cp -R leveldb /usr/local/include
RUN ldconfig


# Install supervisor, a necessity for running several services inside of a container
ENV DEBIAN_FRONTEND noninteractive
RUN \
  sed -i 's/# \(.*multiverse$\)/\1/g' /etc/apt/sources.list && \
    apt-get update && \
      apt-get -y upgrade && \
        locale-gen en_US.UTF-8
RUN apt-get install -y curl htop man software-properties-common unzip
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8
ENV LC_ALL en_US.UTF-8
ENV HOME /root

RUN apt-get -y install supervisor && mkdir -p /var/log/supervisor && mkdir -p /etc/supervisor/conf.d
ADD supervisor.conf /etc/supervisor.conf

# Download electrumx-zcl
#WORKDIR ..
#RUN git clone https://github.com/BTCP-community/electrumx
#RUN mkdir ~/zcl_electrum_db
#WORKDIR electrumx

#CMD supervisord -c /etc/supervisor.conf

