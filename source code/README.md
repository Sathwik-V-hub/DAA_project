# Naive vs KMP String Matching Visualizer

> **Design and Analysis of Algorithms (DAA) Course Project**  
> An educational, interactive web application visually demonstrating how the **Naive String Matching Algorithm** and the **Knuth-Morris-Pratt (KMP) Algorithm** actually work, step by step. Presentation-ready for faculty evaluation and viva examination.

---

## 1. Project Objective

The primary objective of this project is to eliminate the "black box" abstraction of string matching algorithms. Rather than just taking a text and pattern and returning match indexes, this visualizer exposes:
- Every individual character comparison (equality vs mismatch)
- Window shifts in Naive search
- Dynamic **LPS (Longest Proper Prefix which is also a Suffix)** table construction
- KMP pattern pointer jumps upon mismatch
- **The Non-Backtracking Text Pointer Invariant**: visually demonstrating that in KMP, the text pointer $i$ never moves backward!
- Real empirical comparison benchmarks and real-world server log searching

---

## 2. Naive String Matching Algorithm

### Core Logic
The Naive (brute-force) algorithm checks all possible starting positions $i$ in the text $T$ from $0$ to $n - m$:

```python
for i = 0 to n - m:
    j = 0
    while j < m and T[i + j] == P[j]:
        j++
    if j == m:
        report match at index i
    # On mismatch: shift pattern by 1 and restart with j = 0
```

### Key Observation
- **Amnesia**: Naive does not remember any characters that matched in the current window.
- **Shift by 1**: On a mismatch, the pattern shifts by exactly 1 position ($i \leftarrow i + 1$), and the pattern pointer resets to $0$ ($j \leftarrow 0$).

---

## 3. Knuth-Morris-Pratt (KMP) Algorithm

Invented by Donald Knuth, James H. Morris, and Vaughan Pratt (1977), KMP improves on Naive search by using prefix information of the pattern itself to skip redundant comparisons.

### Search Logic
```python
i = 0  # text pointer
j = 0  # pattern pointer

while i < n:
    if T[i] == P[j]:
        i++
        j++
    if j == m:
        report match at (i - m)
        j = lps[j - 1]  # look for overlapping matches
    elif i < n and T[i] != P[j]:
        if j != 0:
            j = lps[j - 1]  # i stays at the SAME position!
        else:
            i++
```

### Key Observation
- **Zero Backtracks**: The text pointer $i$ moves strictly forward ($0 \le i \le n$).
- **LPS Recovery**: When a mismatch occurs after matching $j$ characters, $j$ jumps to $\text{LPS}[j - 1]$. The first $\text{LPS}[j - 1]$ characters are already guaranteed to match the preceding text.

---

## 4. The LPS Concept (Longest Proper Prefix which is also a Suffix)

For any subpattern $P[0..i]$, $\text{LPS}[i]$ stores the length of the **longest proper prefix** of $P[0..i]$ that is also a **suffix** of $P[0..i]$.

- **Proper Prefix**: A prefix of a string that does not include the entire string itself.
- **Suffix**: Any substring ending at the current character index.

### Example for Pattern `ABABCABAB`:

| Index ($i$) | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Pattern** | A | B | A | B | C | A | B | A | B |
| **LPS** | **0** | **0** | **1** | **2** | **0** | **1** | **2** | **3** | **4** |

- At $i = 3$ (`"ABAB"`): Proper prefix `"AB"` matches suffix `"AB"` $\rightarrow \text{LPS}[3] = 2$.
- At $i = 8$ (`"ABABCABAB"`): Proper prefix `"ABAB"` matches suffix `"ABAB"` $\rightarrow \text{LPS}[8] = 4$.

---

## 5. Time Complexity Analysis

| Algorithm | Preprocessing | Best Case | Average Case | Worst Case |
| :--- | :---: | :---: | :---: | :---: |
| **Naive Search** | $0$ (None) | $O(n)$ | $O(n)$ | $O(n \times m)$ |
| **KMP Search** | $O(m)$ (LPS) | $O(n)$ | $O(n)$ | $O(n + m)$ (Guaranteed Linear) |

### Why Naive Degrades to $O(n \times m)$
When the text and pattern contain repetitive prefixes, such as:
- Text: `AAAAAAAAAAAAAAAAAAAAAB`
- Pattern: `AAAAAB`

Naive matches $m - 1$ characters, fails on the last character, shifts by 1, and re-matches the same $m - 1$ characters again. Total comparisons: $(n - m + 1) \times m \approx O(n \times m)$.

### Why KMP is $O(n + m)$
- Preprocessing the LPS array takes amortized $O(m)$ steps because the prefix length pointer `len` increases at most $m$ times and decreases at most $m$ times.
- Searching takes amortized $O(n)$ steps because text pointer $i$ never decreases and pattern pointer $j$ decreases at most $n$ times.

---

## 6. Space Complexity

| Algorithm | Auxiliary Space | Explanation |
| :--- | :---: | :--- |
| **Naive Search** | **$O(1)$** | Operates strictly in place without auxiliary data structures. |
| **KMP Search** | **$O(m)$** | Stores the precomputed integer LPS array of size equal to pattern length $m$. |

---

## 7. How to Run the Project

### Prerequisites
- Node.js (v18+)
- npm (v9+)

### Installation & Launch
```bash
# 1. Install dependencies
npm install

# 2. Run local development server
npm run dev
```

Open your browser at:
```
http://localhost:5173/
```

### Production Build
```bash
npm run build
npm run preview
```

---

## 8. How the Visualization Works

1. **Deterministic Step Generator**: Pure TypeScript functions (`generateNaiveSteps`, `generateLPSSteps`, `generateKMPSteps`) precalculate the deterministic execution sequence.
2. **Character Grid Visualizer**:
   - Monospaced tiles with text indices ($0, 1, 2, \dots$) and pattern indices ($j = 0, 1, \dots$).
   - High-contrast visual color states:
     - **Match**: Emerald Green glow (`#10b981`)
     - **Mismatch**: Crimson Rose glow with shake animation (`#f43f5e`)
     - **Active Pointer**: Electric Blue cursor (`#38bdf8`)
     - **LPS Jump**: Purple glow (`#a855f7`)
     - **Found Occurrence**: Amber / Gold glow (`#fbbf24`)
3. **Step Controls**:
   - **Next Step**: Advances exactly ONE comparison, mismatch shift, or LPS jump.
   - **Prev Step**: Moves backward to the previous algorithm state.
   - **Auto Play / Pause**: Runs automated transitions with adjustable speed (Slow, Normal, Fast, Turbo).
4. **Live Execution Trace Table**: Auto-scrolling tabular trace recording $(Step, i, j, \text{Char}_T, \text{Char}_P, \text{Result}, \text{Action}, \text{Comparisons})$.
5. **Code Line Highlighting**: Highlights the exact line in pseudocode currently being evaluated.
6. **Side-by-Side Live Comparison**: Executes Naive and KMP simultaneously on the exact same inputs to directly visualize comparison savings.
7. **Empirical Benchmarks**: Runs actual algorithm implementations on inputs up to 50,000 characters and plots SVG comparison bars.
8. **Real-World Server Log Search Demo**: Highlights search patterns across timestamped log lines and calculates operational efficiency.
9. **Presentation Mode**: Fullscreen, high-contrast presenter mode optimized for college faculty demonstrations and viva oral examinations.
