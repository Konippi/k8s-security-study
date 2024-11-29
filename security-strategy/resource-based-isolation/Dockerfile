FROM golang:1.23.3-bullseye AS builder

WORKDIR /go/src/app

COPY go.mod ./
RUN go mod download

COPY . .
ARG CGO_ENABLED=0
ARG GOOS=linux
ARG GOARCH=amd64
RUN go build -o /go/bin/app

FROM gcr.io/distroless/static-debian11

COPY --from=builder /go/bin/app /

EXPOSE 8080

CMD ["/app"]
