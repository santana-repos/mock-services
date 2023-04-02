# build
FROM azul/zulu-openjdk-alpine:11 AS build

RUN addgroup -S fakeSMTP && adduser -S fakeSMTP -G fakeSMTP

USER fakeSMTP
WORKDIR /src

COPY . .

RUN ./mvnw clean package && cp target/*.jar app.jar && rm -rf target

# execution
FROM azul/zulu-openjdk-alpine:11 AS exec

RUN addgroup -S fakeSMTP && adduser -S fakeSMTP -G fakeSMTP

USER fakeSMTP
COPY --from=build /src/app.jar /app/fakeSMTP.jar

CMD ls -alh /app && java -jar /app/fakeSMTP.jar

EXPOSE 25

ENTRYPOINT exec java -jar /app/fakeSMTP.jar -s -b -p 25 --bind-address 0.0.0.0
