# orthanc-auth-demo

Download the project to 

## Provision Certificate
```sh
cd certs

export IssuerComName=issuer.orthweb.digihunch.com
export ServerComName=orthweb.digihunch.com
export ClientComName=dcmclient.orthweb.digihunch.com

openssl req -x509 -sha256 -newkey rsa:4096 -days 365 -nodes -subj /C=CA/ST=Ontario/L=Waterloo/O=Digihunch/OU=Imaging/CN=$IssuerComName/emailAddress=info@digihunch.com -keyout ca.key -out ca.crt

openssl req -new -newkey rsa:4096 -nodes -subj /C=CA/ST=Ontario/L=Waterloo/O=Digihunch/OU=Imaging/CN=$ServerComName/emailAddress=orthweb@digihunch.com -addext extendedKeyUsage=serverAuth -addext subjectAltName=DNS:orthweb.digihunch.com,DNS:$IssuerComName -keyout server.key -out server.csr

openssl x509 -req -sha256 -days 365 -in server.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out server.crt

cat server.key server.crt ca.crt > $ServerComName.pem

openssl req -new -newkey rsa:4096 -nodes -subj /C=CA/ST=Ontario/L=Waterloo/O=Digihunch/OU=Imaging/CN=$ClientComName/emailAddress=dcmclient@digihunch.com -keyout client.key -out client.csr

openssl x509 -req -sha256 -days 365 -in client.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out client.crt
```

## Prepare DICOM images

Send with the following command using dcmtk. 
```sh
storescu -aet TESTER -aec ORTHANC -d +tls client.key client.crt -rc +cf ca.crt localhost 11112 IM-0001-0011.dcm
```

## Launch web viewer
