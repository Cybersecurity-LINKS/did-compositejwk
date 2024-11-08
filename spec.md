# DID Method Specification

`did:compositejwk` is a deterministic transformation of a `compositeJwk` into a DID Document.

## compositeJwk

The `compositeJwk` is a new [Verification Material](https://www.w3.org/TR/did-core/#verification-material) property introduced to handle Post-Quantum/Traditinal (PQ/T) hybrid keys.
This object contains the PQ and traditional public keys, both JWK encoded, and the algId string representing the name of algorithms used to generate the hybrid signature.


```json
"compositeJwk": {
  "algId": ".. composite key OID ..",
  "pqPublicKey": {
     ".. PQ JWK encoded key .."
  },
  "traditionalPublicKey": {
    ".. Traditional JWK encoded key .."
  }
}
```

#### Example
```json
"compositeJwk": {
  "algId": "id-MLDSA44-Ed25519-SHA512",
  "pqPublicKey": {
    "kty": "ML-DSA",
    "alg": "ML-DSA-44",
    "kid": ".. key thumbprint ..",
    "pub": ".. encoded public key .."
  },
  "traditionalPublicKey": {
    "crv": "Ed25519",
    "x": ".. x coordinate ..",
    "kty": "OKP",
    "kid": ".. key thumbprint .."
  }
}
```

## DID Format

```
did-compositejwk-format   := did:compositejwk:<base64url-value>
base64url-value  := [A-Za-z0-9_-]+
```

The `base64url-value` is a [base64url](https://datatracker.ietf.org/doc/html/rfc4648#section-5) encoded `compositeJwk`.


## DID Operations

### Create

#### To create the DID:

1. Generate or load a `compositeJwk`
1. Serialize it into a UTF-8 string
1. Encode that string using base64url
1. Attach the prefix `did:compositejwk:`

#### To create the DID Document:

The `compositeJwk` is used to generate the [DID Document](https://www.w3.org/TR/did-core/#dfn-did-documents) in the following form with the [base64url](https://datatracker.ietf.org/doc/html/rfc4648#section-5) encoded value replacing `${base64url-value}`, and the `compositeJwk` structure replacing `${composite-jwk}`:

```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/jws-2020/v1"
  ],
  "id": "did:compositejwk:${base64url-value}",
  "verificationMethod": [
    {
      "id": "did:compositejwk:${base64url-value}#0",
      "type": "CompositeSignaturePublicKey",
      "controller": "did:compositejwk:${base64url-value}",
      "compositeJwk": "${composite-jwk}"
    }
  ]
}
```


#### To create the DID URL:

Since `did:compositejwk` only contains a single composite public key (a PQ and a Traditional public key), the DID URL fragment identifier is always a fixed `#0` value.

If the PQ and/or Traditional JWK contains a `kid` value, it is _not_ used as the reference, `#0` is the only valid value.


### Read

1. Remove the prefix `did:compositejwk:`
2. Decode the remaining string using [base64url](https://datatracker.ietf.org/doc/html/rfc4648#section-5)
3. Parse the decoded string as UTF-8 JSON
4. Validate the `compositeJwk` properties
5. Generate a DID Document using the composite public key 

### Update

Not supported.

### Deactivate

Not supported.

## Security and Privacy Considerations

Since the `did:compositejwk` method is similar to the `did:jwk` method, see also [did:jwk](https://github.com/quartzjer/did-jwk/blob/main/spec.md#security-and-privacy-considerations)

### Security

work in progress

### Privacy

Using the same `compositeJwk` with several different entities allows them to correlate the use to the same subject.
