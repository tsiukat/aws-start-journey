# Lab Data Protection Using Encryption (AWS KMS)

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** AWS KMS, Symmetric Encryption, AWS Encryption CLI, Ciphertext  
> **Date:** <!-- add date -->

---

## Lab Objective

Practical implementation of data protection using AWS Key Management Service (KMS). The goal was to encrypt sensitive plaintext files into ciphertext on an EC2 instance and successfully restore them using the AWS Encryption CLI.

---

## Tasks Completed

### ✅ Task 1 — Created an AWS KMS Key
- Created a **Symmetric KMS key** named `MyKMSKey` via the KMS console
- Assigned key administrative and usage permissions to the `voclabs` IAM role
- Copied the **Key ARN** for use in encryption commands

> **Symmetric** = same key encrypts and decrypts. Fast and efficient for data encryption.  
> **Asymmetric** = public key encrypts, private key decrypts. Used for key exchange and signatures.

### ✅ Task 2 — Configured the File Server Instance
- Connected to the `File Server` EC2 instance via **Session Manager** (no SSH key needed)
- Configured AWS credentials in `~/.aws/credentials` with lab access key, secret, and session token
- Installed the **AWS Encryption CLI**: `pip3 install aws-encryption-sdk-cli`

### ✅ Task 3 — Encrypted and Decrypted Data
During Task 3, the standard lab instructions failed due to environment-specific issues. Here is how they were resolved:

Broken Credentials: Encountered ConfigParseError.

Fix: Cleaned and manually rebuilt ~/.aws/credentials to ensure the [default] profile and aws_session_token were correctly formatted.

Version Incompatibility: The latest aws-encryption-cli required Python 3.8+, but the lab instance used Python 3.7.

Fix: Downgraded the client using pip3 install "aws-encryption-sdk-cli<3.0.0" and manually installed importlib-metadata backport.

CLI Metadata Conflict: Encountered path errors with metadata output.

Fix: Used the -S (Suppress Metadata) flag to bypass metadata logging and focus on core decryption.


**Encrypted `secret1.txt`:**

```bash
echo 'TOP SECRET 1!!!' > secret1.txt

aws-encryption-cli --encrypt \
  --input secret1.txt \
  --wrapping-keys key=$keyArn \
  --encryption-context purpose=test \
  --output ~/output/.
```

Output: `output/secret1.txt.encrypted` — unreadable ciphertext

**Decrypted the file:**

```bash
aws-encryption-cli --decrypt \
  --input secret1.txt.encrypted \
  --wrapping-keys key=$keyArn \
  --encryption-context purpose=test \
  --output .
```

Output: `secret1.txt.encrypted.decrypted` — original plaintext restored

---

## Screenshots

### KMS Key Created — MyKMSKey
<img width="1920" height="1040" alt="Create an AWS KMS key" src="https://github.com/user-attachments/assets/2bb9c3c4-84f0-4130-a0bc-c53840f7356a" />
---

<img width="1920" height="1040" alt="Encrypted and decrypted" src="https://github.com/user-attachments/assets/be29fc82-2c87-4494-b323-556327928186" />

---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **AWS KMS** | A managed service for creating and controlling cryptographic keys — backed by hardware security modules (HSMs) |
| **Symmetric Encryption** | One key used for both encrypt and decrypt — fast, efficient, suited for data at rest |
| **Key ARN** | The unique identifier for a KMS key — required when calling encrypt/decrypt operations |
| **Encryption Context** | A key-value pair (`purpose=test`) authenticated during decryption — adds integrity verification without being a secret itself |
| **Ciphertext** | Encrypted data is completely unreadable without the correct key — content structure is fully obscured |
| **AWS Encryption CLI** | Command-line tool that wraps KMS operations — enables scripted, repeatable encrypt/decrypt workflows |
| **Session Manager** | Secure instance access without SSH keys or open ports |

---

## My Reflection

Before this lab, encryption was abstract — something that "happened" when you checked a box in a service config. Working through the encrypt and decrypt commands made it concrete: plaintext goes in, unreadable ciphertext comes out, and only the correct KMS key brings the original content back.

The **encryption context** (`--encryption-context purpose=test`) was a concept I had not encountered before. It is not a secret — it travels in plaintext in the metadata — but it acts as an integrity check. If the context does not match at decryption time, the operation fails. That is a subtle but powerful guard against using a correctly encrypted file in the wrong context.

The most important architectural insight: AWS KMS keys **never leave the HSM**. You never download the key itself — you only call the service to use it. You cannot accidentally expose a key you never directly hold.

> 💡 **Key Insight:** Encryption is not just about making data unreadable — it is about controlling who can make it readable again. KMS ties that control to IAM permissions, audit logs, and key policies, making encryption a governance tool as much as a security one.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
