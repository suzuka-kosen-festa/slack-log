FROM alpine:3.20.3

RUN apk --no-cache add \
  fuse \
  bash \
  rclone \
  coreutils \
  ca-certificates \
  openssh-client-default

WORKDIR /app
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
