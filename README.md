#  Bloomberg-Style Market Engine (C++)

High-performance C++ market analytics engine inspired by institutional terminals.

---

##  Overview

This project implements a lightweight financial market engine in C++ designed to simulate and analyze:

- Equity markets  
- Oil (Energy)  
- US10Y / US2Y Rates  
- Dollar Index (DXY)  
- Gold  
- VIX (Volatility)  
- HY Credit Spread  

---

##  Structure

.
+-- bloomberg.cpp
+-- README.md

---

##  Build & Run

Compile:
g++ bloomberg.cpp -o bloomberg

Run:
./bloomberg

---

##  Data Model

struct Row {
    double tech, capex, oil;
    double us10y, us2y, dxy, gold;
    double hy_spread, vix, orcl;
};

---

##  Output Example

GLOBAL MACRO TERMINAL

SP500   ? Bullish  
NASDAQ  ? Strong  
OIL     ? Rising  
US10Y   ? Stable  
VIX     ? Low Risk  

SIGNAL: RISK ON

---

##  Philosophy

- Fast (C++)
- Minimal
- Terminal-first
- Expandable

---

##  Roadmap

- Real-time data
- API integration
- Python analytics
- SQL storage
- Multithreading

---

## Author

Lauro Beck

---

##  License

MIT
