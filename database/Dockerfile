FROM postgres:16.13-alpine

RUN apk upgrade --no-cache

COPY init.sql /docker-entrypoint-initdb.d/

EXPOSE 5432