ARG BUILD_FROM
FROM $BUILD_FROM

ARG BUILD_VERSION

# Create Working Directory
WORKDIR /app

# Install enoceanmqtt and requirements
RUN apk add --no-cache python3 py3-pip git && \
    python3 -m venv /app/venv && \
    . /app/venv/bin/activate && \
    python3 -m pip install --upgrade pip setuptools wheel && \
    pip3 install pyyaml==6.0.1 && \
    pip3 install tinydb==4.7.1 && \
    pip3 install paho-mqtt==1.6.1 && \
    pip3 install 'beautifulsoup4<4.13' && \
    pip3 install git+https://github.com/mak-gitdev/enocean.git && \
    git clone -b master --single-branch --depth 1 https://github.com/embyt/enocean-mqtt.git && \
    cd enocean-mqtt && python3 -m pip install -e . && cd ..

# Pull the daemon code from the fork. The ADD line is a cache-buster: when the branch
# HEAD changes, this layer (and the clone below) is invalidated and re-run. Without it
# Docker reuses a cached clone and silently ships an outdated commit.
ADD https://api.github.com/repos/t-ice/HA_enoceanmqtt/git/refs/heads/fsb14-position-persist /tmp/ha_enoceanmqtt_ref.json
RUN echo "Installing fsb14-position-persist branch (fork with cover position persistence)" && \
    git clone -b fsb14-position-persist --single-branch --depth 1 https://github.com/t-ice/HA_enoceanmqtt.git && \
    cp -rf HA_enoceanmqtt/enoceanmqtt enocean-mqtt && \
    rm -rf HA_enoceanmqtt

# Copy data for add-on
COPY run.sh .
RUN chmod a+x run.sh

CMD [ "/app/run.sh" ]
