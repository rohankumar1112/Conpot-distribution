# Use the official Ubuntu 22.04 image
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# Update package lists and upgrade existing packages
RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y --no-install-recommends \
        autoconf \
        libtool \
        subversion \
        python3-dev \
        mysql-server \
        libmysqlclient-dev \
        libxerces-c-dev \
        python3-mysqldb \
        python3-pip \
        libcurl4-openssl-dev \
        python3-sphinx \
        libxml2-dev \
        libxslt1-dev \
        doxygen \
        libcppunit-dev \
        libncurses5-dev \
        libncursesw5-dev \
        libcppunit-doc \
        pkg-config \
        libssl-dev \
        libffi-dev \
        cmake \
        git \
    && \
    pip install wheel && \
    pip install -U sphinx \
        lxml \
        gevent \
        python-dateutil \
        mixbox \
        pyasn1 \
        pycryptodomex \
        pysmi \
        mysqlclient \
        stix \
        pysnmp \
        pymysql \
        hpfeeds \
        charset-normalizer \
        bottle \
        beautifulsoup4 \
    && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /home    

# Clone Git repositories
RUN git clone https://github.com/sk4ld/gridpot.git && \
    git clone https://github.com/mushorg/conpot.git && \
    git clone https://github.com/gridlab-d/gridlab-d.git && \
    git clone https://github.com/rohankumar1112/Conpot-distribution.git

# Move setup.py, requirements.txt, and README.md from conpot to gridpot/conpot
RUN mv conpot/setup.py conpot/requirements.txt conpot/README.md gridpot/conpot/

# Install conpot
RUN cd gridpot/conpot/ && \
    python3 setup.py install

RUN rm -rf gridlabd/

WORKDIR /home/gridpot    

RUN if [ -d "/home/gridpot/gridlab-d" ]; then \
        rm -rf /home/gridpot/gridlab-d; \
    fi

WORKDIR /home/ 

RUN mv gridlab-d/ /home/gridpot/ 

WORKDIR /home/gridpot/gridlab-d/ 

RUN git submodule update --init
  
RUN apt-get update && \
    apt-get install -y build-essential cmake && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /home/gridpot/gridlab-d/

RUN mkdir cmake-build 
WORKDIR /home/gridpot/gridlab-d/cmake-build/

RUN cmake .. && \
    make && \
    make install

WORKDIR /home/gridpot/libiec61850-0.8.5

RUN make && \
    make INSTALL_PREFIX=/usr/local install

WORKDIR /home/gridpot/gridlab-d/models

RUN gridlabd -D run_realtime=1 --server --debug --verbose IEEE_13_Node_With_Houses.glm 2>&1 | tee HousesOutput.txt

WORKDIR /home/

RUN chown -R $USER:$USER /usr/local/lib/python3.10

RUN rm -r /usr/local/lib/python3.10/dist-packages/conpot-0.4.0-py3.10.egg/

WORKDIR /home/Conpot-distribution/

RUN  mv -f  conpot-0.4.0-py3.10.egg/  /usr/local/lib/python3.10/dist-packages/

RUN  rm -r /usr/local/lib/python3.10/dist-packages/modbus_tk-1.1.3-py3.10.egg/modbus_tk/modbus.py

RUN mv -f modbus.py /usr/local/lib/python3.10/dist-packages/modbus_tk-1.1.3-py3.10.egg/modbus_tk/

# Command to run when the container starts
ENTRYPOINT ["/bin/bash"]
