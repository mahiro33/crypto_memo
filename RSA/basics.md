# アルゴリズム

## 鍵生成
* 入力：セキュリティパラメータ $k$
* 出力：公開鍵 $(N, e)$、秘密鍵 $(p, q, d)$
* $k$に対応するサイズの2つの素数 $p, q$をランダムに選択する
* $N=pq, L=\phi(N)$として $L$と互いに素な正整数 $e$を選択し、 $d=e^{-1}\mod{L}$を計算する
  * $\phi(N)$はオイラー関数（後述）
  * $e=65537$ (0x10001)であることが多い
  * $p, q$が異なる素数であれば、 $L=(p-1)(q-1)$となる

## 暗号化

* 入力：平文 $m\in{Z_{N}}$
* 出力：暗号文 $c=m^{e}\mod{N}$

## 復号

* 入力：暗号文 $c\in{Z_{N}}$
* 出力：平文 $m=c^{d}\mod{N}$

# 正しく復号できる理由

* 整数 $N$に対して、 集合 $Z_{N}^{*}= \lbrace x \mid 1 \le x \le N-1, gcd(x, N) \rbrace$の要素数を オイラー関数 $\phi \lparen N \rparen$という
* $p, q$が異なる奇素数のとき、 $\phi \lparen p \rparen =p-1, \phi \lparen p^{e} \rparen =p^{e-1}(p-1), \phi \lparen pq \rparen =(p-1)(q-1)$である
* オイラーの定理
  * すべての $a\in{Z_{N}^*}$に対して、 $a^{\phi \lparen N \rparen} \equiv 1 \pmod{N}$が成り立つ
* $m\in{Z_{N}}$の場合、 $c^{d} \equiv m^{ed} \equiv m^{1+kL} \equiv m \cdot (m^{L})^{k} \equiv m \pmod{N}$となる


# 実装

```python
from Crypto.Util.number import getPrime, inverse, bytes_to_long, long_to_bytes

p = getPrime(1024)
q = getPrime(1024)
N = p * q
e = 0x10001
d = inverse(e, (p - 1) * (q - 1))

m = b"this_is_my_message"
c = pow(bytes_to_long(m), e, N)
print("ciphertext:", c)
dec_m = pow(c, d, N)
print("plaintext:", long_to_bytes(dec_m).decode())
# plaintext: this_is_my_message
```
