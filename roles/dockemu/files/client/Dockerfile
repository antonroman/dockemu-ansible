FROM alpine:latest as builder
#ENV libcoap_dir=libcoap
#COPY . $libcoap_dir
#WORKDIR $libcoap_dir

RUN apk add --update \
    bash    \
    autoconf \
    automake  \
    pkgconf \
    libtool \
    build-base \
    git \
  && rm -rf /var/cache/apk/* \
  && ls
RUN git clone https://github.com/obgm/libcoap.git
WORKDIR /libcoap   
RUN ./autogen.sh --clean && ./autogen.sh \ 
  && ./configure --disable-tests\
       --disable-documentation --enable-examples\
       --disable-dtls --disable-shared \
  && make \
  && cp /libcoap/examples/coap-server /tmp/. \
  && cp /libcoap/examples/coap-client /tmp/. \
  && ls /tmp 


FROM alpine:latest
COPY --from=builder /tmp/coap-* /libcoap/
CMD /libcoap/coap-client -m get -s 60 -T cafe coap://server.dockemu.me/time > /var/log/dockemu/coap-server.log
EXPOSE 5684

STOPSIGNAL SIGTERM


# make it multi-stage to build the library and the application fisrt, and then copy it in Apline so that we can execute a huge number of instance and save space and 
# RAM and CPU resources
