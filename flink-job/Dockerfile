FROM openjdk:8 AS job-build
WORKDIR /home/gradle/app/

COPY build.gradle settings.gradle ./
COPY gradlew ./
COPY gradle gradle
# To cause download and cache of verifyGoogleJavaFormat dependency
RUN echo "class Dummy {}" > Dummy.java
# download dependencies
# RUN curl --insecure -L https://services.gradle.org/distributions/gradle-4.4-all.zip -o gradle-4.4-all.zip

RUN curl --insecure -L https://services.gradle.org/distributions/gradle-4.4-all.zip -o gradle-4.4-all.zip
RUN unzip gradle-4.4-all.zip
ENV GRADLE_HOME=/home/gradle/app/gradle-4.4
ENV PATH=$PATH:$GRADLE_HOME/bin
RUN gradle --version

RUN gradle build
# RUN ./gradlew build
COPY . .
RUN rm Dummy.java
RUN gradle build
# RUN ./gradlew build
RUN mv /home/gradle/app/build/libs/dynamic-fraud-detection-demo*-deploy.jar /home/gradle/app/build/libs/dynamic-fraud-detection-demo-deploy.jar

# ---

FROM flink:1.8.2

COPY --from=job-build /home/gradle/app/build/libs/dynamic-fraud-detection-demo-deploy.jar lib/job.jar
COPY docker-entrypoint.sh /

USER flink
EXPOSE 8081 6123
ENTRYPOINT ["/docker-entrypoint.sh"]
