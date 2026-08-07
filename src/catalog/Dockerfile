# # Build Stage
# FROM public.ecr.aws/amazonlinux/amazonlinux:2023 AS build-env

# # We tell DNF not to install Recommends and Suggests packages, which are
# # weak dependencies in DNF terminology, thus keeping our installed set of
# # packages as minimal as possible.
# RUN dnf --setopt=install_weak_deps=False install -q -y \
#     git \
#     golang \
#     && \
#     dnf clean all

# RUN mkdir -p "${GOPATH}/src" "${GOPATH}/bin" /appsrc

# WORKDIR /appsrc

# ENV GOPROXY=https://goproxy.io,direct

# COPY go.mod go.sum ./
# RUN go mod download

# COPY . .

# RUN go build -o main main.go

# # Final stage
# FROM public.ecr.aws/amazonlinux/amazonlinux:2023

# ENV APPUSER=appuser
# ENV APPUID=1000
# ENV APPGID=1000

# # We tell DNF not to install Recommends and Suggests packages, which are
# # weak dependencies in DNF terminology, thus keeping our installed set of
# # packages as minimal as possible.
# RUN dnf --setopt=install_weak_deps=False install -q -y \
#     shadow-utils \
#     && \
#     dnf clean all

# RUN useradd \
#     --home "/app" \
#     --create-home \
#     --user-group \
#     --uid "$APPUID" \
#     "$APPUSER"

# WORKDIR /app
# USER appuser

# COPY --chown=appuser:appuser --from=build-env /appsrc/main /app/

# COPY ./ATTRIBUTION.md ./LICENSES.md

# ENV GIN_MODE=release

# ENTRYPOINT ["/app/main"]


# ---------- Stage 1 : Build ----------
FROM golang:1.23 AS builder

WORKDIR /workspace

COPY . .

WORKDIR /workspace/src/catalog

RUN go mod download

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -o catalog .

# ---------- Stage 2 : Runtime ----------
FROM alpine:3.21

RUN apk --no-cache add ca-certificates

WORKDIR /app

COPY --from=builder /workspace/src/catalog/catalog .

EXPOSE 8080

ENTRYPOINT ["./catalog"]
