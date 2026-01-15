# Kerberoasting – Active Directory Attack Explained (Beginner Friendly)

> 📌 **Purpose:** Educational / Lab-based learning of Active Directory security
> ⚠️ **Disclaimer:** This content is strictly for **authorized labs, learning, and defensive awareness**.
> Do **not** use against systems you do not own or have permission to test.

---

## 📖 What is Kerberoasting?

**Kerberoasting** is an Active Directory attack where an **authenticated domain user** requests Kerberos **service tickets (TGS)** for service accounts and then **cracks those tickets offline** to recover **service account passwords**.

🔑 Key idea:

* No exploit
* No admin access required
* Abuses **legitimate Kerberos behavior**

---

## 🧠 Why Kerberoasting Matters (Industry Context)

Service accounts often:

* Have **long-lived passwords**
* Are **rarely rotated**
* Sometimes hold **high privileges**

A single weak service account can lead to:

* Server compromise
* Lateral movement
* In some cases, **domain-level impact**

---

## 🧩 Prerequisites

Before Kerberoasting is possible:

* ✅ Attacker has **valid domain user credentials**
* ✅ Domain has **service accounts with SPNs**
* ❌ Admin privileges are **not required**

---

## 🏗️ High-Level Attack Flow

```
Valid Domain User
        ↓
Enumerate SPNs
        ↓
Request Kerberos Service Ticket (TGS)
        ↓
Extract Kerberoastable Hash
        ↓
Offline Password Cracking
        ↓
Service Account Password
```

---

## 🔍 Step 1: Initial Domain Access

Kerberoasting requires the attacker to be an **internal domain user**.

This access can come from:

* Credential exposure
* Misconfigurations
* Internal access in a lab environment

📌 At this stage, the user is **not an admin**.

---

## 🔎 Step 2: Enumerating Service Accounts (SPNs)

Kerberos services are identified using **Service Principal Names (SPNs)**.

Any authenticated user can **request information about SPNs** from the Domain Controller.

This is expected behavior in Active Directory.

---

## 🎟️ Step 3: Requesting Service Tickets (TGS)

Using Impacket’s `GetUserSPNs`, a user can:

* Identify service accounts
* Request Kerberos service tickets for them

### 🧪 Example Command (Lab Use)

```bash
impacket-GetUserSPNs DOMAIN/username:password -dc-ip <DomainController-IP> -request
```

### 🔎 What This Does:

* Lists service accounts with SPNs
* Requests **TGS tickets**
* Outputs **Kerberoastable hashes**

---

## 🔐 Step 4: Understanding the Service Ticket

* The service ticket is **encrypted using the service account’s password**
* The ticket itself **cannot be used to log in**
* Its only purpose here is **offline password cracking**

📌 This is why Kerberoasting is stealthy.

---

## 🧪 Step 5: Offline Password Cracking

The extracted ticket hash can be cracked offline using password-cracking tools.

Why this works:

* Service account passwords are often weak
* Password rotation is poor
* No account lockout risk (offline)

🎯 **Goal:** Recover the **plaintext password** of the service account.

---

## 🔓 Step 6: Post-Compromise Impact

Once the service account password is recovered:

* The attacker gains a **new identity**
* Impact depends on service account privileges

### Possible Outcomes:

* Normal service → service abuse
* Local admin on server → server compromise
* Over-privileged account → lateral movement

---

## 🚨 Why Kerberoasting Is Dangerous

* Uses legitimate Kerberos functionality
* No exploits or malware
* Offline cracking avoids detection
* Often missed in security monitoring

This is why Kerberoasting is commonly seen in **real-world breaches**.

---

## 🛡️ Mitigation & Defense (Very Important)

### ✅ Use Managed Service Accounts (gMSA/MSA)

* Automatic password rotation
* No human-managed passwords

### ✅ Enforce Strong Password Policies

* Long (25–30 characters)
* Random
* No dictionary words

### ✅ Regular Password Rotation

* Especially for legacy service accounts

### ✅ Audit SPNs

* Remove unnecessary SPNs
* One account per service where possible

### ✅ Monitor Kerberos Activity

* Abnormal volume of TGS requests
* Suspicious service ticket behavior

---

## 🧠 Key Takeaways

* Kerberoasting targets **service accounts**, not users
* Tickets are only a **means to extract passwords**
* The real risk comes from **poor identity hygiene**
* Defensive controls can **completely neutralize** this attack

---

## 📌 Final Note

Understanding Kerberoasting is critical for:

* Red teamers learning AD attack paths
* Blue teamers building detection logic
* Security engineers hardening enterprise identity

**Security is not just about patching — it’s about protecting identities.**

---

## 🔗 References

* Microsoft Kerberos Documentation
* Active Directory Security Best Practices
* Impacket Project (for lab research)

