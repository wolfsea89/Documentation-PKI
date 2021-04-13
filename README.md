# pki-documantation
Create PKI consists of root CA and signing CA.

Create directories
=========
```
mkdir -p \
    ca/rootCA/ \
    ca/rootCA/private \
    ca/rootCA/db \
    ca/rootCA/archive \
    ca/signingCA/private \
    ca/signingCA/db \
    ca/signingCA/archive \
    crl \
    certs
chmod 700 ca/rootCA/private
chmod 700 ca/signingCA/private
```

Create Root CA
=========
- Create Root CA database
    ```
    touch ca/rootCA/db/rootCA.db
    touch ca/rootCA/db/rootCA.db.attr
    echo 01 > ca/rootCA/db/rootCA.crt.srl
    echo 01 > ca/rootCA/db/rootCA.crl.srl
    ```
- Create Root CA request
    ```
    openssl req -new \
        -config etc/rootCA.conf \
        -out ca/rootCA/rootCA.csr \
        -keyout ca/rootCA/private/rootCA.key
    ```
- Create Root CA certifica and selfsign
    ```
    openssl ca -selfsign \
        -config etc/rootCA.conf \
        -in ca/rootCA/rootCA.csr \
        -out ca/rootCA/rootCA.crt \
        -extensions root_ca_ext
    ```

Create Signing CA
=========
- Create Signing CA database
    ```
    touch ca/signingCA/db/signingCA.db
    touch ca/signingCA/db/signingCA.db.attr
    echo 01 > ca/signingCA/db/signingCA.crt.srl
    echo 01 > ca/signingCA/db/signingCA.crl.srl
    ```
- Create Signing CA request
    ```
    openssl req -new \
        -config etc/signingCA.conf \
        -out ca/signingCA/signingCA.csr \
        -keyout ca/signingCA/private/signingCA.key
    ```
- Root CA that issues the signing CA certificate
    ```
    openssl ca \
        -config etc/rootCA.conf \
        -in ca/signingCA/signingCA.csr \
        -out ca/signingCA/signingCA.crt \
        -extensions signing_ca_ext
    ```

Create Server Certificate
=========
- Create TLS server request
    ```
    openssl req -new \
        -config etc/server.conf \
        -out certs/server.example.org.csr \
        -keyout certs/server.example.org.key
    ```
- Create TLS server certificate
    ```
    openssl ca \
        -config etc/signingCA.conf \
        -in certs/server.example.org.csr \
        -out certs/server.example.org.crt \
        -extensions server_ext
    ```