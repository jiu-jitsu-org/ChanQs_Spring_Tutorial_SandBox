# 1) Build stage
FROM eclipse-temurin:17-jdk-jammy AS build
WORKDIR /workspace

# Gradle wrapper 복사 + 권한 (wrapper는 반드시 컨텍스트에 있어야 함)
COPY gradlew /workspace/gradlew
COPY gradle  /workspace/gradle
RUN chmod +x /workspace/gradlew

# Groovy/Kotlin DSL 모두 대응 (존재하는 파일만 매칭됨)
COPY settings.gradle* build.gradle* /workspace/

# (디버그) wrapper가 실제로 복사됐는지 확인 + Gradle 실행 확인
RUN ls -al /workspace/gradle/wrapper && ./gradlew --no-daemon --version

# 소스 복사 후 JAR 빌드
COPY src /workspace/src
RUN ./gradlew --no-daemon clean bootJar

# 2) Run stage (슬림 JRE)
FROM eclipse-temurin:17-jre-jammy
WORKDIR /app
COPY --from=build /workspace/build/libs/*.jar app.jar
EXPOSE 8080
# ENTRYPOINT ["java","-XX:MaxRAMPercentage=75.0","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
ENTRYPOINT ["java","-XX:MaxRAMPercentage=75.0","-Djava.security.egd=file:/dev/./urandom","-jar","app.jar"]