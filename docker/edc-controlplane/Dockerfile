FROM gradle:jdk11
# https://docs.docker.com/develop/develop-images/multistage-build/#use-multi-stage-builds

RUN apt-get update && apt-get install -y maven

WORKDIR /build

# JAVA build options - 512m is not enough
ENV _JAVA_OPTIONS="-Xmx2048m"

COPY ./edc ./edc

# to cache gradle distribution download
ENV GRADLE_USER_HOME=/build/.gradle
RUN cd ./edc && ./gradlew --version

# build EDC first
RUN cd ./edc && ./gradlew ${MAVEN_OPTS} clean build publishToMavenLocal -x test

COPY ./mvnw settings.xml pom.xml ./
COPY ./.mvn ./.mvn

COPY ./edc-extensions ./edc-extensions
COPY ./edc-controlplane ./edc-controlplane
COPY ./edc-dataplane ./edc-dataplane
COPY ./edc-dataplane-fs-vault ./edc-dataplane-fs-vault

RUN ./mvnw -pl .,edc-extensions/business-partner-validation -am package

# where we save to jar result to easier find them
RUN mkdir /jars

# build edc-controlplane (memory) and copy result to /jars
RUN ./mvnw -pl .,edc-controlplane/edc-controlplane-memory -am package
RUN cp -a /build/edc-controlplane/edc-controlplane-memory/target/edc-controlplane-memory.jar /jars/
# cosmosdb
RUN ./mvnw -pl .,edc-controlplane/edc-controlplane-cosmosdb -am package
RUN cp -a /build/edc-controlplane/edc-controlplane-cosmosdb/target/edc-controlplane-cosmosdb.jar /jars/
# postgresql
RUN ./mvnw -pl .,edc-controlplane/edc-controlplane-postgresql -am package
RUN cp -a /build/edc-controlplane/edc-controlplane-postgresql/target/edc-controlplane-postgresql.jar /jars/

# move upwards
# build edc-controlplane (memory) without any azure dependencies. E.g. use filesystem-vault
RUN ./mvnw -pl .,edc-controlplane/edc-controlplane-memory-fs-vault -am package
RUN cp -a /build/edc-controlplane/edc-controlplane-memory-fs-vault/target/edc-controlplane-memory-fs-vault.jar /jars/


# build edc-dataplane and copy result to /jars
RUN ./mvnw -pl .,edc-dataplane -am package
RUN cp -a /build/edc-dataplane/target/edc-dataplane.jar /jars/

RUN ./mvnw -pl .,edc-dataplane-fs-vault -am package
RUN cp -a /build/edc-dataplane-fs-vault/target/edc-dataplane-fs-vault.jar /jars/

RUN find /jars

# create default cert
# TODO: can we use localhost.localdomain? Do we need an "email" as described in the Readme?
RUN mkdir /certs
RUN openssl req -newkey rsa:2048 -new -nodes -x509 -days 3650 -subj "/CN=localhost.localdomain" -keyout /certs/key.pem -out /certs/cert.pem
# SECURITY: default password used!
RUN openssl pkcs12 -inkey /certs/key.pem -in /certs/cert.pem -export -out /certs/cert.pfx -password pass:123456
RUN find /certs

FROM openjdk:11-jre-slim-buster
ARG JAR

WORKDIR /app

# copy the relevant jar from the build image
COPY --from=0 /jars/$JAR app.jar
RUN ls -la

# copy default cert built in the build image
RUN mkdir /certs
COPY --from=0 /certs/* /certs/

EXPOSE 9191
EXPOSE 8090

ENTRYPOINT java \
    -jar app.jar
