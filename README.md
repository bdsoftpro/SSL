**Check Openssl Version**  
OpenSSL Version  
openssl version -a
  
  
**Linux / Unix servers:**  
openssl genrsa -des3 -out <private-key-file-name.key> 2048
  
**Windows servers:**  
openssl genrsa -out <private-key-file-name.key>  2048
  
## Generate key or certificate
**Generate a Private Key and a CSR**  
openssl req -newkey rsa:2048 -nodes -keyout webrtc.com.key -out webrtc.com.csr
  
**Generate a CSR from an Existing Private Key**  
openssl req -key webrtc.com.key -new -out webrtc.com.csr
  
**Generate a CSR from an Existing Certificate and Private Key**  
openssl x509 -in webrtc.com.crt -signkey webrtc.com.key -x509toreq -out webrtc.com.csr
  
**Generate a Self-Signed Certificate**  
openssl req -newkey rsa:2048 -nodes -keyout webrtc.com.key -x509 -days 365 -out webrtc.com.crt
  
**Generate a Self-Signed Certificate from an Existing Private Key**  
openssl req -key webrtc.com.key -new -x509 -days 365 -out webrtc.com.crt
  
**Generate a Self-Signed Certificate from an Existing Private Key and CSR**  
openssl x509 -signkey webrtc.com.key -in webrtc.com.csr -req -days 365 -out webrtc.com.crt
  
## View Certificates
**View CSR Entries**  
openssl req -text -noout -verify -in webrtc.com.csr
  
**View Certificate Entries**  
openssl x509 -text -noout -in webrtc.com.crt
  
**Verify a Certificate was Signed by a CA**  
openssl verify -verbose -CAFile ca.crt webrtc.com.crt
  
## Private Keys
**Create a Private Key**  
openssl genrsa -des3 -out webrtc.com.key 2048
  
**Verify a Private Key**  
openssl rsa -check -in webrtc.com.key
  
**Verify a Private Key Matches a Certificate and CSR**  
openssl rsa -noout -modulus -in webrtc.com.key | openssl md5  
openssl x509 -noout -modulus -in webrtc.com.crt | openssl md5  
openssl req -noout -modulus -in webrtc.com.csr | openssl md5  
  
**Encrypt a Private Key**  
openssl rsa -des3 -in unencrypted.key -out encrypted.key
  
**Decrypt a Private Key**  
openssl rsa -in encrypted.key -out decrypted.key
  
## Convert Certificate Formats
**Convert PEM to DER**  
openssl x509 -in domain.crt -outform der -out domain.der
  
**Convert DER to PEM**  
openssl x509 -inform der -in domain.der -out domain.crt
  
**Convert PEM to PKCS7**  
openssl crl2pkcs7 -nocrl -certfile domain.crt -certfile ca-chain.crt -out domain.p7b
  
**Convert PKCS7 to PEM**  
openssl pkcs7 -in domain.p7b -print_certs -out domain.crt
  
**Convert PEM to PKCS12**  
openssl pkcs12 -inkey domain.key -in domain.crt -export -out domain.pfx
  
**Convert PKCS12 to PEM**  
openssl pkcs12 -in domain.pfx -nodes -out domain.combined.crt
  
## Create Certificate chain and sign certificates using Openssl  
**1. Generate Root Certificate key**  
openssl genrsa -out RootCA.key 4096
  
**2. Generate Root certificate**  
openssl req -new -x509 -days 1826 -key RootCA.key -out RootCA.crt
  
**3. Generate Intermediate CA certificate key**  
openssl genrsa -out Intermediate.key 4096
  
**4. Generate Intermediate CA CSR**  
openssl req -new -key Intermediate.key -out Intermediate.csr
  
**5. Sign the Intermediate CA by the Root CA**  
openssl req -x509 -days 1000 -in Intermediate.csr -CA RootCA.crt -CAkey RootCA.key -CAcreateserial Intermediate.crt
  
**6. Generate Server certificate key**  
openssl genrsa -out RootCA.key 2048
  
**7. Generate Server certificate CSR**  
openssl req -new -key Server.key -out Server.csr
  
**8. Sign the Server Certificate CSR using the Intermediate CA**  
openssl req -x509 -days 1000 -in Server.csr -CA Intermediate.crt -CAkey RootCA.key -set_serial 0101 -out Server.crt -sha1
  
The certificate in the trusted store in Linux  
cp *.crt  /usr/local/share/ca-certificates/  
update-ca-certificates  
  
Verify the certificate.  
openssl x509 –in Server.crt –noout –text | grep 'host.local'  
  
  
