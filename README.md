# Secret-Sharing-algorithm-Hashira

---

### How the math works

1. **Secret sharing setup**

   * A secret \(s\) is embedded as the constant term of a polynomial \(P(x)\) of degree \(k-1\):

     \[
     P(x) = a_0 + a_1x + a_2x^2 + \cdots + a_{k-1}x^{k-1},
     \]

     where \(a_0 = s\).

   * Each share is \((x_i, y_i)\) where

     \[
     y_i = P(x_i).
     \]

2. **Your JSON input**

   * You are given \(n\) shares in different bases (so each `"value"` must be converted to decimal).
   * Example (Testcase 1):

     ```json
     "1": { "base": "10", "value": "4" }   → (x=1, y=4)
     "2": { "base": "2",  "value": "111" } → (x=2, y=7)
     "3": { "base": "10", "value": "12" }  → (x=3, y=12)
     "6": { "base": "4",  "value": "213" } → (x=6, y=39)
     ```

3. **Interpolation**

   * Any \(k\) correct shares uniquely determine the polynomial.
   * Use **Lagrange interpolation**:

     \[
     P(x) = \sum_{j=1}^{k} y_j \cdot \ell_j(x),
     \qquad
     \ell_j(x) = \prod_{\substack{1 \le m \le k \\ m \ne j}} \frac{x - x_m}{x_j - x_m}.
     \]

   * The **secret** is the constant term:

     \[
     s = P(0) = \sum_{j=1}^{k} y_j \cdot \ell_j(0).
     \]

4. **Detecting wrong shares**

   * Build \(P(x)\) using any \(k\) shares.
   * Check all other shares.  
     If \(P(x_i) \ne y_i\), then that share is **wrong**.

---

### Worked Example — Testcase 1

Shares:  
\((1,4), (2,7), (3,12), (6,39)\), with \(k=3\).

Solve system:

\[
P(1)=4,\; P(2)=7,\; P(3)=12
\]

→ Polynomial: \(P(x)=x^2+3\).  
→ Secret \(= P(0) = 3\). ✅  
→ Extra share \((6,39)\) is consistent, so **no wrong shares**.

---

### Worked Example — Testcase 2

#### Step 1: Convert shares

- Key 1: \(y_1 = 995085094601491\)  
- Key 2: \(y_2 = 320923294898495900\)  
- Key 3: \(y_3 = 196563650089608567\)  
- Key 4: \(y_4 = 1016509518118225951\)  
- Key 5: \(y_5 = 3711974121218449851\)  
- Key 6: \(y_6 = 10788619898233492461\)  
- Key 7: \(y_7 = 26709394976508342463\)  
- Key 8: \(y_8 = 58725075613853308713\)  
- Key 9: \(y_9 = 117852986202006511971\)  
- Key 10: \(y_{10} = 220003896831595324801\)

#### Step 2: Interpolation

\(n=10,\ k=7\).  
Use keys 1–7 to reconstruct polynomial.  
Weights at 0 for \(x=1..7\):

\[
\ell_1=7,\;\ell_2=-21,\;\ell_3=35,\;\ell_4=-35,\;\ell_5=21,\;\ell_6=-7,\;\ell_7=1.
\]

#### Step 3: Compute secret

\[
P(0) = \sum_{j=1}^7 y_j \ell_j(0) = -6290016743746469796.
\]

So the **secret** is:

\[
\boxed{-6290016743746469796}
\]

#### Step 4: Validate all shares

- Keys 1–7: consistent (by construction).  
- Key 8: mismatch.  
- Key 9: mismatch.  
- Key 10: mismatch.  

**Wrong shares:** \(\{8, 9, 10\}\).

---

### Final Results

- **Testcase 1**  
  Secret = `3`  
  Wrong shares = `none`

- **Testcase 2**  
  Secret = `-6290016743746469796`  
  Wrong shares = `8, 9, 10`
