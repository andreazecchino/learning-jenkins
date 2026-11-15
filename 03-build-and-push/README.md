# Build and Push Docker Images

In this lesson we will run Jenkins in Docker Compose with Docker-in-Docker and a private, TLS-secured Docker registry. Jenkins builds images and pushes them to the registry using an encrypted connections with certificates from a local Certificate Authority (CA).

## Services

| Service | Purpose |
|---------|---------|
| controller | Jenkins, built from the included Dockerfile |
| dind | A privileged Docker daemon used by Jenkins to build and push images. Jenkins connects to it over TLS |
| registry.local | A private Docker registry served over HTTPS |

The services share a network named `jenkins-network`.

Named volumes preserve Jenkins data, Docker client certificates, and registry image data across container restarts.

## Directory layout

```
.
├── certs/              # Generated locally, not committed
├── Dockerfile
├── Jenkinsfile
├── docker-compose.yml
└── README.md
```

## Setting up a local CA and service Certificate

Create a folder for the certificates:

```bash
mkdir -p certs
cd certs
```

Generate the CA's private key:

```bash
openssl genrsa -out ca.key 4096
```

Create the CA certificate:

```bash
openssl req -x509 -new -nodes -key ca.key -sha256 -days 3650 -out ca.crt
```

Those steps create the Root CA. Keep `ca.key` private. Anyone with this key can issue certificates your machine will trust.

To create a certificate for a specific service, we must define the Subject Alternative Names (SAN).

Generate the configuration file (es: `registry.ext`):

```ini
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = registry.local
```

Generate a key and a CSR (Certificate Signing Request):

```bash
openssl req -x509 -new -nodes -key ca.key -sha256 -days 3650 -out ca.crt -subj "/CN=Local Development CA" -addext "basicConstraints=critical,CA:TRUE" -addext "keyUsage=critical,keyCertSign,cRLSign"
```

Certificate sign:

```bash
openssl x509 -req -in registry.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out registry.crt -days 365 -sha256 -extfile registry.ext
```

The certificates are now ready to be used inside Docker-in-Docker and Registry Container.

## Start the stack

```bash
docker compose up -d --build
```

Jenkins is then available at `http://localhost:8080`. The post-installation steps are the same in [installation](../00-installation/README.md) lesson.

## Registry TLS test

Add `registry.local` to the host’s hosts file so it resolves to the machine running the registry:

```bash
echo "127.0.0.1 registry.local" >> /etc/hosts # run as root
```

From the host, the registry is available at `registry.local:5000`. The commands below use `certs/ca.crt` to verify the registry’s certificate without installing the CA certificate in the system trust store. Run them from the project root:

```bash
openssl s_client -connect registry.local:5000 \
  -servername registry.local \
  -CAfile certs/ca.crt \
  -verify_return_error </dev/null

curl --cacert certs/ca.crt https://registry.local:5000/v2/_catalog
curl --cacert certs/ca.crt https://registry.local:5000/v2/<image>/tags/list
```

The catalog endpoint lists repository names. Replace `<image>` with the repository name to list its tags.

## Cleaning up

```bash
docker compose down     # stop and remove containers and network, keep volumes
docker compose down -v  # stop containers and delete all data
```

