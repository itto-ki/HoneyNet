FROM debian:buster-slim as builder
LABEL maintainer="Ittoh Kimura <kimura.itto.kd3@gmail.com>"

ENV COWRIE_GROUP=cowrie \
    COWRIE_USER=cowrie \
    COWRIE_HOME=/cowrie

RUN groupadd -r -g 1000 ${COWRIE_GROUP} && \
    useradd -r -u 1000 -d ${COWRIE_HOME} -m -g ${COWRIE_GROUP} ${COWRIE_USER}

WORKDIR ${COWRIE_HOME}

COPY cowrie cowrie/
RUN chown -R ${COWRIE_USER}:${COWRIE_GROUP} ${COWRIE_HOME}

RUN apt-get update && \
    apt-get install -y \
    git \
    python-virtualenv \
    libssl-dev \
    libffi-dev \
    build-essential \
    libpython3-dev \
    python3-minimal \
    authbind && \
    su - ${COWRIE_USER} -c "\
    cd cowrie && \
    virtualenv --python=python3 cowrie-env && \
    . cowrie-env/bin/activate && \
    pip install --upgrade pip && \
    pip install --upgrade -r requirements.txt"

FROM debian:buster-slim as runtime
LABEL maintainer="Ittoh Kimura <kimura.itto.kd3@gmail.com>"

ENV COWRIE_GROUP=cowrie \
    COWRIE_USER=cowrie \
    COWRIE_HOME=/cowrie

RUN groupadd -r -g 1000 ${COWRIE_GROUP} && \
    useradd -r -u 1000 -d ${COWRIE_HOME} -m -g ${COWRIE_GROUP} ${COWRIE_USER}

WORKDIR ${COWRIE_HOME}

RUN apt-get update && \
    apt-get install -y \
    libssl-dev \
    libffi-dev \
    procps \
    python3

COPY --from=builder ${COWRIE_HOME} ${COWRIE_HOME}
RUN chown -R ${COWRIE_USER}:${COWRIE_GROUP} ${COWRIE_HOME}

USER ${COWRIE_USER}
VOLUME ["/cowrie/cowrie/var", "/cowrie/cowrie/etc/"]
ENTRYPOINT ["cowrie/bin/cowrie"]
CMD ["start", "-n"]
EXPOSE 2222 2223
