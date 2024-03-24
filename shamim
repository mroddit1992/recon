#!/usr/bin/env bash

## --- colors output ---

RESET="\033[0m"
GRAY="\033[1;30m"
RED="\033[1;31m"
GREEN="\033[1;32m"
YELLOW="\033[1;33m"
BLUE="\033[1;34m"
PURPLE="\033[1;35m"
CYAN="\033[1;36m"
WHITE="\033[1;37m"

## ---  Funtions area  ---

banner () {
echo
echo -e "${GREEN} ____                        ____ ${RESET}"
echo -e "${GREEN}|  _ \ ___  ___ ___  _ __   |  _ \ _ __ ___ ${RESET}"
echo -e "${GREEN}| |_) / _ \/ __/ _ \| '_ \  | |_) | '__/ _ \ ${RESET}"
echo -e "${GREEN}|  _ <  __/ (_| (_) | | | | |  __/| | | (_) |${RESET}"
echo -e "${GREEN}|_| \_\___|\___\___/|_| |_| |_|   |_|  \___/${RESET}"
echo
echo -e "${WHITE}             [+] Created by Shamim Hossain ${RESET}"
echo
}

divider () {

    echo 
    echo -e "${GRAY}===================================================${RESET}"
    echo

}


## ---  Variables area  ---

DOMAIN=$1 

if [ $# -eq 0 ]
then
    help
    exit 1
fi

subdomain_path=$DOMAIN/subdomains
screenshot_path=$DOMAIN/screenshots
scan_path=$DOMAIN/scans
gau_path=$DOMAIN/gau
parameter_path=$DOMAIN/parameter
waybackurls_path=$DOMAIN/waybackurls

if ! [ -d "$DOMAIN" ]
then 
    mkdir $DOMAIN
else
    echo -e "${RED} [+] Diretory Already Exists. ${RESET}"
    exit 2
fi

if [ ! -d "$subdomain_path" ]; then
	mkdir $subdomain_path
fi

if [ ! -d "$screenshot_path" ]; then
	mkdir $screenshot_path
fi

if [ ! -d "$scan_path" ]; then
	mkdir $scan_path
fi

if [ ! -d "$gau_path" ]; then
	mkdir $gau_path
fi

if [ ! -d "$parameter_path" ]; then
	mkdir $parameter_path
fi

if [ ! -d "$waybackurls_path" ]; then
	mkdir $waybackurls_path
fi
## ---  Scripts area  ---

banner
divider

echo -e "[+] Gathering sub-domin from subfinder..."
subfinder -silent -d $DOMAIN >> $subdomain_path/sub-found.txt

divider
echo -e "[+] Gathering sub-domin from assetfinder..."
assetfinder $DOMAIN | grep $DOMAIN >> $subdomain_path/sub-found.txt

divider
echo -e "[+] Gathering sub-domin from crt.sh..."
curl -s https://crt.sh//?q\=%.$DOMAIN\&output\=json | jq -r '.[].name_value' | sed 's/*.//g' >> $subdomain_path/sub-found.txt
cat $subdomain_path/sub-found.txt | sort -u | tee -a $subdomain_path/uniq-sub.txt

echo 
echo -e "[+] Subdomin Gathering Completed."
echo
rm $subdomain_path/sub-found.txt

divider
echo -e "${GREEN}[+] Starting HTTPx${RESET}"
cat $subdomain_path/uniq-sub.txt | httpx | tee -a $subdomain_path/live-subdomain.txt
cat $subdomain_path/live-subdomain.txt | sed 's/https\?:\/\///' | sed 's/https\?:\/\///' > $subdomain_path/alive2-for-nmap.txt
echo -e "${GREEN}[+] Finding Live Sub-domain Complete${RESET}"

divider
echo -e "${GREEN}[+] Starting GOwitness${RESET}"
gowitness file -f $subdomain_path/alive2-for-nmap.txt -P $screenshot_path/ --no-http
echo -e "${GREEN}[+] Takeing Screen Short Complete${RESET}"


divider
echo -e "${GREEN}[+] Starting GAU${RESET}"
echo $DOMAIN | gau | tee -a $gau_path/gau.txt
echo -e "${GREEN}[+] Getting url by GAU Complete${RESET}"


divider
echo -e "${GREEN}[+] Starting WaybackURLS${RESET}"
echo $DOMAIN | waybackurls | sort -u | tee -a $waybackurls_path/waybackurl.txt
echo -e "${GREEN}[+] Getting url by waybackurls Complete${RESET}"


divider
echo -e "${GREEN}[+] Starting Nmap${RESET}"
nmap -iL $subdomain_path/alive2-for-nmap.txt -p21,22,25,80,8080,443,8433,8000,10000 -oN $scan_path/nmap.txt
echo -e "${GREEN}[+] Port scaning by NMAP Complete${RESET}"



divider
echo -e "${GREEN}[+] Starting paramspider${RESET}"
paramspider -l $subdomain_path/alive2-for-nmap.txt >> $parameter_path/parameter.txt
echo -e "${GREEN}[+] Parameter Finding by paramspider Complete${RESET}"

