FROM gcc:7.3.0 as builder
ARG MANGOS_SERVER_VERSION=master

RUN apt-get update -qq && \
    apt-get install openssl libssl-dev cmake -y && \
    git clone https://github.com/mangostwo/server.git -b ${MANGOS_SERVER_VERSION} --recursive && \
    cd server && \
    cmake . -DBUILD_REALMD=No -DBUILD_TOOLS=No -DCONF_DIR=conf/ && \
    make -j4 && \
    make install -j4

FROM debian:9
MAINTAINER Stephen SORRIAUX <stephen.sorriaux@gmail.com>
EXPOSE 8085
ENV LOGIN_DATABASE_INFO=127.0.0.1;3306;root;mangos;realmd
ENV WORLD_DATABASE_INFO=127.0.0.1;3306;root;mangos;mangos1
ENV CHARACTER_DATABASE_INFO=127.0.0.1;3306;root;mangos;character1
WORKDIR /etc/mangos/
COPY --from=builder /server/bin .
RUN apt-get update && \
    apt-get install wget libstdc++6 libmysql++-dev libssl-dev -y && \
    useradd -ms /bin/bash mangos && \
    wget --no-check-certificate https://raw.githubusercontent.com/StephenSorriaux/mangos-docker/master/launch_mangosd.sh && \
    chmod -R a+x . && \
    mv conf/mangosd.conf.dist conf/mangosd.conf && \
    sed -i 's/^LoginDatabaseInfo            =.*$/LoginDatabaseInfo            = LOGIN_DATABASE_INFO/' conf/mangosd.conf && \
    sed -i 's/^WorldDatabaseInfo            =.*$/WorldDatabaseInfo            = WORLD_DATABASE_INFO/' conf/mangosd.conf && \
    sed -i 's/^CharacterDatabaseInfo        =.*$/CharacterDatabaseInfo        = CHARACTER_DATABASE_INFO/' conf/mangosd.conf && \
    chown -R mangos:mangos . && \
    rm -rf /var/lib/apt/lists/*
USER mangos
CMD ["./launch_mangosd.sh"]
