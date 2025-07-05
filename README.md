In This file Many Tools are added ....
chmod +x filename.sh
./filename.sh
Tool subject 
#!/bin/bash

# Check for root
if [ "$EUID" -ne 0 ]; then
  echo "[!] root chala sarfraz"
  exit
fi

TARGET=$1
DATE=$(date +"%Y-%m-%d_%H-%M-%S")
REPORT_DIR="report_$TARGET_$DATE"
PDF_REPORT="${REPORT_DIR}/Pentest_Report_${TARGET}.pdf"

mkdir -p "$REPORT_DIR"

echo "[*] Starting Advanced Penetration Test on: $TARGET"
echo "[+] Output will be stored in: $REPORT_DIR"

# Function: Subdomain Enumeration
function subdomain_enum {
  echo "[+] Enumerating Subdomains..."
  amass enum -passive -d "$TARGET" > "$REPORT_DIR/subdomains.txt"
}

# Function: Nmap Deep Scan
function nmap_scan {
  echo "[+] Running Nmap Full Scan..."
  nmap -sS -sV -sC -A -p- --script=vuln "$TARGET" -oN "$REPORT_DIR/nmap_scan.txt"
}

# Function: WhatWeb for Tech Fingerprint
function tech_scan {
  echo "[+] Identifying Technologies..."
  whatweb -a 3 "$TARGET" > "$REPORT_DIR/whatweb.txt"
}

# Function: Nikto Web Scan
function web_vuln_scan {
  echo "[+] Running Nikto Web Scan..."
  nikto -h "$TARGET" > "$REPORT_DIR/nikto.txt"
}

# Function: WordPress Vulnerability Scan
function wp_scan {
  echo "[+] Running WPScan (if applicable)..."
  wpscan --url "$TARGET" --enumerate ap,at,cb,dbe,u --disable-tls-checks --random-user-agent --no-banner > "$REPORT_DIR/wpscan.txt"
}

# Function: Gobuster Directory Brute Force
function dir_scan {
  echo "[+] Running Directory Scan..."
  gobuster dir -u "$TARGET" -w /usr/share/wordlists/dirb/common.txt -t 50 -o "$REPORT_DIR/gobuster.txt"
}

# Function: SSL Vulnerability Scan
function ssl_scan {
  echo "[+] Running SSLyze..."
  sslyze --regular "$TARGET" > "$REPORT_DIR/sslyze.txt"
}

# Function: Generate PDF from all reports
function generate_pdf {
  echo "[+] Generating PDF Report..."
  {
    echo "Advanced Penetration Testing Report - $TARGET"
    echo "Date: $DATE"
    echo "-----------------------------------------"
    echo ""

    for file in "$REPORT_DIR"/*.txt; do
      echo "=== $(basename "$file") ==="
      cat "$file"
      echo ""
    done
  } > "$REPORT_DIR/full_report.txt"

  enscript "$REPORT_DIR/full_report.txt" -o - | ps2pdf - "$PDF_REPORT"

  echo "[+] PDF Report Generated: $PDF_REPORT"
}

# Execute All Functions
subdomain_enum
nmap_scan
tech_scan
web_vuln_scan
wp_scan
dir_scan
ssl_scan
generate_pdf

echo "[✓] Advanced Penetration Test Complete."





.
.
.
.
After scanning every thing this bash file provide a pdf file as a results
