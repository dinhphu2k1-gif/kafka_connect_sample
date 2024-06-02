ARG DEBEZIUM_VERSION
FROM debezium/connect:$DEBEZIUM_VERSION
ENV KAFKA_CONNECT_JDBC_DIR=$KAFKA_CONNECT_PLUGINS_DIR/kafka-connect-jdbc \
    INSTANT_CLIENT_DIR=/instant_client/ \
    DEBEZIUM_CONNECTOR_ORACLE_DIR=$KAFKA_CONNECT_PLUGINS_DIR/debezium-connector-oracle

ARG POSTGRES_VERSION=42.2.8
ARG KAFKA_JDBC_VERSION=10.2.0
ARG KAFKA_ELASTICSEARCH_VERSION=5.3.2
ARG JMX_AGENT_VERSION
ARG CONFLUENT_VERSION=7.5.3
ARG AVRO_VERSION=1.11.3
ARG COMPRESS_VERSION=1.21
ARG JACKSON_VERSION=2.12.3

USER root
RUN microdnf -y install libaio && microdnf clean all
RUN microdnf -y install wget && microdnf clean all

# Deploy PostgreSQL JDBC Driver
RUN cd /kafka/libs && curl -sO https://jdbc.postgresql.org/download/postgresql-$POSTGRES_VERSION.jar

# Deploy Kafka Connect JDBC
RUN mkdir $KAFKA_CONNECT_JDBC_DIR && cd $KAFKA_CONNECT_JDBC_DIR &&\
	curl -sO https://packages.confluent.io/maven/io/confluent/kafka-connect-jdbc/$KAFKA_JDBC_VERSION/kafka-connect-jdbc-$KAFKA_JDBC_VERSION.jar &&\
    cp $DEBEZIUM_CONNECTOR_ORACLE_DIR/* .

RUN mkdir /kafka/etc && cd /kafka/etc &&\
        curl -so jmx_prometheus_javaagent.jar \
        https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/$JMX_AGENT_VERSION/jmx_prometheus_javaagent-$JMX_AGENT_VERSION.jar

# download confluent avro convertor
RUN cd /kafka/libs &&\
    wget https://packages.confluent.io/maven/io/confluent/kafka-connect-avro-converter/${CONFLUENT_VERSION}/kafka-connect-avro-converter-${CONFLUENT_VERSION}.jar &&\
    wget https://packages.confluent.io/maven/io/confluent/kafka-connect-avro-data/${CONFLUENT_VERSION}/kafka-connect-avro-data-${CONFLUENT_VERSION}.jar &&\
    wget https://packages.confluent.io/maven/io/confluent/kafka-avro-serializer/${CONFLUENT_VERSION}/kafka-avro-serializer-${CONFLUENT_VERSION}.jar &&\
    wget https://packages.confluent.io/maven/io/confluent/kafka-schema-serializer/${CONFLUENT_VERSION}/kafka-schema-serializer-${CONFLUENT_VERSION}.jar &&\
    wget https://packages.confluent.io/maven/io/confluent/kafka-schema-converter/${CONFLUENT_VERSION}/kafka-schema-converter-${CONFLUENT_VERSION}.jar &&\
    wget https://packages.confluent.io/maven/io/confluent/kafka-schema-registry-client/${CONFLUENT_VERSION}/kafka-schema-registry-client-${CONFLUENT_VERSION}.jar &&\
    wget https://packages.confluent.io/maven/io/confluent/common-config/${CONFLUENT_VERSION}/common-config-${CONFLUENT_VERSION}.jar &&\
    wget https://packages.confluent.io/maven/io/confluent/common-utils/${CONFLUENT_VERSION}/common-utils-${CONFLUENT_VERSION}.jar &&\
    wget https://repo1.maven.org/maven2/org/apache/avro/avro/${AVRO_VERSION}/avro-${AVRO_VERSION}.jar &&\
    wget https://repo1.maven.org/maven2/org/apache/commons/commons-compress/${COMPRESS_VERSION}/commons-compress-${COMPRESS_VERSION}.jar &&\
    wget https://repo1.maven.org/maven2/com/google/guava/failureaccess/1.0.1/failureaccess-1.0.1.jar &&\
    wget https://repo1.maven.org/maven2/com/google/guava/guava/32.0.1-jre/guava-32.0.1-jre.jar &&\
    wget https://repo1.maven.org/maven2/com/eclipsesource/minimal-json/minimal-json/0.9.5/minimal-json-0.9.5.jar &&\
    wget https://repo1.maven.org/maven2/com/google/re2j/re2j/1.6/re2j-1.6.jar &&\
    wget https://repo1.maven.org/maven2/org/slf4j/slf4j-api/1.7.25/slf4j-api-1.7.25.jar &&\
    wget https://repo1.maven.org/maven2/org/yaml/snakeyaml/1.29/snakeyaml-1.29.jar &&\
    wget https://repo1.maven.org/maven2/io/swagger/core/v3/swagger-annotations/2.1.12/swagger-annotations-2.1.12.jar &&\
    wget https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-databind/${JACKSON_VERSION}/jackson-databind-${JACKSON_VERSION}.jar &&\
    wget https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-core/${JACKSON_VERSION}/jackson-core-${JACKSON_VERSION}.jar &&\
    wget https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-annotations/${JACKSON_VERSION}/jackson-annotations-${JACKSON_VERSION}.jar &&\
    wget https://repo1.maven.org/maven2/com/fasterxml/jackson/dataformat/jackson-dataformat-csv/${JACKSON_VERSION}/jackson-dataformat-csv-${JACKSON_VERSION}.jar &&\
    wget https://repository.cloudera.com/artifactory/cloudera-repos/org/cloudera/logredactor/logredactor/2.0.11/logredactor-2.0.11.jar &&\
    wget https://repo1.maven.org/maven2/io/confluent/logredactor-metrics/1.0.8/logredactor-metrics-1.0.8.jar

# COPY config.yml /kafka/etc/config.yml
# COPY connect-distributed.properties /kafka/config/connect-distributed.properties

RUN mkdir /kafka/connect/multiple-field-timestamp-converter/
COPY multiple-field-timestamp-converter-1.0.0-jar-with-dependencies.jar /kafka/connect/multiple-field-timestamp-converter/multiple-field-timestamp-converter-1.0.0-jar-with-dependencies.jar

USER kafka
# Deploy Oracle client and drivers

COPY oracle_instantclient/* $INSTANT_CLIENT_DIR
COPY oracle_instantclient/xstreams.jar /kafka/libs
COPY oracle_instantclient/ojdbc8.jar /kafka/libs
