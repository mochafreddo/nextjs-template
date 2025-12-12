# Decisions

This document records the template’s core technical choices and the reasons behind them.
The guiding principle is simple: these are **defaults**, not optional add‑ons. We prefer making the right constraints explicit early rather than “maybe later.”

---

## Why TypeScript

This template treats TypeScript as a baseline, not a feature flag.

- Frontend code grows in complexity quickly; types are one of the most cost‑effective ways to control that complexity.
- TypeScript reduces runtime bugs and makes refactoring and feature work faster by giving reliable, tool‑assisted feedback.
- In collaboration, types act as contracts: component and API usage becomes self‑documenting in code.
- The Next.js/React ecosystem has effectively standardized on TypeScript, so starting with it improves long‑term maintainability and compatibility.

---

## Why strict mode from the start

We enable `strict: true` from day one.

- Strict mode is the real dividing line between “using TypeScript” and “getting TypeScript’s safety guarantees.”
- Turning on strictness later creates a noisy, expensive migration with many accumulated type errors.
- Enabling it early keeps the cost low while the codebase is small and establishes consistent standards naturally.
- Strict mode surfaces implicit `any`, nullability mistakes, and shape mismatches during development, lowering long‑term bug and maintenance costs.

In short, TypeScript + strict mode reflects this template’s philosophy: safe defaults now, lower total cost later.
