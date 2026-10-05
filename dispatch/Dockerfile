FROM golang:1.27-bookworm AS build

WORKDIR /src

COPY *.go .

RUN go mod init dispatch \
    && go mod tidy \
    && CGO_ENABLED=0 go build -trimpath -ldflags="-s -w" -o /dispatch .

FROM debian:bookworm-slim

COPY --from=build /dispatch /dispatch

USER 65532:65532
CMD ["/dispatch"]
