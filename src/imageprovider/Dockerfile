# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

FROM nginx:1.25.3

RUN apt-get update ; apt-get install curl gnupg2 ca-certificates lsb-release debian-archive-keyring --no-install-recommends --no-install-suggests -y

RUN curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor \
| tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null

# This file is needed for nginx-module-otel to be found.
RUN echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] http://nginx.org/packages/mainline/debian `lsb_release -cs` nginx" | tee /etc/apt/sources.list.d/nginx.list

RUN apt-get update ; apt-get install nginx-module-otel --no-install-recommends --no-install-suggests -y

RUN mkdir /static
COPY src/imageprovider/static /static

EXPOSE ${IMAGE_PROVIDER_PORT}

STOPSIGNAL SIGQUIT

COPY src/imageprovider/nginx.conf.template /nginx.conf.template

# Start nginx
CMD ["/bin/sh" , "-c" , "envsubst '$OTEL_COLLECTOR_HOST $IMAGE_PROVIDER_PORT $OTEL_COLLECTOR_PORT_GRPC $OTEL_SERVICE_NAME' < /nginx.conf.template > /etc/nginx/nginx.conf && cat  /etc/nginx/nginx.conf && exec nginx -g 'daemon off;'"]
