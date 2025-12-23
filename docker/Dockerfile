# 1. Aşama: Builder
FROM eclipse-temurin:17-jdk AS builder
WORKDIR application
ARG ARTIFACT_NAME
# Yıldızlı gelen yolu COPY ile alıp ismini sabitleyelim
COPY ${ARTIFACT_NAME}.jar application.jar
# Dosyanın kopyalandığından emin olmak için bir check ekleyelim (Opsiyonel ama iyi olur)
RUN ls -lh application.jar
RUN java -Djarmode=layertools -jar application.jar extract

# ... geri kalan kısımlar aynı ...