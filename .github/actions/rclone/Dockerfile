FROM alpine:3.19.1

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
