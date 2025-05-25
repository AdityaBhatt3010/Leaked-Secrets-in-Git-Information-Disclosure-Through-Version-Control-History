# 💥 Leaked Secrets in Git: Information Disclosure Through Version Control History

**Author:** Aditya Bhatt
**WriteUp Type:** Bug Bounty Simulation
**Vulnerability:** Information Disclosure via Git History
**Difficulty:** 🟠 Practitioner
**Platform:** PortSwigger Web Security Academy
**Status:** 🟢 Lab Solved

---

## 🧠 Overview

Version control is a blessing for developers—but when misconfigured or left exposed in production environments, it can become a ticking time bomb for sensitive data. In this write-up, we’ll walk through an **Information Disclosure vulnerability** caused by exposing the **`.git` directory** on a live web server. This allows us to **leak the administrator password from Git commit history**, hijack the admin session, and ultimately delete a user to complete the lab scenario.

![Cover](https://github.com/user-attachments/assets/3a3449b9-dfa2-4dac-a2cb-1cec1c2a148a) <br/>

---

## 🎯 Vulnerability Summary

* **Bug Type:** Information Disclosure
* **Impact:** Unauthorized admin access and account deletion
* **Root Cause:** Accessible `.git` directory on the production server
* **Attack Vector:** Git commit history reveals previously hardcoded credentials

---

## 📍Step-by-Step Exploitation

Here's a **step-by-step Proof of Concept (PoC)** mapped out clearly with corresponding tool usage. All steps are performed in **present tense** as requested:

1. Go to Lab ([https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-in-version-control-history](https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-in-version-control-history)).

![1](https://github.com/user-attachments/assets/91864b36-917b-4b18-9e2d-56ff9d8804e9) <br/>

2. Try `/.git` — the directory is exposed and browsable, indicating a serious misconfiguration.

![2](https://github.com/user-attachments/assets/4a9495bb-c36e-4466-8030-96e255f03abb) <br/>

3. Run `wget -r https://YOUR-LAB-ID.web-security-academy.net/.git/` to recursively download the entire Git repository from the live server.

![3](https://github.com/user-attachments/assets/d108cc7e-10f4-4341-bd8f-6e0b5a00286e) <br/>

4. Open `git-cola`.
   If you don’t have it already, run:
   `sudo apt-get install git-cola`
   In case of any errors, run:
   `sudo apt-get install git-cola --fix-missing`

![4](https://github.com/user-attachments/assets/c1253082-d707-4c11-a4d6-beb54cee4064) <br/>

5. Right-click `admin.conf` and hit **"View History"**.
   Voila — we uncover a commit with the message:
   *"Remove admin password from config"*
   The Git diff clearly exposes the **previous hardcoded password**, even though it was later replaced by an environment variable.


![5](https://github.com/user-attachments/assets/22f3d905-6134-46a3-86cb-853beaab8864) <br/>

6. Login using `administrator:<PASSWORD>` obtained from the Git diff.

![6](https://github.com/user-attachments/assets/28e1817f-f7f0-4469-82d2-4425f2650225) <br/>

7. Navigate to the admin panel and **delete Carlos**, the user specified in the lab.

![7](https://github.com/user-attachments/assets/d5e8f73b-9322-4278-a978-3ca63c2d6de0) <br/>

8. Congrats! The Lab has been solved and the vulnerability exploited successfully.
 
![8](https://github.com/user-attachments/assets/5437c3f0-1970-4c80-aa54-5977a492d7e1) <br/>

---

## 🕵️‍♂️ Root Cause Analysis

The issue stems from **exposing the `.git/` directory** to the public. Git repositories contain a complete history of changes, which includes sensitive information even if it’s later removed. Attackers can **reconstruct past states of the codebase** and **recover deleted secrets**, such as credentials, tokens, or private keys.

---

## 🛡️ Mitigation & Recommendations

* **Never deploy `.git` directories to production.**
  Use a `.gitignore` in your deployment pipeline to exclude version control metadata.
* **Scrub secrets from history** using tools like `git filter-branch` or `BFG Repo-Cleaner`.
* **Regularly audit** publicly accessible directories and endpoint exposures using tools like:

  * [`git-dumper`](https://github.com/internetwache/GitTools)
  * [`truffleHog`](https://github.com/trufflesecurity/trufflehog)
* **Monitor commit messages** for unintentional disclosures or descriptive messages that hint at security-sensitive changes.

---

## 💬 Final Thoughts

This lab is a textbook example of how **development artifacts can become attack surfaces**. As security researchers and ethical hackers, we must constantly scan for these oversights. For bug bounty hunters, **exposed `.git` directories** are goldmines of opportunity. Always check for historical leaks—you never know what secrets the past is still holding onto.

---

## 🧰 Tools Used

* 🔎 BurpSuite
* 🐧 wget
* 🧠 git-cola
* 🖥️ Linux Terminal

---

## 🔗 Reference

* [PortSwigger Lab - Information disclosure in version control history](https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-in-version-control-history)
* [GitTools: GitDumper](https://github.com/internetwache/GitTools)
* [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)

---
