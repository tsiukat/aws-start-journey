# Lab: Creating a Hello, World Program

> **Course:** AWS re/Start Python Programming  
> **Topic:** Python Basics, AWS Cloud9 IDE, Environment Setup  
> **Date:** ---

## Lab Scenario

The goal of this lab was to set up a development environment using **AWS Cloud9**, explore the terminal and Python versions, and successfully write and execute a first Python script. This serves as a "sanity check" to ensure the coding environment is correctly configured.

---

## Tasks Completed

### ✅ Task 1 — Environment Setup & Version Check

- Launched the **AWS Cloud9 IDE** via the AWS Management Console.
- Used the terminal to verify installed Python versions:
  - `python --version` (Checked the system default)
  - `python3 --version` (Verified the target version for development)

**Root Discovery:** The system contains multiple versions of Python. Since Python 3.x is not backward compatible with Python 2.x, it is crucial to ensure we are calling the correct interpreter (`python3`) to avoid syntax errors in modern scripts.

### ✅ Task 2 — Writing and Running the Program

- Created a new file named `hello-world.py`.
- Wrote the traditional starter code:
  ```python
  print("Hello, World")
