---
layout: post
title:  "A First, Hands On Review Of Letsencrypt With nginx On Ubuntu 14.04 LTS"
date:   2015-12-05 16:48:10 +0100
categories: letsencrypt, nginx, primer
---


There has been lots of announcements and previews of ["letsencrypt"][letsencrypt]. Now the public beta phase of the project has begun and I want to get my hands dirty to give it a try.

The concept is appealing to me, but at this point I only do have a rough idea what is coming ahead. What I do understand right now is:

* certificates are for free [("as in beer")][asinbeer].
* certificates will be accepted by most or all web browsers. (no "untrusted certificate, add exception dialogue" any more, driving your dad away from using your services)
* certificate renewal can be automatised. At least it can be archived by running a command on the web server.
* certificate signatures have a maximum validity of 90 days.
* integration with apache2 is better, nginx is not as mature presently.
* I think that integration in a web server, running [multiple named virtual hosts][namebasedvirtualhosting] is not integrated and needs the most manual intervention.


So lets get started. My prerequisites are:

1. I an running Ubuntu 14.04 LTS
1. I am using nginx. 
1. I am serving [multiple named virtual hosts][namebasedvirtualhosting] from nginx.

## First Orientation

1. You don't need an account for using letsencrypt.
  - it is recommended to give an email address which males sense. So I do use an anonymous email address from [the anti spam service spammotel.com][spammotel.com]
1. The sources are available at [Github][letsencryptgithub].
  - Scroll down the [Github][letsencryptgithub] page to review the projects README file. This will give you a neat rundown.
1. The extensive documentation is available at [readthedocs][letsencryptgithub-docs].

## Installing 

1. If the client is not available from you distributions package management (as it isn't in my Ubuntu version), you need to git clone the github repository.
1. If you don't have git installed, on Ubuntu run apt-get install git-core (running apt-get update beforehand is recommended).
1. If you have cloned the github repo, enter the new directory and run ./letsencrypt-auto --help. On the first run this will install all required packages for the client from your distributions package management. 

Done!

I guess that on successive runs this also will pull in updates of these packages if needed. Anyway, you are able to keep letsencrypt up to date by:

- pulling git hub, which will probably be done by running `./letsencrypt-auto ...`
- running `letsencrypt-auto`, which you will at a maximum 90 days cycle to resign your certificate,
  - checking the github repo
  - and pulling in the updates from your distros package management, which you also will do on a frequent basis, anyway. 


### Installing: Reallity Check

- I already had git installed (of course ;-) )
- git clone was a breeze as always
- did get the "InsecurePlatformWarning" during first run, (see next chapter)
- Rerun was the same, but OK.

  
#### Insecure Platform Warning

The first run of ./letsenvrypt-auto --help gives me

> Updating letsencrypt and virtual environment dependencies...../root/.local/share/letsencrypt/local/lib/python2.7/site-packages/pip/_vendor/requests/packages/urllib3/util/ssl_.py:90: InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. For more information, see https://urllib3.readthedocs.org/en/latest/security.html#insecureplatformwarning.
>
>  InsecurePlatformWarning
>
> ./root/.local/share/letsencrypt/local/lib/python2.7/site-packages/pip/_vendor/requests/packages/urllib3/util/ssl_.py:90: InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. For more information, see https://urllib3.readthedocs.org/en/latest/security.html#insecureplatformwarning.
>
>  InsecurePlatformWarning
> 
> Command "/root/.local/share/letsencrypt/bin/python2.7 -c "import setuptools, tokenize;__file__='/tmp/pip-build-xuiQq1/cryptography/setup.py';exec(compile(getattr(tokenize, 'open', open)(__file__).read().replace('\r\n', '\n'), __file__, 'exec'))" install --record /tmp/pip-LbqOu0-record/install-record.txt --single-version-externally-managed --compile --install-headers /root/.local/share/letsencrypt/include/site/python2.7/cryptography" failed with error code 1 in /tmp/pip-build-xuiQq1/cryptography


After having read 

