# Challenge Lab: Python Scripting - Prime Numbers

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Python Scripting on EC2 - Logic, File I/O, Linux Environment  

---

## Challenge

Write a Python 3 script on a Linux EC2 instance that:
- Finds and displays all **prime numbers between 1 and 250**
- Saves the results to a `results.txt` file

---

## Setup

Connected to the Linux Host EC2 instance via SSH:

## The Script

Created `prime_script.py`
```bash
touch prime_script.py
```
Opened it in a redactor:
```bash
nano prime_script.py
```

Code:
```python
def is_prime(n):
    if n < 2:
        return False
    for i in range(2, int(n**0.5) + 1):
        if n % i == 0:
            return False
    return True

primes = [n for n in range(1, 251) if is_prime(n)]

print("Prime numbers between 1 and 250:")
print(primes)
print(f"\nTotal count: {len(primes)}")

with open("results.txt", "w") as f:
    f.write("Prime numbers between 1 and 250:\n")
    f.write(str(primes) + "\n")
    f.write(f"\nTotal count: {len(primes)}\n")

print("\nResults saved to results.txt")
```

**Run the script:**

```bash
python3 prime_script.py
```

**Verify the output file:**

```bash
cat results.txt
```

---

## Expected Output

```
Prime numbers between 1 and 250:
[2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71,
 73, 79, 83, 89, 97, 101, 103, 107, 109, 113, 127, 131, 137, 139, 149, 151,
 157, 163, 167, 173, 179, 181, 191, 193, 197, 199, 211, 223, 227, 229, 233,
 239, 241]

Total count: 53
```

> **Screenshot:** Terminal - script output + `cat results.txt` confirming file created

![script-output](screenshots/01-script-output.png)

---

## Algorithm Logic

| Step | What it does |
|---|---|
| `is_prime(n)` | Returns `True` if `n` has no divisors other than 1 and itself |
| `range(2, √n + 1)` | Only checks up to square root - no need to check further |
| List comprehension | Filters all numbers 1–250 through `is_prime()` in one line |
| `open("results.txt", "w")` | Creates/overwrites file and writes results |

---

## Reflection

In this lab I learned how to:

- **Write and run Python 3 scripts on a Linux EC2 instance** - connecting via SSH, creating a file with a text editor, and executing it with `python3` is the standard workflow for any server-side scripting task in a cloud environment
- **Implement the prime number algorithm with square root optimization** - checking divisors only up to `√n` reduces unnecessary iterations and is a classic efficiency improvement over brute-force checking
- **Use Python file I/O to persist results** - writing output to `results.txt` with `open()` and `write()` demonstrates a practical pattern used in data pipelines, log generation, and report automation
- **Combine terminal verification with script output** - running `cat results.txt` after the script confirmed both the logic and the file write worked correctly end to end

> Key takeaway: Python scripting on EC2 is a foundational skill for cloud automation. The same pattern - SSH into an instance, write a script, execute it, verify output - is used for data processing, infrastructure automation, and AWS Lambda function development.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
