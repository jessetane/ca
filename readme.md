# ca
Notes on how to configure and use openssl to set up your very own certificate authority for testing and debugging. A configuration file (`req.cnf`) is included that shows how to create certificate signing requests using the subjectAlternateName field. Please note that this is a very basic example and is not appropriate for production use of any kind.

## Creating a self-signed root certificate for the CA
Assumes you have already cloned this repo. You should only have to do these steps once. After completion you may want to register the resulting root certificate with your operating system - the links below may be helpful:
* [Linux](https://golang.org/src/crypto/x509/root_linux.go)
* [OS X](https://support.apple.com/kb/PH18677?locale=en_US)
* [Windows](https://technet.microsoft.com/en-us/library/cc754841)

##### 1. Create a working directory
``` shell
$ mkdir work
```

##### 2. Generate a 2048 bit RSA key
``` shell
$ openssl genrsa -out work/key.pem 2048
```

##### 3. Create a CSR
Edit `req.ca.cnf` to suit first...
``` shell
$ openssl req -new -config req.ca.cnf -key work/key.pem -out work/csr.pem
```

##### 4. Review the CSR
``` shell
$ openssl req -noout -text -in work/csr.pem
```

##### 5. Create a self-signed certificate from the CSR
``` shell
$ openssl x509 -req -days 36500 -sha512 -signkey work/key.pem -in work/csr.pem -out work/cert.pem
```

##### 6. Review the certificate
``` shell
$ openssl x509 -noout -text -in work/cert.pem
```

##### 7. Save your work
``` shell
$ mv work state/root
```

## Generating CSR's and signing them with the CA's key

##### 1. Set up a database and serial number tracking
You should only have to do this once.
``` shell
$ touch state/database.txt
$ echo "01" > state/serial.hex
```

##### 2. Generate a 2048 bit RSA key
``` shell
$ openssl genrsa -out work/key.pem 2048
```

##### 3. Create a CSR
Edit `req.cnf` to suit first...
``` shell
$ openssl req -config req.cnf -new -key work/key.pem -out work/csr.pem
```

##### 4. Review the CSR
``` shell
$ openssl req -noout -text -in work/csr.pem
```

##### 5. Create and sign a certificate for the CSR
``` shell
$ openssl ca -config ca.cnf -notext -in work/csr.pem -out work/cert.pem
```

##### 6. Review the certificate
``` shell
$ openssl x509 -noout -text -in work/cert.pem
```

##### 7. Save your work
``` shell
$ mv work state/issued/common-name.tld
```

## License
Public domain
