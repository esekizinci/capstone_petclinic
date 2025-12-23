# 1. Aşama: Builder
FROM eclipse-temurin:17-jdk AS builder
WORKDIR application
ARG ARTIFACT_NAME
# Workflow'dan gelen tam yolu kopyalıyoruz
COPY ${ARTIFACT_NAME} application.jar
RUN java -Djarmode=layertools -jar application.jar extract

# 2. Aşama: Runner
FROM eclipse-temurin:17-jre
WORKDIR application
ENV JAVA_OPTS="-XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0"
COPY --from=builder application/dependencies/ ./
COPY --from=builder application/spring-boot-loader/ ./
COPY --from=builder application/snapshot-dependencies/ ./
COPY --from=builder application/application/ ./
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS org.springframework.boot.loader.launch.JarLauncher"]