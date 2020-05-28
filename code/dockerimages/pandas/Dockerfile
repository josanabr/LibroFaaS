FROM arm32v7/ubuntu
RUN apt update && apt install -y python3-pip python3-setuptools build-essential python3-numpy && rm -rf /var/lib/apt/lists/*
RUN pip3 install --no-cache-dir Cython
RUN pip3 install --no-cache-dir pandas 
