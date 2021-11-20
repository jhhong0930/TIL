### openssl을 이용하여 자체 서명 인증서 생성하기

```
1. 개인 키 생성
$ openssl genrsa -des3 -passout pass:패스워드 -out server.pass.key 2048
$ openssl rsa -passin pass:x -in server.pass.key -out server.key
$ rm server.pass.key

2. CSR 생성(Certificate Signing Request - 인증서 서명 요청)
$ openssl req -new -key server.key -out server.csr

3. 사설 인증서 파일 생성
$ openssl x509 -req -sha256 -days 365 -in server.csr -signkey server.key -out server.crt

4. keystore.p12 생성
$ openssl pkcs12 -export -in server.crt -inkey server.key -out keystore.p12 -name airpageserver -CAfile server.csr -caname root

5. 스프링 application 설정파일에 ssl 적용하기
server.port=443
server.ssl.enabled=true
server.ssl.key-store=keystore.p12위치
server.ssl.key-store-password=패스워드
server.ssl.key-store-type=PKCS12

6. EC2에 적용할 경우 keystore 파일이 application에 설정해둔 경로와 일치해야됨 ubuntu/이후
jar 파일 실행시 권한 필요 (sudo)
```

