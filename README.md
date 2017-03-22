Nginx Compliance Config
=======================

`nginx.conf`

Overview
--------

Example configuration of Nginx for security compliance.

* PCI-DSS
* HIPPA
* NIST
* Common best practices

Reference Platform Requirements
------------------------------

* Nginx ~1.10 (v1.3+ required)
* CentOS7 _(This shouldn't matter)_

Website SSL Config/Vulnerability Scanners
----------------------------------------

### High-tech Bridge - SSL Server Security Test

>[https://www.htbridge.com/ssl]

![SSL Scan Results](img/hightech-ssl-server-security-results-01.png "High-tech Bridge - SSL Server Security Test")

* A+
* PCI-DSS
* HIPPA
* NIST compliance

### High-tech Bridge - Web Server Security Test

>[https://www.htbridge.com/websec]

![WebSec Scan Results](img/hightech-web-server-security-results-01.png "High-tech Bridge - Web Server Security Test")

* A
* HTTP/1.1 and HTTP2
* Webserver does not send detailed information about its version
* `STRICT-TRANSPORT-SECURITY` - The header is properly set.
* `PUBLIC-KEY-PINS` - The header was not sent by the server.
* `X-FRAME-OPTIONS` - The header is properly set.
* `X-XSS-PROTECTION` - The header is properly set.
* `X-CONTENT-TYPE-OPTIONS` - The header is properly set.
* `CONTENT-SECURITY-POLICY` - Content-Security Policy is enforced. _Some directives have values that are too permissive, like wildcards._

### SSL Labs

>[https://globalsign.ssllabs.com]

![SSL Labs Scan Results](img/ssllabs-results-01.png "SSL Labs - Scan Results")

* A+
* `Certificate`: 100/100
* `Support Protocol`: 98/100
* `Key Exchange`: 90/100
* `Cipher Strength`: 90/100
* HTTP Strict Transport Security (HSTS) with long duration deployed on this server.
* TLS v1.1 + v1.2
* Secure Renegotiation  Supported
* Secure Client-Initiated Renegotiation No
* Insecure Client-Initiated Renegotiation No
* POODLE (SSLv3)  No, SSL 3 not supported
* POODLE (TLS)  No
* Downgrade attack prevention Yes, TLS_FALLBACK_SCSV supported
* SSL/TLS compression No
* RC4 No
* Heartbeat (extension) Yes
* Heartbleed (vulnerability)  No
* Forward Secrecy Yes
* ALPN  No
* NPN Yes   h2 http/1.1
* Session resumption (caching)  Yes
* Session resumption (tickets)  Yes
* OCSP stapling Yes
* Strict Transport Security (HSTS)  Yes 
* Incorrect SNI alerts  No
* Uses common DH primes No
* DH public server param (Ys) reuse No
* HTTP server signature nginx

### DigiCert

> [https://www.digicert.com/help/]

* All green checks
* OCSP Staple:  Good
* OCSP Origin:  Good
* CRL Status: Good

## SSL Cert/Pem File Notes

### ssl_certificate

Single CRT files from certificate authorities cannot be used. In this configuration we need a PEM format x509 certificate file. This includes the signed CRT and all intermediary CA certificates but not the External Root CA certificate. Adding this will result in an error.

Creation Example:
~~~
cat www.example.com.crt COMODORSADomainValidationSecureServerCA.crt COMODORSAAddTrustCA.crt > /etc/ssl/ssl-bundle.pem
~~~

### ssl_certificate_key

This is simply the private key file you used to create the CSR request before sending it off to the CA to be signed.

Creation Example:
~~~
openssl genrsa -out www.example.com.key 2048
~~~

### ssl_dhparam

The Diffie-Hellman algorithm provides the capability for two communicating parties to agree upon a shared secret between them. A unique 2048+ bit DH secret is required and can be easily created with openssl.

Creation Example:
~~~
openssl dsaparam -out /etc/ssl/dsaparam.pem 4096 # A 4096 bit key will be considered strong for some time.
~~~

### ssl_trusted_certificate

The trusted certificate file is used internally by Nginx for use with OCSP. The PEM format file is the signed CRT, combined with all intermediary CA certs, combined with the final external CA root cert. We use the previously created ssl-bundle.pem file and combine it with the CA provided CA root authority cert. If they don't provide it google is your friend.

Creation Example:
~~~
cat /etc/ssl/ssl-bundle.pem /etc/ssl/www.example.com.externalcaroot > /etc/ssl/ca.trust
~~~
