# syntax=docker/dockerfile:1

ARG RUST_VERSION=1.85.0
ARG APP_NAME=shipping

FROM rust:${RUST_VERSION}-alpine AS build
ARG APP_NAME
WORKDIR /app

# Ensure Cargo builds into a writable target directory.
ENV CARGO_TARGET_DIR=/app/target

# Install build dependencies.
RUN apk add --no-cache clang lld musl-dev git protobuf protobuf-dev

# Mount the entire repository parts to preserve the layout:
#   - src/shipping is mounted at /app/src/shipping
#   - pb is mounted at /app/pb
RUN --mount=type=bind,source=src/shipping,target=src/shipping \
    --mount=type=bind,source=pb,target=pb \
    --mount=type=cache,target=/app/target/ \
    --mount=type=cache,target=/usr/local/cargo/git/db \
    --mount=type=cache,target=/usr/local/cargo/registry/ \
    cargo build --locked --release --manifest-path src/shipping/Cargo.toml && \
    cp ./target/release/$APP_NAME /bin/server

FROM alpine:3.18 AS final
ARG UID=10001
RUN adduser \
    --disabled-password \
    --gecos "" \
    --home "/nonexistent" \
    --shell "/sbin/nologin" \
    --no-create-home \
    --uid "${UID}" \
    appuser
USER appuser

COPY --from=build /bin/server /bin/
EXPOSE 7077
ENV SHIPPING_PORT=7077
CMD ["/bin/server"]
