FROM arm32v7/ubuntu
RUN apt update && apt install --no-install-recommends -y python3-pip python3-setuptools && rm -rf /var/lib/apt/lists/* && pip3 --no-cache-dir install wheel && pip3 --no-cache-dir install flask
