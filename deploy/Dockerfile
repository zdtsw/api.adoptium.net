FROM eclipse-temurin:17

# BUILDS FOR ADOPTIUM, NOT ADOPTOPENJDK
ARG MAVEN_FLAGS="-Padoptium,-adoptopenjdk"
ENV MAVEN_FLAGS=$MAVEN_FLAGS

ARG REPOSITORY="https://github.com/adoptium/api.adoptium.net.git"
ENV REPOSITORY=$REPOSITORY

ARG BRANCH="main"
ENV BRANCH=$BRANCH

RUN     useradd -ms /bin/bash api && \
        mkdir -p /home/api/deployment/ && \
        mkdir -p /logs && \
        mkdir -p /home/api/deployment/lib && \
        mkdir -p /home/api/build && \
        mkdir /tmp/build && \
        chown -R api: /home/api/ && \
        apt-get update && apt-get -y install openssl

WORKDIR /tmp/build

COPY . /tmp/build

RUN chown -R api: /tmp/build

USER api

RUN     ./mvnw clean install $MAVEN_FLAGS && \
        cp adoptium-updater-parent/adoptium-api-v3-updater/target/adoptium-api-v3-updater-*-jar-with-dependencies.jar /home/api/deployment/updater.jar && \
        cp -r adoptium-frontend-parent/adoptium-api-v3-frontend/target/quarkus-app/* /home/api/deployment/ && \
        mv /home/api/deployment/quarkus-run.jar /home/api/deployment/frontend.jar && \
        cp deploy/run.sh /home/api/deployment/ && \
        chmod +x /home/api/deployment/run.sh && \
        cd /tmp && \
        rm -rf /tmp/build ~/.m2 && \
        cd /home/api/ && find

WORKDIR /home/api/deployment/

ENV JAVA_OPTS=""
ARG type=frontend
ENV typeEnv=$type

CMD cd /home/api/deployment/ && ./run.sh ${typeEnv}

