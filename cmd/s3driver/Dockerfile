FROM golang:1.16 as gobuild

WORKDIR /build
ADD . /build

RUN go env -w GOPROXY=https://goproxy.cn,direct
RUN go get -d -v ./...
RUN CGO_ENABLED=0 GOOS=linux go build -a -ldflags '-extldflags "-static"' -o ./s3driver ./cmd/s3driver

FROM centos:7
LABEL maintainers="Cyrill Troxler <cyrilltroxler@gmail.com>"
LABEL description="csi-s3 slim image"

# s3fs and some other dependencies
RUN yum install -y epel-release && yum install -y s3fs curl unzip && yum clean all

# install rclone
#ARG RCLONE_VERSION=v1.54.1
#RUN cd /tmp \
#  && curl -O https://downloads.rclone.org/${RCLONE_VERSION}/rclone-${RCLONE_VERSION}-linux-amd64.zip \
#  && unzip /tmp/rclone-${RCLONE_VERSION}-linux-amd64.zip \
#  && mv /tmp/rclone-*-linux-amd64/rclone /usr/bin \
#  && rm -r /tmp/rclone*

COPY --from=gobuild /build/s3driver /s3driver
RUN /s3driver --help

ENTRYPOINT ["/s3driver"]
