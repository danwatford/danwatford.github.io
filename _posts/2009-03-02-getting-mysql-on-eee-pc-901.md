---
layout: post
---
I fancied doing some database work on the Eee PC so needed a DBMS to play with. Mysql is a very popular database system so would be worth getting installed.

The ASUS dpkg repositories for the eee pc don't contain mysql server, and I didn't want to mess around with other repositories. One day I'll probably install debian on this netbook and then be able to seamlessly install practically anything I want - but for now I'll just still with binary tar balls.

Checking on the eeeuser.com website and searching for mysql I came across several references to LAMP (Linux Apache Mysql Php) - basically defining a software development stack. I thought this would be a simpler way to get mysql running and have some other tools (namely phpMyAdmin) to boot.

Apachefriends.org have built a lamp distribution called xammp for linux. I downloaded version 1.7 from it from <a href="http://www.apachefriends.org/en/xampp-linux.html">http://www.apachefriends.org/en/xampp-linux.html</a>to /home/user/xampp-linux-1.7.tar.gz. The instructions at the website call for the tar.gz file to be extracted to /opt, but on my eee pc there isn't much space:

~~~
/home/user> df -h
Filesystem            Size  Used Avail Use% Mounted on
rootfs                680M  487M  159M  76% /
/dev/sda1             680M  487M  159M  76% /
none                  680M  487M  159M  76% /
tmpfs                 503M   12K  503M   1% /dev/shm
tmpfs                 128M  484K  128M   1% /tmp
/dev/sdb1              15G  1.2G   13G   9% /home
/dev/sda1             3.1G  2.8G  259M  92% /ro
~~~
/opt sits on /dev/sda1 and only has 159M left and the xampp distribution when extracted requires around 210M.

I extracted the downloaded tar.gz file by opening a terminal (Ctrl+Alt+t) and using the command:
<pre>tar xvfz xampp-linux-1.7.tar.gz</pre>
This command will extract the xampp distribution to directory /home/user/lampp. You can start the mysql and apache servers with the command:
<pre>sudo lampp/lampp start</pre>
but you will get error messages relating to lampp not being installed in /opt. To remedy this create a symbolic link from /opt to /home/user/lampp with the command:
<pre>sudo ln -s /home/user/lampp/ /opt/lampp</pre>
You can then launch the servers with:
<pre>sudo /opt/lampp/lampp start</pre>
You can then point your browser at http://localhost and hopefully see this:
![](/images/xampp_screenshot-737073.jpg)

From here you can click on the link to phpMyAdmin to get to some database management tools.