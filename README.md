# Webcryptobox
WebCrypto compatible encryption libraries and CLIs.

## Libraries
* [Webcryptobox Rust](https://github.com/jo/webcryptobox-rs)
* [Webcryptobox JavaScript](https://github.com/jo/webcryptobox-js)
* [Webcryptobox Bash](https://github.com/jo/webcryptobox-sh)


## CLIs
* [wcb Rust](https://github.com/jo/wcb-rs)
* [wcb JavaScript](https://github.com/jo/wcb-js)
* [wcb Bash](https://github.com/jo/wcb-sh)


## Architecture
Webcryptobox provides opinionated cipher selection and a few abstractions so you don't have to worry about too much details. It is heavily inspired by NaCL, but is focused on [WebCrypto](https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API) and [OpenSSL](https://www.openssl.org/) compatibility. That means you can implement crypto systems for the web which are also easily usable from within other systems.

For understanding the way Webcryptobox works, best look at the [Webcryptobox Bash](https://github.com/jo/webcryptobox-sh) implementation. It basically wraps calls to `openssl` and massages data on its way in and out with `xxd`.

### Cipher
We use Elliptic-curve Diffie–Hellman (**ECDH**) key agreement with curve **P-521** aka secp521r1 or ansip521r1. Symmetric encryption is done with Advanced Encryption Standard (**AES**) with a key length of **256** bits in Cipher Block Chaining Mode (**CBC**), in short: `ECDH-P-521-AES-256-CBC`.

### Ciphertext & Initialization Vectors
CBC uses initialisation vectors (iv) aka nonce, which should be randomly chosen. In contrary to other modes like Galois/Counter Mode (GCM), the iv is a) longer and b) does not leak the entire key in case of a nonce reusage.

Webcryptobox generates ciphertexts which are composed of the iv plus the encrypted content. This means there is no separation of the nonce and the ciphertext, so you can easily distribute the encrypted messages in a single file.

Ciphertext is Base64 encoded.

### Password Derivation
For scenarios where you'd like to authenticate with a key pair against a webservice, Webcryptobox provides a password key derivation. Because we use a curve which provides enough derived material, we can generate both an AES key (256 bits long) plus a password, which can be maximal 256 bits long (in fact it could be a little longer, most of the time 264 bits, but we clip at 256 for simplicity).
That means if an attacer would gain the password, the AES key would not be leaket.

### Key Exchange
Webcryptobox uses the Privacy Enhanced Mail (PEM) file format for key exchange. You can exchange public keys, private keys and encrypted private keys. Private keys can be exchanged between peers by using a derived passphrase, which is a 256 bit hex representation of derived bits. That passphrase is used as an input for a Password-Based Key Derivation Function 2 (PBKDF2) with 64000 iterations and SHA-256 hashing.


## API
Webcryptobox provides functions to

* generate AES keys
* encrypt/decrypt AES messages

* generate EC private keys
* get public key from private key

* calculate fingerprint of public key in either SHA-1 or SHA-256 format

* encrypt/decrypt with key pair

* derive AES key from key pair
* derive password from key pair

* encrypt/decrypt private key with passphrase
* encrypt/decrypt private key with key pair


## License
This project is licensed under the Apache 2.0 License.

© 2022 Johannes J. Schmidt
