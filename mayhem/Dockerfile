FROM golang:1.17.13 as fuzzbuilder
ARG project='lz4-go'
ARG fuzzTargetPath='fuzz'

ADD . ${project}
WORKDIR /go/${project}/${fuzzTargetPath}

RUN go get -u github.com/dvyukov/go-fuzz/go-fuzz@latest github.com/dvyukov/go-fuzz/go-fuzz-build@latest
RUN GO_ENABLED=0 go-fuzz-build -libfuzzer -func=FuzzCompressBlock -o /compression-fuzz.a .

FROM silkeh/clang:latest as sanitizer
COPY --from=fuzzbuilder /compression-fuzz.a compression-fuzz.a
RUN clang -fsanitize=fuzzer /compression-fuzz.a -o /compression-fuzz.libfuzzer

FROM ubuntu:20.04
COPY --from=sanitizer /compression-fuzz.libfuzzer /
