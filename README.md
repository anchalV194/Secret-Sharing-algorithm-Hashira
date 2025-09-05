# Secret-Sharing-algorithm-Hashira
---

### How the math works

1. **Secret sharing setup**

   * A secret $s$ is embedded as the constant term of a polynomial $P(x)$ of degree $k-1$.

     $$
     P(x) = a_0 + a_1x + a_2x^2 + \cdots + a_{k-1}x^{k-1}
     $$

     where $a_0 = s$.
   * Each share is $(x_i, y_i)$ where

     $$
     y_i = P(x_i)
     $$

2. **Your JSON input**

   * Gives you $n$ such shares in different bases (you must convert them to decimal first).
   * Example from testcase 1:

     ```
     "1": { "base": "10", "value": "4" }   → (x=1, y=4)
     "2": { "base": "2", "value": "111" }  → (x=2, y=7)
     "3": { "base": "10", "value": "12" }  → (x=3, y=12)
     "6": { "base": "4", "value": "213" }  → (x=6, y=39)
     ```

3. **Interpolation**

   * You need $k$ shares to reconstruct the polynomial uniquely (since $k$ points determine a degree $k-1$ polynomial).

   * Use **Lagrange interpolation formula**:

     $$
     P(x) = \sum_{j=1}^{k} y_j \cdot \ell_j(x)
     $$

     where

     $$
     \ell_j(x) = \prod_{\substack{1 \le m \le k \\ m \ne j}} \frac{x - x_m}{x_j - x_m}
     $$

   * To recover the **secret**, just compute $P(0)$:

     $$
     s = P(0) = \sum_{j=1}^{k} y_j \cdot \ell_j(0)
     $$

4. **Detecting wrong shares**

   * Reconstruct the polynomial using any $k$ shares.
   * Plug the other shares into this polynomial.
   * If $P(x_i) \neq y_i$, then that share is **wrong**.

---

### Worked Example (Testcase 1)

Shares:
$(1,4), (2,7), (3,12), (6,39)$, $k=3$ → quadratic polynomial.

Take first 3 shares $(1,4), (2,7), (3,12)$:

* Form Lagrange basis at $x=0$:

  $$
  \ell_1(0) = \frac{(0-2)(0-3)}{(1-2)(1-3)} = \frac{(-2)(-3)}{(-1)(-2)} = \frac{6}{2} = 3
  $$

  $$
  \ell_2(0) = \frac{(0-1)(0-3)}{(2-1)(2-3)} = \frac{(-1)(-3)}{(1)(-1)} = \frac{3}{-1} = -3
  $$

  $$
  \ell_3(0) = \frac{(0-1)(0-2)}{(3-1)(3-2)} = \frac{(-1)(-2)}{(2)(1)} = \frac{2}{2} = 1
  $$

* Compute secret:

  $$
  s = 4 \cdot 3 + 7 \cdot (-3) + 12 \cdot 1 = 12 - 21 + 12 = 3
  $$

Wait — check carefully. Did we pick wrong? Let’s check with all 3.

Actually, solving system directly:

$$
P(x) = ax^2 + bx + c
$$

$$
P(1)=4 \Rightarrow a+b+c=4
$$

$$
P(2)=7 \Rightarrow 4a+2b+c=7
$$

$$
P(3)=12 \Rightarrow 9a+3b+c=12
$$

Subtract eqns:

* From (2)-(1): $3a + b = 3$ → (i)
* From (3)-(2): $5a + b = 5$ → (ii)

Subtract (ii)-(i): $2a = 2 \Rightarrow a=1$.
Then $3(1)+b=3 \Rightarrow b=0$.
Then $a+b+c=4 \Rightarrow 1+0+c=4 \Rightarrow c=3$.

So polynomial is $P(x)=x^2+3$.
Thus secret = $c=3$. ✅

Check 4th share: $P(6)=36+3=39$, matches.
So no wrong shares.

---
Test case 2:
---

## 1) Convert every share value to decimal

(Each JSON key is the $x$-coordinate; `"base"` and `"value"` give the $y$-coordinate in that base.)

* Key `1` (base 6):
  $y_1 = 13444211440455345511_{(6)} = 995,085,094,601,491$

* Key `2` (base 15):
  $y_2 = \text{aed7015a346d635}_{(15)} = 320,923,294,898,495,900$

* Key `3` (base 15):
  $y_3 = \text{6aeeb69631c227c}_{(15)} = 196,563,650,089,608,567$

* Key `4` (base 16):
  $y_4 = \text{e1b5e05623d881f}_{(16)} = 1,016,509,518,118,225,951$

* Key `5` (base 8):
  $y_5 = 316034514573652620673_{(8)} = 3,711,974,121,218,449,851$

