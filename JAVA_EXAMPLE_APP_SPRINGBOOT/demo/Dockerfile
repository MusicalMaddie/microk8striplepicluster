# Stage 1: Build the application with Gradle
FROM eclipse-temurin:21-jdk-jammy AS build

# Install Gradle
ENV GRADLE_VERSION=8.3
RUN apt-get update && \
    apt-get install -y wget unzip && \
    wget https://services.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip && \
    unzip gradle-${GRADLE_VERSION}-bin.zip -d /opt && \
    ln -s /opt/gradle-${GRADLE_VERSION} /opt/gradle && \
    rm gradle-${GRADLE_VERSION}-bin.zip

ENV PATH=/opt/gradle/bin:$PATH

WORKDIR /app
COPY . .
# Ensure the Gradle wrapper has executable permissions
RUN chmod +x gradlew
RUN ./gradlew build --no-daemon

# Stage 2: Run the application
FROM eclipse-temurin:21-jre-jammy
COPY --from=build /app/build/libs/*.jar /usr/local/lib/demo.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","/usr/local/lib/demo.jar"]