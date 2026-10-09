# Naive vs KMP String Matching

## DAA Individual Project

**Name:** Sathwik Vithanala  
**Roll No:** 25WU0102302  
**Class:** AIML WOLVES  
**Subject:** Design and Analysis of Algorithms

---

## 📌 Project Overview

This project implements and visualizes two string matching algorithms:

- **Naive String Matching**
- **KMP (Knuth-Morris-Pratt)**

The application demonstrates how both algorithms search for a pattern inside a text and shows their differences step by step.

---

## 🎯 Objectives

- Understand Naive String Matching.
- Understand KMP and the LPS table.
- Visualize character comparisons and mismatches.
- Compare the performance of both algorithms.
- Demonstrate string searching in real-world log files.

---

## ⚙️ Algorithms

### Naive String Matching

Checks the pattern at every possible position in the text.

**Worst-case:** `O(n × m)`  
**Space:** `O(1)`

### KMP

Uses the **LPS (Longest Proper Prefix which is also a Suffix)** table to avoid unnecessary comparisons.

**Time:** `O(n + m)`  
**Space:** `O(m)`

---

## 🔍 Main Features

- Interactive text and pattern input
- Step-by-step algorithm visualization
- Naive vs KMP comparison
- LPS table visualization
- Character comparison tracking
- Match position detection
- Comparison count
- Log file search demonstration

---

## 💡 Example

```text
Text:    ABABDABACDABABCABAB
Pattern: ABABCABAB
