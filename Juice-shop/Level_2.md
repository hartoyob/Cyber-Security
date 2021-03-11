# Juice-Shop - Level 2

## Chalanges' solutions

<hr>

**Name:** Admin Section </br>
**Description:** Access the administration section of the store. </br> 
**Category:**  Broken Access Control </br>
**Solution:**
<p>
Solution goes here
</p>

<hr>

**Name:**  Login MC SafeSearch </br>
**Description:** Log in with MC SafeSearch's original user credentials without applying SQL Injection or any other bypass. </br> 
**Category:**  Sensitive Data Exposure  </br>
**Solution:**
<p>

Since i have no idea of the credentials, i went ahead and searched in the products to check if Mc SafeSearch posted some review in any product <br>
```
Product: Juice Shop "Permafrost" 2020 Edition
Review: mc.safesearch@juice-sh.op 🧊 Let it go, let it go 🎶 Can't hold it back anymore 🎶 Let it go, let it go 🎶 Turn away and slam the door ❄️
```

Alright, now we need the password. Since i could not find anything related to it, i looked at the hint which said : Listen to "Protect Ya Passwordz" <br>

In the song it states that the initial password is his first dog name (Mr.Noodles) with the O's as 0's

```
mc.safesearch@juice-sh.op:Mr. N00dles
```
</p>

<hr>

**Name:** Security Policy </br>
**Description:**  Behave like any "white-hat" should before getting into the action.</br> 
**Category:**  Miscellaneous</br>
**Solution:**
<p>
This demonstrates a standard propose, did not really knew that it existed but seems pretty awesome
</p>

<hr>

**Name:**  View Basket</br>
**Description:**  View another user's shopping basket. </br> 
**Category:**  Broken Access Control</br>
**Solution:**
<p>

Tampering the request by sending a different user id does not work, the token is verified. But we can change the local cookies/session. By changing them in the browser developers tools **Session storage** section (by changing the bid, which might stand for basket id), we solve this challenge 

</p>

<hr>

**Name:**  Visual Geo Stalking</br>
**Description:**  Determine the answer to Emma's security question by looking at an upload of her to the Photo Wall and use it to reset her password via the Forgot Password mechanism. </br> 
**Category:** Sensitive Data Exposure </br>
**Solution:**
<p>

Downloading the image and uploading at TinEye or Google did not provided anything <br>
Exfiltrating the image (in hope for GPS coordinates or any information) nothing else was found <br>

By zooming at the image, we can see a window with a sign with a name **"ITsec"**, which turns out to be the secret answer to the **emma@juice-sh.op** email ! <br>

</p>

<hr>

**Name:**  Meta Geo Stalking </br>
**Description:**  Determine the answer to John's security question by looking at an upload of him to the Photo Wall and use it to reset his password via the Forgot Password mechanism. </br> 
**Category:** Sensitive Data Exposure </br>
**Solution:**
<p>

First we need to know what is the question
```
What's your favorite place to go hiking
```

Using the above techniques, we can exfiltrate some information from it, such as GPS coordinates

```
cmd: exiftool -gpslatitude -gpslongitude john_image.png
GPS Latitude                    : 36 deg 57' 31.38" N
GPS Longitude                   : 84 deg 20' 53.58" W
```

Using the coordinates itself was a pain, so i used [this tool](https://tool.geoimgr.com/) which located to **Daniel Boone National Forest** which is, in fact, the answer to the question

</p>

<hr>

**Name:** Password Strength </br>
**Description:** Log in with the administrator's user credentials without previously changing them or applying SQL Injection. </br> 
**Category:**  Broken Authentication </br>
**Solution:**
<p>

From the description and the name we can pretty much assume that it needs to be solved using bruteforce! <br>

The tricky part is finding the correct wordlist! 

```
admin@juice-sh.op:admin123
```

</p>

<hr>

**Name:** Login Admin </br>
**Description:**  Log in with the administrator's user account. </br> 
**Category:** Injection  </br>
**Solution:**
<p>

Well this was solved with the afore challenge, but since the category is different, it is not technically solved. <br>

From the previous level challenges we saw that the application is vulnerable to sql injection <br>

So if we craft the following login payload, we get authenticated as the administrator

```
admin@juice-sh.op' or 1==1--:w
```

</p>

<hr>

**Name:** Admin Section </br>
**Description:** Access the administration section of the store. </br> 
**Category:**  Broken Access Control </br>
**Solution:**
<p>

If we open the file **main-es2018.js**, and search for **admin** we can see the route to the **administration** page 

```
16243: path: 'administration',
16244: component: Za,
```

</p>

<hr>

**Name:**        Five-Star Feedback   </br>
**Description:** Get rid of all 5-star customer feedback.   </br> 
**Category:**    Broken Access Control   </br>
**Solution:**
<p>

Just access the administration page and delete an entry

</p>


<hr>

**Name:**         Deprecated Interface  </br>
**Description:**  Use a deprecated B2B interface that was not properly shut down.  </br> 
**Category:**     Security Misconfiguration  </br>
**Solution:**
<p>

This one i could not find by myself (a reminder to always think outside the box). The issue is under the complaint form which allows a file upload <br>

The thing is that it supports more types than the ones listed in the file explorer (when picking the files) and, when a file which is not listed there is used, a **410 Gone** response is received and the challenge is solved... <br>

Again, from the hints and the description this is not something that is understandable (i guess)

</p>

<hr>

**Name:**       Weird Crypto    </br>
**Description:** Inform the shop about an algorithm or library it should definitely not use the way it does.    </br> 
**Category:**   Cryptographic Issues     </br>
**Solution:**
<p>

From the hints, it is stated that there is 5 ways to solve this <br>

The way i've done it was to check the source code on github, specially the unaccessible ftp files (which are in the repo) and i noted in the dependencies (**/ftp/package.json.bak**) there was the entry **hashids:1.1**. <br>

So i downloaded the code and greped all of it for **hashid**, and got an interesting result from **/test/api/feedbackApiSpec.js**
```
cmd: grep -RE "hashid"

test/api/feedbackApiSpec.js:            comment: 'Lousy crap! You use sequelize 1.7.x? Welcome to SQL Injection-land, morons! As if that is not bad enough, you use z85/base85 and hashids for crypto? Even MD5 to hash passwords! Srsly?!?!',
``` 

It also states MD5 to hash passwords, lets confirm that <br>

``` 
cmd: grep -RE "(MD5|md5)"

lib/insecurity.js:exports.hash = data => crypto.createHash('md5').update(data).digest('hex')
```

Which can be seen being used for hashing passwords

``` 
Cmd: grep -RE "insecurity"
(...)
models/user.js:        this.setDataValue('password', insecurity.hash(clearTextPassword))
``` 

Since the md5 stuff in that feedback is accurate, i assume that the remaining are correct (as answer) 
</p>

<hr>

**Name:**       Reflected XSS    </br>
**Description:**Perform a reflected XSS attack with < iframe src="javascript:alert(`xss`)">. (This challenge is potentially harmful on Docker!)    </br> 
**Category:**   XSS     </br>
**Solution:**
<p>

In order for this to work, we need to find some page that uses the argument (passed via url) to display information. This argument must also be displayed in the content <br>

After digging around the app, i found that the tracking page (accessible through the orders->truck icon) uses the tracking id in the url and so it might be used to solve this challenge (**(url)/track-result?id=track_id_here**) <br>

Although the search functionality (used for the DOM-xss) also have the same characteristics, the payload does not trigger as correct solution for this challenge but it triggers the XSS so i assume that the juice-shop needs it to be specifically done in the tracking page (seems harcoded to avoid conflicts). 

</p>






