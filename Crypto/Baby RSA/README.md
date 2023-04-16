The script is a textbook RSA encrypting the flag $m$ with two coprime public exponents. The modulus $n$, the public exponents $e_1, e_2$, and their corresponding ciphertext $c_1, c_2$ are all given. Our goal is to recover the flag $m$. 

Recall that RSA is hard precisely because finding the private exponent, i.e. inverse of the public exponent in $\mathbb Z_n^\times$, is hard. However, if we have two coprime public exponents encrypting the same message, we can exploit the [Bézout's identity](https://en.wikipedia.org/wiki/B%C3%A9zout%27s_identity) so we don't have to find the private exponents. 

Bézout's identity tell us that there exist $a,b\in \mathbb Z$ such that $$ae_1 + be_2 = 1$$
and moreover finding $a$ and $b$ is fast using [extended Euclidean algorithm](https://en.wikipedia.org/wiki/Extended_Euclidean_algorithm). With $a$ and $b$ in hand, we have our flag $$m = m^1 = m^{ae_1 + be_2} = m^{ae_1}m^{ae_2} = (m^{e_1})^a(m^{e_2})^b = c_1^ac_2^b$$
where the above computation happens in the ring $\mathbb Z_n$. 

Using [SageMath](https://www.sagemath.org/), we can find Bezout coefficients with a single line
```python
(_, a, b) = xgcd(e_1, e_2)
```
and the rest is straightforward: raise $c_1$ and $c_2$ to their corresponding power, compute the product, and take modulo $n$. 