FROM holomekc/wiremock-gui:latest

COPY ./mappings /home/wiremock/storage/mappings
COPY ./__files /home/wiremock/storage/__files

USER root

RUN apk update && apk add jq curl && chown wiremock -R /home/wiremock/storage

USER wiremock
