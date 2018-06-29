FROM alpine:3.5 as atomicparsley
MAINTAINER Simon Martin <havvoric@gmail.com>

RUN apk --update add g++

RUN mkdir -p /data

ADD https://downloads.sourceforge.net/project/atomicparsley/atomicparsley/AtomicParsley%20v0.9.0/AtomicParsley-source-0.9.0.zip /tmp

RUN cd /tmp && unzip AtomicParsley-source-0.9.0.zip && \
    cd AtomicParsley-source-0.9.0 && \
    sed -i -e '/#define _UINT64_T/i #ifndef __DEFINED_uint64_t' -e '/#endif .. _UINT64_T ../i #endif \/* __DEFINED_uint64_t */' AP_commons.h && \
    sed -i 's|\(fsetpos(\)\(.*\)\(&\)\(.*\)\().*\)|fseeko(\2\4,SEEK_SET\5|g' AtomicParsley.cpp && \
    ./build

FROM alpine:3.5
MAINTAINER Simon Martin <havvoric@gmail.com>

RUN mkdir -p /data/output /data/config

COPY --from=atomicparsley /tmp/AtomicParsley-source-0.9.0/AtomicParsley /usr/local/bin

RUN apk --update add \
    ffmpeg \
    openssl \
    perl-mojolicious \
    perl-lwp-protocol-https \
    perl-xml-simple \
    perl-xml-libxml

WORKDIR /app

ENV GET_IPLAYER_VERSION=3.14

RUN wget -qO- https://github.com/get-iplayer/get_iplayer/archive/v${GET_IPLAYER_VERSION}.tar.gz | tar -xvz -C /tmp && \
    mv /tmp/get_iplayer-${GET_IPLAYER_VERSION}/get_iplayer . && \
    rm -rf /tmp/* && \
    chmod +x ./get_iplayer

ENTRYPOINT ["./get_iplayer", "--subtitles", "--ffmpeg", "/usr/bin/ffmpeg", "--atomicparsley", "/usr/local/bin/AtomicParsley", "--profile-dir", "/data/config", "--output", "/data/output"]

CMD ["-h"]
