---
layout: default
title: HTTP/HTTPS
parent: TCP
has_children: true
nav_order: 4-3
---
# HTTP / HTTPS | 80, 443

## Methodology
<ul>
<li> Manual Inspection ('View the source Luke') </li>
<li> robots.txt (if it exists - any restricted area or admin portals?) </li>
<li> <a href="IDOR">Insecure Direct Object Reference (IDOR)?</a></li>
<li> <a href="CMS">CMS</a></li>
<li>  .svn entries (any left over dev stuff, repositories or data) </li>
<li>  .DS_STORE (may contain sensitive info - run strings across it) </li>
<li>  Login Forms - Authentication Bypass Attacks </li>
	<ul>
	<li> <a href="../SQL/SQLi">SQL Injection & SQLMap</a></li>
		<ul>
		<li>try: ' OR 1=1 -- - </li>
		<li>try: '||1=1 -- -</li>
		</ul>
	<li> <a href="../NoSQL/NoSQLi">NoSQL Injection</a></li>
	<li> Error Message Username Enumeration (common & removes 50% of the effort)</li>
	<li> Default credentials? (Common web service credentials often remain unchanged in production)</li>
	<li> Brute-Forcing (See below)</li>
	<li> Is a template engine running? Try <a href="HTTP/SSTI">Server-Side Template Injection (SSTI)</a></li>
	<li> <a href="HTTP/XSS">XSS (Cross-Site Scrpting)</a></li>
	</ul>
<li> Contact Forms / Password Reset or other Forms for Input</li>
	<ul>
	<li> Test for <b> Parameter Existence</b></li>
	<li> Password Reset Form & IDOR (Intercept the request in BurpSuite, identify any IDOR values such as: new_password=X&confirm_password=XX&username=someuser, can we provide another username for account takeover)</li>
	<li> Fuzz password reset forms to see if there is any additional hidden input/parameters</li>
	</ul>
<li> Local Code Execution (Web Server - e.g. the server runs a ping command we could try ping -c 3 127.0.0.1;id or break the command with ping -c 3 127.0.0.1BADDATA || id)</li>
</ul>
### Logged In:
<ul>
<li>Identify service versions & versioning information (search for these on exploit-db) </li>
<li><a href="file-upload-vulns">File upload vulnerabilities?</a></li>
<li> What CMS functionality can be abused if any</li>
<li> Identify web server info </li>
<li> Command Injection possibilities - diagnostic tools (any system level execution happening e.g. cron?) Can we schedule any system level jobs to run?</li>
<li> Admin panels may allow us to download backups and read them locally - exfiltrate sensitive data, potentially modify or downgrade access control lists and make our own world writable directory</li>
<li> User privilege may be able to be changed - Downgrade attacks</li>
<li> Code execution (directly into a new post/page, or via plugins, modules, extensions & templates)</li>
<li> What modules/extensions/addons are available - can we create our own with backdoors?</li>
<li> If it's open source - check our any github repositories (with version information)</li>
<li> Create your own malicious plugin - all you need is one authentic plugin to clone and plant your backdoor in there then upload</li>
</ul>

### CUPS (HTTP) Network Printers
- The <b> PUT </b> method is risky - allows the ability to upload any file to a writeable web directory
- If you can find a web irectory on the port number with write permissions, we could drop a file there (assuming it's unauthorised)

### Testing for Parameter Existence
- Particularly examine any URL that submits data across the URL String - are there any hidden backend parameters?
Example:
``` 192.168.1.165/thankyou.php?FUZZ=someData```
Where FUZZ = the parameter name to discover: <b>/seclists/Discovery/Web-Content/burp-parameter-names.txt</b>

### Apache 
- ALWAYS go for the Apache Configuration file to discover additional attack surfaces we may not have access to
{: .important }
httpd.config
#### Apache Tomcat
<b> URI's to visit for ANY Tomcat installation: </b>
<ul>
<li> Manager App: /manager/html </li>
<li> Host Manager: /host-manager/html</li>
	<ul>
	<li>Host manager is used to add virtual hosts, etc</li>
	<li> Check to see if there is a UPC - Universal Path Controller</li>
	</ul>
<li> Configuration files for users:</li>
	<ul>
	<li>../../../../etc/tomcat7/tomcat-users.xml (Replace 7 with the major version of Tomcat)</li>
	</ul>
<li> Default credentials?</li>
	<ul>
	<li> In Tomcat there is a github called <a href="https://github.com/netbiosX/Default-Credentials/blob/master/Apache-Tomcat-Default-Passwords.mdown">'Apache Tomcat Default Credentials'</a></li>
	</ul>
<li> Brute-Forcing</li>
	<ul>
	<li> Metasploit: use auxiliary/scanner/http/tomcat_mgr_login</li>
	<li> With valid machine users create your own users file: set USER_FILE ./users.txt</li>
	<li> You could do this with Burp Suite Repeater - make sure encodings are used e.g. brute force to base64 and send</li>
	</ul>

</ul>

### PHP
<b>PHPSESSID:</b>
- <b> httponly </b> flag is not set? This flag indicates that javascript cannot interact with the cookie (and prevents many XSS attacks)
{: .important }
Developers working with session IDs and secure session management <b> must </b> be setting the <b>httponly</b> flag


## Directory Fuzzing / Busting

### Tools
- dirb
- wfuzz
- ffuf
- nikto (vulnerability scanning)
- hydra (brute-forcing)
- sqlmap

#### Command Reference
- ffuf/wfuzz Authentication Bypass - Username Enumeration (providing a valid list of usernames)
```
fuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.68.173/customers/signup -mr "username already exists"
```
- Brute force from the created list using ffuf or wfuzz:
```
ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.68.173/customers/login -fc 200
```


## Vulnerabilities
<b>"target=_blank" vulnerability</b>
- When used, the page we're linking to <i> gains partial access to the source page</i> via a window.opener object (similar to an open redirect vulnerability or tab nabbing)
Example Attack:
- Create a fake page, get it shared (e.g. Facebook is known for opening links via _blank) - every time someone clicks the link it will execute this:
``` window.opener.location = 'https://fakesite/facebook.com/phishing-page.html'; ```
- This would ultimately redirect to a page that asks for the user to re-enter their Facebook password

#### Brute-Forcing
- hydra
- wfuzz


