# Pi-hole Block Page
A user friendly "Website Blocked" page to add onto a [Pi-hole installation](https://pi-hole.net).

## What does this do?
The goal is to provide concise and relevant feedback to the end user, so they are better informed about the site they are trying to visit.

When a user browses to a blacklisted domain (For example, `doubleclick.net`), they will be presented with the block page (*as pictured below*). As an Internet connection is generally shared with a number of other people (and not just the person who set up Pi-hole on the network), this provides excellent visual feedback showing them what domain was blocked, what the most "notable" list it was featured in, and how they can go about resolving the issue (by emailing the Pi-hole admin).

In this case, `doubleclick.net` was found in `https://s3.amazonaws.com/lists.disconnect.me/simple_malvertising.txt` which I have classed as Malicious.

![Pi-hole Block Page](http://i.imgur.com/vrjiy6R.png)

When one attempts to access any non HTML resource (IE: not HTML, PHP, XML or RSS), the page will interpret this request as a "file" and will show the following image:

![Blocked by Pi-hole](https://wally3k.github.io/style/blocked.svg)

If the blockpage is accessed through an iframe, a 1x1 transparent GIF ~~will~~ should be shown.

## Install:
**DISCLAIMER:** While these instructions have been tested for my own setup, they have not been verified on any other build. While it shouldn't break, be prepared to troubleshoot if necessary.


````
html=$(grep server.document-root /etc/lighttpd/lighttpd.conf | awk -F\" '{print $2}')
sudo wget -q https://raw.githubusercontent.com/WaLLy3K/Pi-hole-Block-Page/master/index.php -O "$html/index.php"
sudo wget -q https://raw.githubusercontent.com/WaLLy3K/Pi-hole-Block-Page/master/phbp.php -O "/var/phbp.php"
sudo chmod 755 "$html/index.php"
[ ! -d "/etc/lighttpd/conf-enabled" ] && sudo mkdir -m 755 /etc/lighttpd/conf-enabled
echo -e '# Pi-hole "server.error-handler-404" override\nurl.rewrite-once = ( "pihole/index.php" => "/index.php" )' | sudo tee /etc/lighttpd/conf-enabled/phbp.conf
sudo service lighttpd force-reload
````

This script will not presume where the default document-root is, as [installations such as DietPi](https://github.com/Fourdee/DietPi/blob/master/dietpi/dietpi-software#L3552) are known to change this.

## Uninstall:

````
html=$(grep server.document-root /etc/lighttpd/lighttpd.conf | awk -F\" '{print $2}')
sudo rm -rf $html/index.php /var/phbp.php /etc/lighttpd/conf-enabled/phbp.conf
sudo service lighttpd force-reload
````

## Website Test Cases:

* http://192.168.1.x (Raspberry Pi IP) -- landing page
* http://pi.hole -- redirect to Pi-hole Admin Interface
* http://doubleclick.net/ -- site
* http://doubleclick.net/some/folder -- site
* http://doubleclick.net/some/content.php -- site
* http://doubleclick.net/some/content.php?query=true -- file
* http://doubleclick.net/file.exe -- file
* http://doubleclick.net/some/image.gif -- file
 
 
## Pre-Github changelog:

* 10SEP16: Removed $ from grep search
* 07DEC16: Cleaned up formatting, changed some variables & uploaded to GitHub
