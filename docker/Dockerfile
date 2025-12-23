# FROM eclipse-temurin:17 AS builder
# WORKDIR application
# ARG ARTIFACT_NAME
# COPY ${ARTIFACT_NAME}.jar application.jar
# RUN java -Djarmode=layertools -jar application.jar extract


# FROM eclipse-temurin:17
# WORKDIR application

# ARG EXPOSED_PORT
# EXPOSE ${EXPOSED_PORT}

# ENV SPRING_PROFILES_ACTIVE=docker

# COPY --from=builder application/dependencies/ ./

# # fix for https://stackoverflow.com/questions/51115856/docker-failed-to-export-image-failed-to-create-image-failed-to-get-layer
# # (only last copy caused issue)
# # this seems to be triggered by using btrfs:
# # https://github.com/moby/moby/issues/36573
# RUN true
# COPY --from=builder application/spring-boot-loader/ ./
# RUN true
# COPY --from=builder application/snapshot-dependencies/ ./
# RUN true
# COPY --from=builder application/application/ ./
# ENTRYPOINT ["java", "org.springframework.boot.loader.launch.JarLauncher"]

# 1. Aşama: Builder (Derlenmiş dosyayı parçalara ayırır)
FROM eclipse-temurin:17-jdk-alpine AS builder
WORKDIR application
ARG ARTIFACT_NAME
# Maven'dan gelen JAR'ı içeri al
COPY ${ARTIFACT_NAME}.jar application.jar
# Spring Boot Layertools ile JAR'ı katmanlarına ayır (Önbellekleme için kritik)
RUN java -Djarmode=layertools -jar application.jar extract

# 2. Aşama: Runner (Sadece gerekli dosyalarla uygulamayı çalıştırır)
FROM eclipse-temurin:17-jre-alpine
WORKDIR application

# Pi 5'te RAM kullanımı kontrol altında tutmak için JVM bayrakları
# -XX:+UseContainerSupport: Konteyner limitlerini (K8s) anlamasını sağlar
ENV JAVA_OPTS="-XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0 -Djava.security.egd=file:/dev/./urandom"
ENV SPRING_PROFILES_ACTIVE=docker

ARG EXPOSED_PORT=8080
EXPOSE ${EXPOSED_PORT}

# Layer'ları builder aşamasından kopyala
COPY --from=builder application/dependencies/ ./
COPY --from=builder application/spring-boot-loader/ ./
COPY --from=builder application/snapshot-dependencies/ ./
COPY --from=builder application/application/ ./

# Uygulamayı başlat
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS org.springframework.boot.loader.launch.JarLauncher"]