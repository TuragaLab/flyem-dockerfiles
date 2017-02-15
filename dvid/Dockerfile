FROM golang:1.7.5
LABEL authors="Stephen Plaza, William Grisaitis"

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    cmake \
    git \
    mercurial \
    zip \
    && rm -rf /var/lib/apt/lists/*

WORKDIR $GOPATH/src/github.com/janelia-flyem/dvid
RUN git clone https://github.com/janelia-flyem/dvid . \
    && git checkout 6b5bb3adac2486a57567d7901eae9648677ce123

WORKDIR $GOPATH/src/github.com/janelia-flyem/dvid/build
ENV BUILDEM_DIR /opt/buildem
RUN cmake -D DVID_BACKEND="basholeveldb" -D BUILDEM_DIR=$BUILDEM_DIR ..
RUN make
RUN cmake -D BUILDEM_DIR=$BUILDEM_DIR ..
RUN make dvid

ADD config.toml /var/config.toml
ENV LD_LIBRARY_PATH $BUILDEM_DIR/lib:$LD_LIBRARY_PATH
ENV PATH /usr/local/go/bin:$BUILDEM_DIR/bin:$PATH

ENV CONSOLEVERSION 1.14.10
RUN wget https://github.com/janelia-flyem/dvid-console/releases/download/v${CONSOLEVERSION}/dvid-console-${CONSOLEVERSION}.tar.gz \
    && tar -C /usr/local -xzf dvid-console-${CONSOLEVERSION}.tar.gz

CMD ["dvid", "serve", "/var/config.toml"]
EXPOSE 8000
