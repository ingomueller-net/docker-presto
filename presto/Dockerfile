FROM maven:3.6.3-openjdk-8 AS namespace-manager-builder
ARG MAVEN_OPTS
RUN git clone --branch 0.246 --depth 1 https://github.com/prestodb/presto.git /presto && \
    cd /presto && \
    mvn clean install -pl presto-function-namespace-managers -am -DskipTests && \
    rm -rf /root/.m2

FROM openjdk:8-jre
MAINTAINER Ingo Müller <ingo.mueller@inf.ethz.ch>
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
        python-minimal \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN mkdir /opt/presto && \
    cd /opt/presto && \
    wget --progress=dot:giga https://repo1.maven.org/maven2/com/facebook/presto/presto-server/0.246/presto-server-0.246.tar.gz -O - | \
         tar -xz --strip-components=1 && \
    wget --progress=dot:giga https://repo1.maven.org/maven2/com/facebook/presto/presto-cli/0.246/presto-cli-0.246-executable.jar -O /opt/presto/bin/presto && \
    chmod +x /opt/presto/bin/presto

COPY --from=namespace-manager-builder /presto/presto-function-namespace-managers/target/presto-function-namespace-managers-0.246/ /opt/presto/plugin/function-namespace-managers
COPY etc /opt/presto/etc

ENTRYPOINT /opt/presto/bin/launcher run
