FROM eclipse-temurin:17-jdk AS builder
WORKDIR application
ARG ARTIFACT_NAME
COPY ${ARTIFACT_NAME} application.jar
RUN java -Djarmode=layertools -jar application.jar extract

FROM eclipse-temurin:17-jre
WORKDIR application
COPY --from=builder application/dependencies/ ./
COPY --from=builder application/spring-boot-loader/ ./
COPY --from=builder application/snapshot-dependencies/ ./
COPY --from=builder application/application/ ./
ENTRYPOINT ["sh", "-c", "java org.springframework.boot.loader.launch.JarLauncher"]