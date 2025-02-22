###############
# Build Stage #
###############
FROM golang:1.22-alpine AS builder

WORKDIR /usr/src/app
COPY . .

RUN go mod download
RUN go build -o checkout ./

#################
# Final Stage   #
#################
FROM alpine:latest AS release

WORKDIR /usr/src/app
COPY --from=builder /usr/src/app/checkout ./

# -- REQUIRED ENVIRONMENT VARIABLES --
ENV CHECKOUT_PORT=8089
ENV SHIPPING_ADDR=http://shipping:50051
ENV PRODUCT_CATALOG_ADDR=http://product-catalog:50051
ENV CART_ADDR=http://cart:50051
ENV CURRENCY_ADDR=http://currency:50051
ENV EMAIL_ADDR=http://email:50051
ENV PAYMENT_ADDR=http://payment:50051
# (KAFKA_ADDR is optional unless you want Kafka connectivity)

EXPOSE 8089
ENTRYPOINT ["./checkout"]
