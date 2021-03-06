FROM alpine:3.6
LABEL maintainer="Kong Core Team <team-core@konghq.com>"

ENV KONG_VERSION 1.0.0rc2
ENV KONG_SHA256 42eba2f0c566740472ce69aae44dd93df81a75f494f32f45285426545ba1e914

RUN apk add --no-cache --virtual .build-deps wget tar ca-certificates \
	&& apk add --no-cache libgcc openssl pcre perl tzdata curl \
	&& wget -O kong.tar.gz "https://bintray.com/kong/kong-community-edition-alpine-tar/download_file?file_path=kong-community-edition-$KONG_VERSION.apk.tar.gz" \
	&& echo "$KONG_SHA256 *kong.tar.gz" | sha256sum -c - \
	&& tar -xzf kong.tar.gz -C /tmp \
	&& rm -f kong.tar.gz \
	&& cp -R /tmp/usr / \
	&& rm -rf /tmp/usr \
	&& cp -R /tmp/etc / \
	&& rm -rf /tmp/etc \
	&& apk del .build-deps \
	# OpenShift specific. OpenShift runs containers using an arbitrarily assigned user ID.
	# This user doesn't have access to change file permissions during runtime, they have to be changed during image building.
	# https://docs.okd.io/latest/creating_images/guidelines.html#use-uid
	&& mkdir -p "/usr/local/kong" \
	&& chgrp -R 0 "/usr/local/kong" \
	&& chmod -R g=u "/usr/local/kong"

COPY docker-entrypoint.sh /docker-entrypoint.sh
ENTRYPOINT ["/docker-entrypoint.sh"]

EXPOSE 8000 8443 8001 8444

STOPSIGNAL SIGTERM

CMD ["kong", "docker-start"]
