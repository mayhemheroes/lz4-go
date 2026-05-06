FROM golang:1.25 AS builder
RUN apt-get update && apt-get install -y clang

ADD . lz4-go
WORKDIR /go/lz4-go/fuzz

RUN go install github.com/dvyukov/go-fuzz/go-fuzz@latest github.com/dvyukov/go-fuzz/go-fuzz-build@latest
RUN go get github.com/dvyukov/go-fuzz/go-fuzz-dep
RUN go-fuzz-build -libfuzzer -func=FuzzCompressBlock -o /compression-fuzz.a .
RUN clang -fsanitize=fuzzer /compression-fuzz.a -o /compression-fuzz.libfuzzer

FROM ubuntu:22.04
COPY --from=builder /compression-fuzz.libfuzzer /
