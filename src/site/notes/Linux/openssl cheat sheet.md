---
{"dg-publish":true,"permalink":"/linux/openssl-cheat-sheet/"}
---

# Converting a PFX file to PEM and Key via openssl
Export the private key
`openssl pkcs12 -in aa.pfx -out aa.key -nocerts -nodes`

take out the encryption from the private key
`openssl rsa -in aa.key -out aa_s.key`

export the ssl cert (normal cases)
`openssl pkcs12 -in aa.pfx -out aa.pem -nokeys -clcerts`