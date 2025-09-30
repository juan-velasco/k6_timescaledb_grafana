# ---------- Etapa 1: Builder ----------
FROM ubuntu:22.04 AS builder

# Dependencias para compilar
RUN apt-get update && apt-get install -y wget git tar build-essential \
    && rm -rf /var/lib/apt/lists/*

# Instalar Go
ENV GOLANG_VERSION=1.23.2
RUN wget https://go.dev/dl/go${GOLANG_VERSION}.linux-amd64.tar.gz && \
    tar -C /usr/local -xzf go${GOLANG_VERSION}.linux-amd64.tar.gz && \
    rm go${GOLANG_VERSION}.linux-amd64.tar.gz
ENV PATH=$PATH:/usr/local/go/bin

# Instalar xk6
RUN go install go.k6.io/xk6/cmd/xk6@latest
ENV PATH=$PATH:/root/go/bin

# Compilar k6 con la extensión de TimescaleDB
RUN xk6 build --with github.com/grafana/xk6-output-timescaledb

# ---------- Etapa 2: Imagen final ----------
FROM ubuntu:22.04

# Copiar solo el binario compilado
COPY --from=builder /k6 /usr/local/bin/k6

# Verificar que k6 funciona
RUN k6 version

ENTRYPOINT ["k6"]