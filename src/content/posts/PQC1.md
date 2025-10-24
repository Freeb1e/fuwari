---
title: PQC leaning note 1
published: 2025-10-24
description: A simple example of a Markdown blog post.
tags: [PQC]
category: PQC
draft: false
---

This note summarizes the core steps of Kyber's public-key encryption (K‑PKE) scheme. Notation: matrices/vectors over a polynomial ring modulo q; $A$ is a matrix; $s, e, y$ are short polynomial vectors; $\mu$ is the message; $\rho$ is the seed to expand $A$.

## Key Generation

1) Sample secret and error from the centered binomial distribution (SamplePolyCBD): $s, e \leftarrow \text{CBD}$.  
2) Expand the public matrix $A$ from seed $\rho$.  
3) Compute

	$$ t \;=\; A^{T} \cdot s \; + \; e. $$

4) Secret key: $s$.  
5) Public key: $(t,\; \rho)$.

## Encryption

1) Re-expand $A$ from the public seed $\rho$.  
2) Sample a temporary secret and noises: $y, e_1, e_2 \leftarrow \text{CBD}$.  
3) Compute (typically in the NTT domain):

	$$ u \gets A^{T} \cdot y + e_1. $$

4) Compute the second component:

	$$ v \gets t^{T} \cdot y + e_2 + \mu. $$

5) Compress $u$ and $v$ to obtain $c_1$ and $c_2$.  
6) Output ciphertext $(c_1 \;\|\; c_2)$.

## Decryption

1) Decompress $c_1$ and $c_2$ to obtain $u'$ and $v'$.  
2) Compute the inner product with the secret:

	$$ u' \cdot s \;=\; (A^{T} \cdot y + e_1) \cdot s \;=\; (A^{T} \cdot s) \cdot y + e_1 \cdot s \;=\; t^{T} \cdot y + e_1 \cdot s. $$

3) Subtract to cancel the key-dependent term and isolate the message:

	$$ \omega \gets v' - (u' \cdot s) \;=\; (\mu + e_2) - e_1 \cdot s. $$

4) Apply the (de)compression/rounding rule to remove the noise $e_2 - e_1\cdot s$ and recover the plaintext $\mu$.

---

Notes

- All operations are modulo $q$ and in the respective polynomial ring; many multiplications are implemented in the NTT domain for efficiency.  
- $\rho$ is a short seed used to deterministically expand $A$ (saves public key size).  
- Compression is lossy by design but chosen so that decryption can still recover $\mu$ with overwhelming probability.