* Key `6` (base 3):
  $y_6 = 2122212201122002221120200210011020220200_{(3)} = 10,788,619,898,233,492,461$

* Key `7` (base 3):
  $y_7 = 20120221122211000100210021102001201112121_{(3)} = 26,709,394,976,508,342,463$

* Key `8` (base 6):
  $y_8 = 20220554335330240002224253_{(6)} = 58,725,075,613,853,308,713$

* Key `9` (base 12):
  $y_9 = 45153788322a1255483_{(12)} = 117,852,986,202,006,511,971$

* Key `10` (base 7):
  $y_{10} = 1101613130313526312514143_{(7)} = 220,003,896,831,595,324,801$

---

## 2) Problem parameters & plan

* $n=10,\ k=7$. So $P(x)$ is degree $\le 6$.
* Use any 7 correct shares to uniquely determine $P(x)$. I used keys `1..7` (i.e. $x=1,2,\dots,7$).
* To recover the secret $s=P(0)$ I used **Lagrange interpolation at 0**:

$$
P(0)=\sum_{j=1}^{7} y_j \cdot \ell_j(0),\qquad
\ell_j(0)=\prod_{\substack{1\le m\le7\\m\ne j}}\frac{0-x_m}{x_j-x_m}.
$$

Because the $x$-coordinates are $1,2,\dots,7$, the Lagrange **weights at 0** become simple integers:

$$
\ell_1(0)=7,\quad
\ell_2(0)=-21,\quad
\ell_3(0)=35,\quad
\ell_4(0)=-35,\quad
\ell_5(0)=21,\quad
\ell_6(0)=-7,\quad
\ell_7(0)=1.
$$
---

## 3) Compute the secret $P(0)$ by summing $y_j \cdot \ell_j(0)$

Compute each term $y_j\cdot \ell_j(0)$:

* $y_1 \cdot \ell_1(0) = 995,085,094,601,491 \times 7 = 6,965,595,662,210,437$
* $y_2 \cdot \ell_2(0) = 320,923,294,898,495,900 \times (-21) = -6,739,389,192,868,413,900$
* $y_3 \cdot \ell_3(0) = 196,563,650,089,608,567 \times 35 = 6,879,727,753,136,299,845$
* $y_4 \cdot \ell_4(0) = 1,016,509,518,118,225,951 \times (-35) = -35,577,833,134,137,908,585$
* $y_5 \cdot \ell_5(0) = 3,711,974,121,218,449,851 \times 21 = 77,951,456,545,587,446,871$
* $y_6 \cdot \ell_6(0) = 10,788,619,898,233,492,461 \times (-7) = -75,520,339,287,634,447,227$
* $y_7 \cdot \ell_7(0) = 26,709,394,976,508,342,463 \times 1 = 26,709,394,976,508,342,463$

Now sum those seven terms:

$$
\begin{aligned}
P(0)
&= 6,965,595,662,210,437 \\
&\quad - 6,739,389,192,868,413,900 \\
&\quad + 6,879,727,753,136,299,845 \\
&\quad -35,577,833,134,137,908,585 \\
&\quad +77,951,456,545,587,446,871 \\
&\quad -75,520,339,287,634,447,227 \\
&\quad +26,709,394,976,508,342,463 \\
&= -6,290,016,743,746,469,796.
\end{aligned}
$$

So the recovered **secret (constant term)** is:

$$
\boxed{\,P(0) = -6,\!290,\!016,\!743,\!746,\!469,\!796\,}
$$

(Everything above is exact integer arithmetic — no floating point.)

---

## 4) Verify all $n=10$ shares and list wrong ones

Using the unique degree-6 polynomial determined by keys `1..7` (you can compute full coefficients by solving the Vandermonde system or use Lagrange form for evaluations), I evaluated $P(x)$ at the remaining $x$-values:

* For key `8` (x=8):

  * Provided $y_8 = 58,725,075,613,853,308,713$
  * Computed $P(8) = 56,628,376,753,849,802,158$
    → **mismatch**

* For key `9` (x=9):

  * Provided $y_9 = 117,852,986,202,006,511,971$
  * Computed $P(9) = 103,475,622,590,553,895,635$
    → **mismatch**

* For key `10` (x=10):

  * Provided $y_{10} = 220,003,896,831,595,324,801$
  * Computed $P(10) = 163,393,027,611,500,647,978$
    → **mismatch**

All other keys `1`–`7` match exactly by construction.

So the **inconsistent (wrong) shares are keys**:

$$
\boxed{\,8,\;9,\;10\,}
$$

---

## 5) Final concise outputs you can paste

* **Testcase 2 — Recovered secret (P(0))**: `-6290016743746469796`
* **Wrong / inconsistent shares (JSON keys)**: `8, 9, 10`


---



