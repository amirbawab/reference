# OpenSSH

## Encrypt
```
$ openssl rsautl -encrypt -in $PLAINTEXT -out $PLAINTEXT.encrypt -pubin -inkey keys/pubkey.pem
```

## Decrypt
```
$ openssl rsautl -decrypt -in $ENCRYPTED -out $PLAINTEXT -inkey keys/privkey.pem
```