- [The link from the error message][Warninglink01]
- [The issue entry #1362 from the projects Github bug tracker][InsecurePlatformWarningLetsencryptIssue1362]

I decided to just ignore it. There are some hints that my version of python may not support [server name indication (SNI)][SNI], which might relevant for my [name based virtual hosting set-up][namebasedvirtualhosting]. But we'll see.

The next run I did get the same, but that's OK for now.

## Generating the First Certificate

Two things keep me from using the fully integrated approach:

- using nginx (there is no working nginx plug-in)
- hosting multiple sites on one instance (aka [name based virtual hosting set-up][namebasedvirtualhosting])

So I give it a first go with:

- using the [webroot plugin][letsencrypt-docs-webroot], which I assume is the right choice for future automation on name based virtual hosts.
- creating a config file to minimise interaction.

### Config File

- Create ```/etc/letsencrypt```
 - it turned out that this is the default directory which is used by letsencrypt itself to store all necessary data. But we can use this too.
- Create one letsencrypt config file per certificate (read as per named virtual host in your web server)

{% highlight ApacheConf %}
# This is an example of the kind of things you can do in a configuration file.
# All flags used by the client can be configured here. Run Let's Encrypt with
# "--help" to learn more about the available options.

# Use a 4096 bit RSA key instead of 2048
rsa-key-size = 4096

# Always use the staging/testing server
# Don't use it for production as it will give you a not trusted CA called "happy hacker fake CA"
# So I did comment it out.
#server = https://acme-staging.api.letsencrypt.org/directory

# Uncomment and update to register with the specified e-mail address
# email = foo@example.com

# Uncomment to use a text interface instead of ncurses
text = True

# Uncomment to use the standalone authenticator on port 443
# authenticator = standalone
# standalone-supported-challenges = tls-sni-01

# Uncomment to use the webroot authenticator. Replace webroot-path with the
# path to the public_html / webroot folder being served by your web server.
authenticator = webroot
webroot-path = /var/www/myexample/

domain my.example.de
email XXXXXXXXXXXXXXX@spammotel.com
{% endhighlight %}


Now running ```./letsencrypt-auto -c /etc/letsencrypt/my.example.de certonly``` will get you a certificate. In the first run you have to nod at the license agreement manually.

> root@example:~/install/letsencrypt/bin/letsencrypt# ./letsencrypt-auto -c /etc/letsencrypt/my.example.de certonly
> Updating letsencrypt and virtual environment dependencies.......
> Running with virtualenv: /root/.local/share/letsencrypt/bin/letsencrypt -c /etc/letsencrypt/my.example.de certonly
> 
> -------------------------------------------------------------------------------
> Please read the Terms of Service at https://letsencrypt.org/documents/LE-
> SA-v1.0.1-July-27-2015.pdf. You must agree in order to register with the ACME
> server at https://acme-v01.api.letsencrypt.org/directory
> -------------------------------------------------------------------------------
> (A)gree/(C)ancel: A
> IMPORTANT NOTES:
>  - If you lose your account credentials, you can recover through
>    e-mails sent to XXXXXXXXX@spammotel.com.
>  - Congratulations! Your certificate and chain have been saved at
>    /etc/letsencrypt/live/my.example.de/fullchain.pem. Your
>    cert will expire on 2016-03-04. To obtain a new version of the
>    certificate in the future, simply run Let's Encrypt again.
>  - Your account credentials have been saved in your Let's Encrypt
>    configuration directory at /etc/letsencrypt. You should make a
>    secure backup of this folder now. This configuration directory will
>    also contain certificates and private keys obtained by Let's
>    Encrypt so making regular backups of this folder is ideal.
>  - If like Let's Encrypt, please consider supporting our work by:
> 
>    Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
>    Donating to EFF:                    https://eff.org/donate-le
> 
> root@example:~/install/letsencrypt/bin/letsencrypt#
	

This did get you all the cryptographic material you need in the directory ```/etc/letsencrypt/lice/$DOMAINNAME``` (here e.g. ```/etc/letsencrypt/live/my.example.com```).


## Integrating the Certificates into the Web Server

This part is fairly easy, if done right (as allways ;-) ).

Just replace the old paths pointing to your former certificates in the "server" section of your nginx configuration to the certificates in ```/etc/letsencrypt/live/my.example.com``` :

- point the "ssl_certificate_key" entry to  ```/etc/letsencrypt/live/my.example.de/privkey.pem```:
 - ```ssl_certificate_key /etc/letsencrypt/live/my.example.de/privkey.pem;```
- point the "ssl_certificate" entry to the fullchain ```/etc/letsencrypt/live/my.example.de/fullchain.pem```:
 - ```ssl_certificate /etc/letsencrypt/live/my.example.de/fullchain.pem;```


Please make sure to use the "fullchain.pem" file for nginx, as there is no means of providing the certificate and the CA chain separately (see [NGINX Documentation on CA Chains][nginxsslchains]). But letsencrypt provides you with all the necessary files from the start.

Unfortunately nginx requires a restart to use the new certificates. After doing so  you should be able to browse your website using a publicly trusted certificate.

If you are wondering what operating systems and browsers do trust the CA, [read this post][letsencrypt-supported] 

## Testing the Security of Your Setup

Now that you successfully have deployed you ssl certificate, you might tend to think you're done and safe. But as we're talking security, having ssl encryption running is one thing of many. The really important part is to make sure the configuration details are right and e.g. do not offer any protocols or ciphers to the client, which are insecure. As this is a topic where some clever guys write their master thesis on, we cannot go into such depths for an every day setup.

