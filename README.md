# toxssin  
[![Python 3.x](https://img.shields.io/badge/python-3.x-yellow.svg)](https://www.python.org/) ![JavaScript](https://img.shields.io/badge/-JavaScript-blue) [![License](https://img.shields.io/badge/license-MIT-red.svg)](https://github.com/t3l3machus/toxssin/blob/main/LICENSE)
## Purpose

toxssin is an open-source penetration testing tool that automates the process of exploiting Cross-Site Scripting (XSS) vulnerabilities. It consists of an https server that works as an interpreter for the traffic generated by the malicious JavaScript payload that powers this tool (toxin.js).  

This project started as (and still is) a research-based creative endeavor to explore the exploitability depth that an XSS vulnerability may introduce by using vanilla JavaScript, trusted certificates and cheap tricks.

**Disclaimer**: The project is quite fresh and has not been widely tested.  
### Video Presentation  
https://www.youtube.com/watch?v=i9osyuFK6ro

## Screenshots
![usage_example_png](https://raw.github.com/t3l3machus/toxssin/master/Screenshots/toxssin-1.png)
  
Find more screenshots [here](Screenshots/).

## Capabilities  
By default, toxssin intercepts:
- cookies (if HttpOnly not present),
- keystrokes,
- paste events,
- input change events,
- file selections,
- form submissions,
- server responses,
- table data (static as well as updates),

Most importantly, toxssin:
- attempts to maintain XSS persistence while the user browses the website by intercepting http requests & responses and re-writing the document,
- supports session management, meaning that, you can use it to exploit reflected as well as stored XSS,
- supports custom JS script execution against sessions,
- automatically logs every session.

## Installation & Usage
```
git clone https://github.com/t3l3machus/toxssin
cd ./toxssin
pip3 install -r requirements
```  
To start toxssin.py, you will need to supply ssl certificate and private key files.

If you don't own a domain with a trusted certificate, you can issue and use self-signed certificates with the following command (although this won't take you far):  
```
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365
```

It is strongly recommended to run toxssin with a trusted certificate (see [How to get a Valid Certificate](#How-to-get-a-Valid-Certificate) in this document). That said, you can start the toxssin server like this:
```
# python3 toxssin.py -u https://your.domain.com -c /your/certificate.pem -k /your/privkey.pem
```

## XSS Exploitation Obstacles
In my experience, there are 4 major obstacles when it comes to Cross-Site Scripting attacks attempting to include external JS scripts:
1. the "Mixed Content" error, which can be resolved by serving the JavaScript payload via https (even with a self-signed certificate).
2. the "NET::ERR_CERT_AUTHORITY_INVALID" error, which indicates that the server's certificate is untrusted / expired and can be bypassed by using a certificate issued by a trusted Authority.
3. Cross-origin resource sharing (CORS), which is handled appropriately by the toxssin server.
4. `Content-Security-Policy` header with the `script-src` set to specific domain(s) only will block scripts with cross-domain src from loading. Toxssin relies on the `eval()` function to deliver its poison, so, if the website has a CSP and the `unsafe-eval` source expression is not specified in the `script-src` directive, the attack will most likely fail (i'm working on a second poison delivery method to work around this). 

**Note**: The "Mixed Content" error can of course occur when the target website is hosted via http and the JavaScript payload via https. This limits the scope of toxssin to https only webistes, as (by default) toxssin is started with ssl only.


## How to get a Valid Certificate
First, you need to own a domain name. The fastest and most economic way to get one (in my knowledge) is via a cheap domain registrar service (e.g.  https://www.namecheap.com/). Search for a random string domain name (e.g. "fvcm98duf") and check the less popular TLDs, like .xyz, as they will probably cost around 3$ per year.

After you purchase a domain name, you can use certbot (Let's Encrypt) to get a trusted certificate in 5 minutes or less:
1. Append an A record to your Domain's DNS settings so that it points to your server ip,
2. Follow certbots [official instructions](https://certbot.eff.org/instructions).  

**Tip**: Don't install and run certbot on your own, you might get unexpected errors. Stick with the instructions.

## Changelog
`2022-06-19` - Added exec command (you can now execute custom JS script against a session).
## Future 
The idea is to make it sharper, more reliable and expand its capabilities. Currently, i'm working on improving file captures.
