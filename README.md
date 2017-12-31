# Lets Encrypt SSL certificate
Step by step guide to install FREE Let's Encrypt SSL certificate on shared hosting

In this tutorial we'll generate the free Let’s Encrypt certificate locally (I'm using Ubuntu Linux 16.04 LTS) and install it to your server using cPanel.

## You'll Need ##

- **An OS to install and run the Let’s Encrypt client**. As I said, I'm using Ubuntu 16.04, you can use VM to install it on Windows machine

- **cPanel access**. Your cPanel should allow SSL certificates to be installed manually (in this case I'm using GoDaddy hosting).

- **File upload access to the server**. I use FileZilla client.


## Installing the Let’s Encrypt Client ##

1. Launch the terminal and update Ubuntu.

    Type `sudo su` and enter root password once so you'll not be requested in following steps

    Then `apt-get update` to update linux packages repository
    
    Then install letsenceypt client using git:
    
    `git clone https://github.com/letsencrypt/letsencrypt`
    

2. Once its done, go to the directory where the client is installed.
   For me its: `cd letsencrypt`
   

## Generating the Lets Encrypt Certificate ##

3. Inside the `letsencrypt` directory, type:

   `./letsencrypt-auto certonly -a manual --rsa-key-size 4096 -d bumbu.tv -d www.bumbu.tv`
   
   > Don't forget to swap `bumbu.tv` to your own
   
4. The client will prompt you if its OK to log your IP. Choose Yes.

![alt text](https://github.com/BumbuKhan/lets-encrypt-certificate/raw/master/screenshots/1.png)

5. Now, don't touch anything yet or you will have to restart the generation process!

## Confirming Domain Ownership ##

Notice that below the prompt is an instruction on how to confirm ownership of your domain: Make sure your web server displays the following content `5ovykElisoTZei4qxursJs4jyiUYCR0ZBnBYuk8yGvY.09c2DPZt0l1WWZFj6OhhzEC5Hj3E1XprqH9vDF76_Nw` at `http://bumbu.tv/.well-known/acme-challenge/5ovykElisoTZei4qxursJs4jyiUYCR0ZBnBYuk8yGvY` in order to do so...

![alt text](https://github.com/BumbuKhan/lets-encrypt-certificate/raw/master/screenshots/2.png)

6. Open Filezilla, navigate to `public_html` and add folders `.well-known/acme-challenge/`

7. Create file `5ovykElisoTZei4qxursJs4jyiUYCR0ZBnBYuk8yGvY` (yep, there is no extension) and put `5ovykElisoTZei4qxursJs4jyiUYCR0ZBnBYuk8yGvY.09c2DPZt0l1WWZFj6OhhzEC5Hj3E1XprqH9vDF76_Nw` as a content

> NOTE: file name & content will be different in your case

8. Once you are done, go back to the terminal and press the enter. Lets Encrypt client will generate one more confirmation. Just repeat step number 7.

9. And finaly you must get something like:

![alt text](https://github.com/BumbuKhan/lets-encrypt-certificate/raw/master/screenshots/4.png)


## Installing the Certificate in cPanel ##

![alt text](https://github.com/BumbuKhan/lets-encrypt-certificate/raw/master/screenshots/3.png)

10. Navigate to folder where generated sercificates are by typing `cd /etc/letsencrypt/live/bumbu.tv/`

> NOTE: folder `bumbu.tv` is in my case

11. Login to your cPanel. Copy and paste the SSL info from these files into the cPanel SSL panel:

    - Certificate (CRT) is `cert.pem`
    - Private Key is `privkey.pem`
    - Certificate Authority Bundle: (CABUNDLE) or CA is `chain.pem`
  
![alt text](https://github.com/BumbuKhan/lets-encrypt-certificate/raw/master/screenshots/5.png)
  
![alt text](https://github.com/BumbuKhan/lets-encrypt-certificate/raw/master/screenshots/6.png)

> Note: You can use the cat command to view text files in the terminal. Eg. `cat cert.pem`

And that's it 🤘


## Redirect all request to https ##

Add this in your .htaccess:

 ```# BEGIN Force SSL
  # This should be the first rule before other rules
  <IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
  </IfModule>
# END Force SSL
