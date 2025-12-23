# 1. Aşama: Builder (Kesinlikle Multi-Platform desteği olan versiyon)
FROM eclipse-temurin:17-jdk AS builder
WORKDIR application
ARG ARTIFACT_NAME
COPY ${ARTIFACT_NAME}.jar application.jar
RUN java -Djarmode=layertools -jar application.jar extract

# 2. Aşama: Runner (Hafif ve ARM uyumlu)
FROM eclipse-temurin:17-jre
WORKDIR application

ENV JAVA_OPTS="-XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0 -Djava.security.egd=file:/dev/./urandom"
ENV SPRING_PROFILES_ACTIVE=docker

ARG EXPOSED_PORT=8080
EXPOSE ${EXPOSED_PORT}

COPY --from=builder application/dependencies/ ./
COPY --from=builder application/spring-boot-loader/ ./
COPY --from=builder application/snapshot-dependencies/ ./
COPY --from=builder application/application/ ./

ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS org.springframework.boot.loader.launch.JarLauncher"]