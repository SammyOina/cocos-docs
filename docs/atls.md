# Attested TLS

For the relying party to send confidential data or code to the Agent, a secure channel must be established between them. The secure channel is established using attested TLS, which is a TLS connection enriched with the attestation report of the Agent.

In Cocos, the CVM acts as the server, and the Agent extends the X.509 certificate it uses for TLS with the attestation report. When generating the report, the Agent embeds the hash of its public key into the `report_data` field of the AMD SEV-SNP report. To maintain the freshness of the attestation report, a new certificate is regenerated with every connection, along with a fresh attestation report.

By combining the TLS certificate along with the attestation report, the relying party (the user of cocos-cli) can be assured that he is talking directly to the Agent inside the TEE, because the attestation report is bound to the certificate and the report has the guarantee of freshness, the relying party knows that the other side of the TLS connection is inside the TEE.

The entire process is illustrated in the picture below. The green color represents the trusted part of the system, while the red represents the untrusted part.

![Attested TLS](/img/attestation/atls.png)

The exact point at which the Agent certificate is sent during the TLS handshake process is shown below.

![TLS handshake](/img/attestation/tls.png)

One of the key components of the verification process is the attestation policy. The CLI uses the attestation policy to verify the fields of the attestation report (vTPM and SEV-SNP). One example of the attestation report is show below:

```json
{
  "pcr_values": {
    "sha256": {
      "0": "71e0cc99e4609fdbc44698cceeda9e5ecb2f74fe07bd10710d5330e0eb6bd32b",
      "1": "a40e22460c21d2450367ca70c751ec0ae5ae1072994a131287a96eadc295603b",
      "2": "3d458cfe55cc03ea1f443f1562beec8df51c75e14a9fcf9a7234a13f198e7969",
      "3": "3d458cfe55cc03ea1f443f1562beec8df51c75e14a9fcf9a7234a13f198e7969",
      "4": "e16812b9181e13078b29f2e4844be7087f9e1bbffc3cb4171d2813580cafdb8d",
      "5": "a5ceb755d043f32431d63e39f5161464620a3437280494b5850dc1b47cc074e0",
      "6": "3d458cfe55cc03ea1f443f1562beec8df51c75e14a9fcf9a7234a13f198e7969",
      "7": "70d12f32fdb109ba0960697b5a8d5d8d860b004a757fe2471be2c2a19ec1a765",
      "9": "2add30b0f2b31480ee5eb802c436cfffe77ceebc6009e063e84fc6a6ef2c05ac"
    },
    "sha384": {
      "0": "ff93a763afde2c4a152d4843d9fcabe73a70d4f34bf8861845f2ab08440c1f0742b5882ed7f2524e38a3a6e40fbcdfca",
      "1": "c9b3bcc22d856cbc5be2a2bf72d81819df325db083cfea20e84d082a87f44d643e6fca98f29eb3cce4c87eed2dbca2e5",
      "2": "518923b0f955d08da077c96aaba522b9decede61c599cea6c41889cfbea4ae4d50529d96fe4d1afdafb65e7f95bf23c4",
      "3": "518923b0f955d08da077c96aaba522b9decede61c599cea6c41889cfbea4ae4d50529d96fe4d1afdafb65e7f95bf23c4",
      "4": "d18d213c26e7bc309e52448bde2f0a8ef86be388223f64f85c4e0c625f1e0a7f8c901d4f7c98f8445730bc63c4dfa88d",
      "5": "c50b529497c7f441ea47305587d6ce83e2e31f7b4fab6c13dc0b0c3c900e1d0caf0768321100927862df142bf0465ee4",
      "6": "518923b0f955d08da077c96aaba522b9decede61c599cea6c41889cfbea4ae4d50529d96fe4d1afdafb65e7f95bf23c4",
      "7": "ea40cbd8f51eed103d75821340e71fa3c0cfde3e75c360b4c9aca534b7fed021e12f8890acef36ccfe12b33ea4111576",
      "9": "02556c6b494abaf21481def35b38574e80dc68f20ceb8385f78a5ad4ecfbab60f9fcfca7c69f09a081fdd4ca13f3c14d"
    }
  },
  "policy": {
    "chip_id": "GrFqtQ+lrkLsjBslu9pcC6XqkrtFWY1ArIQ+I4gugQIsvCG0qekSvEtE4P/SLSJ6mHNpOkY0MHnGpvz1OkV+kw==",
    "family_id": "AAAAAAAAAAAAAAAAAAAAAA==",
    "host_data": "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA=",
    "image_id": "AAAAAAAAAAAAAAAAAAAAAA==",
    "measurement": "oDYo4e98Da2Fy73nDVZmxiWiz+5gnxae7NMRtdfnwpbBuVYZsI0mynz3fpfe+YIX",
    "minimum_build": 8,
    "minimum_launch_tcb": 15352208179752599555,
    "minimum_tcb": 15352208179752599555,
    "minimum_version": "1.55",
    "permit_provisional_firmware": true,
    "policy": 196608,
    "product": {
      "name": 1
    },
    "report_id_ma": "//////////////////////////////////////////8=",
    "require_author_key": false,
    "require_id_block": false,
    "vmpl": 2
  },
  "root_of_trust": {
    "check_crl": true,
    "disallow_network": false,
    "product": "Milan",
    "product_line": "Milan"
  }
}
```

The relying party uses the Cocos CLI to verify the self-signed (or CA-signed) certificate and the attestation report that is part of it. Successful verification proves to the relying party that the CVM is in a good state and that the relying party is indeed communicating with the Agent.

The array `pcr_values` represents the expected (golden) PCR values that must match the PCR values in the vTPM attestation report. The `policy` and `root_of_trust` sections describe the reference values for the fields in the SEV-SNP attestation report.

It is also possible to use mutual attested TLS, which is a combination of mutual TLS and attested TLS.
