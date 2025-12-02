# 🔍 Bug Bounty Recon Methodology

This repository contains my personal recon workflow used for subdomain enumeration, URL harvesting, parameter discovery, port scanning, directory fuzzing and vulnerability scanning.

---

## 🛠️ Tools Used
Subfinder  
Assetfinder  
Httpx  
Naabu  
Gau  
Waybackurls  
Katana  
ParamSpider  
Arjun  
URO  
LinkFinder  
GF Patterns  
Nuclei  
Dalfox  
OpenRedireX  
FFUF  
Feroxbuster  

---

## 🛰️ Step-by-Step Recon Flow

### 1️⃣ Subdomain Enumeration
subfinder -d target.com -o subs.txt  
assetfinder --subs-only target.com > asset.txt  
cat *.txt > finaldomain.txt  

### 2️⃣ Alive Checking
httpx -l finaldomain.txt -o alive.txt  

### 3️⃣ Port Scan + Tech Detection
naabu -list alive.txt -o ports.txt  
cat ports.txt | httpx -title -tech-detect -status-code -o tech.txt  

### 4️⃣ URL Collection
gau alive.txt > gau.txt  
waybackurls alive.txt > wb.txt  
katana -list alive.txt -o katana.txt  

### 5️⃣ Parameters Discovery
ParamSpider -d target.com > paramspider.txt  
arjun -u https://target.com -o arjun_endpoints  

### 6️⃣ Final URL List
cat gau.txt wb.txt katana.txt paramspider.txt | uro > urls.txt  
linkfinder -i https://target.com/*.js -o cli > js_endpoints.txt  
cat urls.txt js_endpoints.txt | uro > final_urls.txt  

### 7️⃣ Vulnerability Parameter Filtering
gf xss final_urls.txt > xss-params.txt  
gf sqli final_urls.txt > sqli-params.txt  
gf lfi final_urls.txt > lfi.txt  
gf ssrf final_urls.txt > ssrf.txt  

### 8️⃣ Template Scanning (Nuclei)
nuclei -u https://target.com -t http/ -t file/ -t cves/ -t dns/ -t ssl/ -t workflows/ -o result.txt  

### 9️⃣ XSS Testing
dalfox file xss-params.txt -o xss.txt  

### 🔟 Open Redirect Testing
python3 openredirex.py -l final_urls.txt -p payloads.txt  

### 1️⃣1️⃣ Directory Bruteforce
ffuf -u https://target.com/FUZZ -w /usr/share/seclists/...  
feroxbuster -u https://target.com -w /usr/share/seclists/... -r  

---

## 📝 Notes
- Methodology focuses on wide recon → deep recon → active validation.  
- This workflow keeps evolving as I learn new tools and techniques.

