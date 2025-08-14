
## **Workflow Reconnaissance - Apa itu?**

**Reconnaissance** adalah fase pertama dalam penetration testing dimana Anda mengumpulkan informasi sebanyak-banyaknya tentang target **SEBELUM** melakukan serangan. Ini seperti mata-mata yang mengintai sebelum bertempur.

**Kenapa penting?**
- Mencari **entry point** (pintu masuk) ke sistem
- Memahami **teknologi** yang digunakan target
- Menemukan **hidden files/directories** 
- Mencari **vulnerability / celah** yang bisa dieksploit 

## **1. Directory/File Enumeration Tools**

### **Gobuster**
gobuster dir -u http://target.com -w /path/to/wordlist
**Fungsi:** Mencari direktori dan file tersembunyi yang tidak terlihat di website
**Cara kerja:** Brute force - mencoba ribuan nama file/direktori dari wordlist
**Contoh hasil:**
/admin       (Status: 200) [Size: 1234]
/backup      (Status: 403) [Size: 567] 
/config.php  (Status: 200) [Size: 890]
**Kenapa berguna?** Admin panel, backup files, config files sering tidak dipublish tapi bisa diakses langsung

### **DIRB**
dirb http://target.com
**Fungsi:** Sama seperti Gobuster, tapi lebih tua dan agak lebih lambat
**Cara kerja:** Recursive directory brute forcing
**Kelebihan:** Punya built-in wordlist yang bagus

**Analogi:** Bayangkan website seperti rumah. Tools ini mencoba membuka setiap pintu tersembunyi untuk cari ruang rahasia.

## **2. Technology Stack Detection**

### **WhatWeb**
whatweb http://target.com
**Fungsi:** Mendeteksi teknologi apa saja yang digunakan website
**Hasil contoh:**
Apache[2.4.41], PHP[7.4], WordPress[5.8], jQuery[3.6.0]
### **WebTech**
webtech -u http://target.com
**Fungsi:** Serupa WhatWeb, tapi dengan output yang lebih detailed
**Hasil contoh:**
Web Server: Apache 2.4.41
Language: PHP 7.4.3
CMS: WordPress 5.8.1
JavaScript: jQuery 3.6.0
**Kenapa penting?** 
- Setiap teknologi punya **vulnerability** spesifik
- Anda bisa fokus ke exploit yang sesuai
- Contoh: Jika tau WordPress versi lama, bisa langsung cari CVE WordPress

## **3. WordPress Specific - WPScan**

### **WPScan**
wpscan --url http://target.com --enumerate p,t,u
**Fungsi:** Scanner khusus untuk WordPress
**Yang diperiksa:**
- **p** = plugins (dan versinya)
- **t** = themes (dan versinya) 
- **u** = users (username admin)

**Hasil contoh:**
[+] WordPress version 5.4.2 (vulnerable)
[+] Plugin: contact-form-7 5.1.3 (vulnerable)
[+] User: admin (ID: 1)
**Kenapa powerful?** WordPress = 40% dari semua website. Plugin/theme lama = vulnerability goldmine

## **4. Web Vulnerability Scanners**

### **Nikto**
nikto -h http://target.com
**Fungsi:** Scanner vulnerability web server klasik
**Yang dicek:**
- Outdated server versions
- Default files/folders
- Dangerous files (.htaccess, backup files)
- Server misconfigurations

**Hasil contoh:**
+ Server may leak inodes via ETags
+ OSVDB-3092: /admin/: This might be interesting
+ /backup/: Backup folder found
### **Nuclei**
nuclei -u http://target.com -t cves/
**Fungsi:** Modern vulnerability scanner dengan template system
**Kelebihan:**
- **10,000+** templates for various vulnerabilities
- Selalu terupdate dengan CVE terbaru
- Bisa custom templates
- Fast & accurate

**Template categories:**
- **cves/** = Known CVE vulnerabilities
- **exposures/** = Information disclosure
- **misconfiguration/** = Server misconfig
- **technologies/** = Tech-specific vulns

## **Workflow Logic - Urutan yang Benar:**

1. Technology Detection (WhatWeb/WebTech)
   ↓
   "Oh ini WordPress Apache PHP"
   
2. Directory Enumeration (Gobuster/DIRB)  
   ↓
   "Ada /admin, /backup, /wp-config.php.bak"
   
3. Specific Scanner (WPScan jika WordPress)
   ↓
   "Plugin X versi Y vulnerable ke CVE-Z"
   
4. Vulnerability Scanning (Nuclei/Nikto)
   ↓
   "Confirmed: SQL injection di /search.php"
   
5. Manual Testing
   ↓
   "Eksploitasi vulnerability yang ditemukan"
## **Practical Example:**

Target: `http://oldwebsite.com`

# Step 1:
