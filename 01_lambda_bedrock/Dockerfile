FROM --platform=linux/amd64 amazonlinux:2

RUN yum update -y && \
    yum install -y gcc openssl-devel bzip2-devel libffi-devel tar wget make unzip zip sudo su curl

# Install Python@3.9
RUN cd /usr/src && \
    wget https://www.python.org/ftp/python/3.9.17/Python-3.9.17.tgz && \
    tar xzf Python-3.9.17.tgz && \
    cd Python-3.9.17 && \
    ./configure --enable-optimizations && \
    make altinstall

RUN ln -sf /usr/local/bin/python3.9 /usr/bin/python3 && \
    ln -sf /usr/local/bin/pip3.9 /usr/bin/pip3

# Install & Setup AWS CLI
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install

ARG AWS_ACCESS_KEY_ID
ARG AWS_SECRET_ACCESS_KEY
ARG AWS_DEFAULT_REGION

RUN aws configure set aws_access_key_id "$AWS_ACCESS_KEY_ID" && \
    aws configure set aws_secret_access_key "$AWS_SECRET_ACCESS_KEY" && \
    aws configure set default.region "$AWS_DEFAULT_REGION" && \
    aws configure set default.format "json"