My solution for the general housekeeping is to have my ssl enabled web sites checked by some free ([as in beer][asinbeer]) services, which will the give me advice where my setup is insecure. My favourite service is [Qualys SSL Labs' SSL Server Test][QualysSslServerTest]. Please be aware that this service, as you use it, will contact your web server and send requests to it.

The result of the scan is very detailed and in case of any flaws found, they will provide you with advice for the most common web servers on how to fix it.

Do these checks on a regular basis as the situation on security can change daily!
  
## Renewal of the Certificate

You must be aware that the signatures of the certificates have a relatively short lifetime of 90 days. Before the signatures expire, you must renew the certificate. Otherwise your users will be presented with warnings (at best) or denied access to your services. But before you start complaining about the short lifetime, there are [some good points to do so][letsencrypt-shortlifetime]. As renewal can be automated, this should not be an issue at all. But, if put to the reality test, it is not as easy as it seems at first glance.

### The Simple Version
The simple version (from the documentation) is:

- with the setup above setup, run ```./letsencrypt-auto -c /etc/letsencrypt/my.example.de certonly``` 

but this will give you some interactive chat:

```
Do you want to renew and replace this certificate with a newly-issued one?
-------------------------------------------------------------------------------
(R)eplace/(C)ancel:
```

The solution to this is to add the lines 

{% highlight ApacheConf %}
# make renewal noninteractive
renew-by-default
{% endhighlight %}


This will enable you to create a file ```/etc/cron.d/letsencrypt-renew-my.example.de```

{% highlight ApacheConf %}
# m h dom mon dow user  command
22 03 3 */2 *       letsencrypt-auto -c /etc/letsencrypt/my.example.de certonly; service nginx stop; service nginx start
{% endhighlight %}

This will (at least under recent Ubuntu distributions) renew the certificate every two months in the middle of the night of day three.



### The real world version

The simple version might be appealing but it has no safeguard against any failures at any point. 

- The cron job might not run without being detected (e.g. the server was down at the time)
- The certificate rewewal might fail due to 
  - failure of letsencrypt-auto to upgrade your distribution
  - failure of letsencrypt-auto to pull from github
  - failure of letsencrypt-auto to contact its servers
  - failure of letsencrypt-auto to obtain or save the new certificates
- enginx might fail to shut down or start


So I do feel not comfortable to just let it run this way. There are some posts on the net with solutions, but I think I may bake my own.

For the time being you might want to look at:

* [a script that is more how think it should be](http://eblog.damia.net/2015/12/03/lets-encrypt-automation-on-debian/)
* [community post at letsencrypt website](https://community.letsencrypt.org/t/how-to-automatically-renew-certificates/4393/2)


# Conclusion

"letsencrypt" takes some effort to set it up at present, mainly due to the lack of user friendy documentation. At present you have to goolge lots of the details you need to get it doing its job reliably in the long term. But even today it is taking away a lot of the struggles you have to go through to obtain a publicly accepted SSL certificate. But the idea is totally right. And taking the cost factor away from the "basic" ssl cerificate to give the user the choice of communicating privately is the right direction. This will be not the favorite of the commercial CA's but the have the [Extended Validation Certificates][https://en.wikipedia.org/wiki/Extended_Validation_Certificate] to maintain their business model and their commercial existence.

It will not solve some of the basic criticism PKI structures are taking, like having to trust the CA. But this is not the topic at this level.

If the underlying protocols ar well crafted and the infrastucture is run appropriatly, letsencrypt has a large potential. In case it's not, we'll read about that on the news in the future, for sure.

With a growing set of documentation, there is the potential to give SSL the push, especially for the small setups, that will make encrypted communication on the internet the normal case.  



[letsencrypt]: https://letsencrypt.org/
[letsencryptgithub]: https://github.com/letsencrypt/letsencrypt
[letsencrypt-docs]: https://letsencrypt.readthedocs.org/en/latest/index.html
[letsencrypt-supported]: https://community.letsencrypt.org/t/which-browsers-and-operating-systems-support-lets-encrypt/4394
[letsencrypt-shortlifetime]: https://letsencrypt.org/2015/11/09/why-90-days.html
[asinbeer]: http://www.howtogeek.com/howto/31717/what-do-the-phrases-free-speech-vs.-free-beer-really-mean/
[namebasedvirtualhosting]: http://httpd.apache.org/docs/2.2/en/vhosts/
[Warninglink01]:  https://urllib3.readthedocs.org/en/latest/security.html#insecureplatformwarning
[InsecurePlatformWarningLetsencryptIssue1362]: https://github.com/letsencrypt/letsencrypt/issues/1362
[SNI]: 	https://en.wikipedia.org/wiki/Server_Name_Indication
[letsencrypt-docs-webroot]: https://letsencrypt.readthedocs.org/en/latest/using.html#webroot
[spammotel.com]: http://www.spammotel.com/
[nginxsslchains]: http://nginx.org/en/docs/http/configuring_https_servers.html#chains
[QualysSslServerTest]: https://www.ssllabs.com/ssltest/analyze.html
