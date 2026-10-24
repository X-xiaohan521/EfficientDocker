# base image
FROM debian:latest

# build
RUN apt update
RUN apt install -y go
COPY ./hello-web.go /srv/
RUN go build -o /srv/hello-web /srv/hello-web.go

# runtime config
EXPOSE 8080
CMD ["/srv/hello-web"]