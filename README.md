StandaLock
==========
A standalone slide bar that your visitors unlock using _drag and drop_ to prevent spambots for harvesting sensitive, private information stored in your web pages such as email address and phone number.

![StandaLock in action](http://timlegrand.github.io/assets/images/StandaLock.gif)


Why a slidelock?
---------------
Wondering about privacy and [spambot harvesting](http://en.wikipedia.org/wiki/Email_address_harvesting)? Many articles on the web are discussing about the best ways to protect personal information (PI) and prevent bots from using them for spam.
A short resume of the state of the art could be:
  1. Spambots read HTML, so do not write PI in your web pages.
  2. The only way to know if the visitor is not a bot is making him proving is human.
  3. GUI-based actions (like reading a Captcha, listing to audio, picking an object of a certain kind or drawing a sheep) have the best results since no spambot can afford the computing power needed to process such inputs in a reasonnable time for brute force attacks.
  4. Slidelocks are a simple, user-friendly, device-agnostic way to do it compared to Captchas which require text input, focus, and multiple retries to get it unlocked.

In addition, these countermeasures support clickable email links (which cannot be done with HTML offuscation, for example).

Why standalone?
---------------
  5. It turns out that every slidelock found on the web is based on a client-server model implying various handshakes that end with the retrieval of PI from the server. But what if you cannot ask the server? What if the server cannot run PHP or Python? Today many websites are static-pages servers (Harp, Jekyll, etc.) as for GitHub.io. In these cases such slidelocks won't work.
  6. Spambots can read Javascipt. So writing

     ```javascript
     my_email = "personal@example.com";
     document.getElementById('my_contact_div').innerHTML = '<p>' + my_email + '</p>';
     ```
     will not work either since the spambot can clearly read the line `my_email = "personal@example.com";`.
  7. Embedding encrypted PI and decrypting it with Javascript at render time is useless since the rendered web page read by spambots will contain clear PI.
  8. So solution is to decrypt and render PI only if visitor is human, that is, if he manually unlocked the slide lock via GUI.

This solution is in **pure Javascript**, so no communication with server is required:
* runs on static websites such as Jekyll servers,
* still runs if you loose network connection on your mobile,
* don't wait for the server to respond, get unlocked information instantly!

How it works?
---------------
  1. Download [the zip archive](https://github.com/timlegrand/StandaLock/archive/master.zip) then uncompress in your website folder.
  2. Add this in your HTML:
     ```html
     <canvas id="progress" width="469" height="69" style="cursor: pointer;"></canvas>
     <div id="contact"></div>
     <script src="/path/to/standalock.js"></script>
     ```
     Where "contact" is the div that will contain your personal information. If you need to give another name to this div, make sure you change the JavaScript accordingly (see below).

  3. Edit the ```securedAction()``` function in the ```standalock.js``` file.
     Here is an implementation provided as an example:
     ```javascript
     function securedAction() {
     
         // 1- Store encrypted info, e.g. here just the Base64 encoding
         // of an email address and phone [obtainded with window.btoa()]
         var bm = "cHJpdmF0ZUBleGFtcGxlLmNvbQ==";
         var bp = "MSg1NTUpNTU1LTU1NTU=";
     
         // 2- Implement your own 'decrypt' function here
         function decrypt(encrypted_msg) {
             decrypted_msg = window.atob(encrypted_msg);
             return decrypted_msg;
         }
     
         // 3- Safely insert sensitive information in your HTML document
         insertion  = '<p>';
         insertion += '<a href="mailto:' + decrypt(bm) + '">' + decrypt(bm) + '</a>';
         insertion += '&nbsp;&nbsp;|&nbsp;&nbsp;';
         insertion += '<a href="tel:' + decrypt(bp) + '">' + decrypt(bp) + '</a>';
         insertion += '</p>';
         document.getElementById('contact').innerHTML = insertion;
     }
     ```
     Don't forget that automatically calling ```securedAction()``` on page load, for example, is **not** secure. You should always make sure your visitor is human before *securedAction()* is performed, that is, he had unlocked the slider, and this is the precise purpose of this software.

License
-------
This software (graphics artwork included) is provided under the MIT license. Please read LICENSE file for further information.
The canvas implementation is based on Red Hammond HTML5 canvas works you can find on [his GitHub page](https://github.com/rheh/HTML5-canvas-projects/tree/master/progress). A detailed explanation of the HTML5 canvas construction of his original slidebar can be found [here](http://geeksretreat.wordpress.com/2012/08/13/a-progress-bar-using-html5s-canvas/). 
Full credit for the original digital image goes [here](http://365psd.com/day/106/) for source the PSD (this version was modified).

