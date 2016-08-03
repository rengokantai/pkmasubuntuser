####pkmasubuntuser
#####Chapter 4. Connecting to Networks
######Setting the hostname
```
hostnamectl set-hostname y.me
```
in /etc/hosts
```
127.0.1.1 y.me #not 127.0.0.1
```
######Managing network interfaces
```
ip link set enp0s3 up 
ifconfig enp0s3 up #same
```

```
iface enp0s3 inet static
    address
    netmask 
    broadcast
    dns-search local.lan
    dns-nameservers
```
restart:
```
systemctl restart networking.service
```
old
```
/etc/init.d/networking restart
```
install tmux,test:
```
tmux
top
```
ctrl+b D to back,tmux to re attach.
######Understanding Linux name resolution
```
vim /etc/nsswitch.conf
```
this line
```
hosts:          files dns
```
check
```
/etc/hosts
/etc/resolv.conf
```
######Getting started with OpenSSH
```
systemctl enable ssh
```
old
```
service ssh start
update-rc.d ssh defaults
```
######Simplifying SSH connections with a ~/.ssh/config file
```
vim ~/.ssh/config
```
edit
```
Host k
    Hostname host
    Port 2222
    User ke
```
then
```
ssh k
```
same as
```
ssh -p 2222 ke@host
```
#####Chapter 5. Managing Software Packages
######Searching for packages
```
apt-cache search php
apt-cache show php
```
######Managing software repositories
ppa
```
apt-add-repository ppa:ondrej/mariadb-10.0
```

#####Chapter 7. Managing Your Ubuntu Server Network
######Serving IP addresses with isc-dhcp-server
```
apt-get install isc-dhcp-server -y
```
######Serving IP addresses with isc-dhcp-server
```
apt-get install bind9 -y
```
then
```
vim /etc/bind/named.conf.options
```
edit
```
   forwarders {
    8.8.8.8;
    8.8.4.4;
};
```



#####Chapter 8. Accessing and Sharing Files
######Setting up NFS shares
```
apt-get install nfs-kernel-server -y
```
config
```
mv /etc/exports /etc/exports.orig
mkdir -p /etc/exports/{backup,documents,public}
```

######Transferring files with rsync
below are same
```
rsync -r /home/test /backup
rsync -r /home/test /backup/
```
below are same
```
rsync -r /home/test/ /backup
rsync -r /home/test/* /backup
rsync -r /home/test/ /backup/
rsync -r /home/test/* /backup/
```
keep metadata
```
rsync -a /home/myuser /backup  (same as -rlptgoD)
```
real sync, delete file in target if src does not exist
```
rsync -av --delete /src /target
```
backup if overwritten
```
rsync -avb --delete /src /target
```
assign baskupdir
```
rsync -avb --backup-dir=../test3 /etc/postgresql/9.5/main/ test2 //test3 relative to test2 path
```
######Transferring files with SCP
```
scp -P 22222 -r /src /target
```
######Mounting remote filesystems with SSHFS(temp solution)
```
apt install sshfs
sshfs ke@192.168.1.1:/test /test
```
```
umount /test
```
if failed, use
```
fusermount -u /test
```
or in fstab
```
ke@192.168.1.1:/test    /test   fuse.sshfs  rw,noauto,users,_netdev  0  0
```




#####Chapter 10. Serving Web Content
######Installing and configuring Apache
env save in (/etc/apache2/envvars)
```
<VirtualHost 192.168.1.1:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/a

    ErrorLog ${APACHE_LOG_DIR}/a-error.log
    CustomLog ${APACHE_LOG_DIR}/a-access.log combined
</VirtualHost>
```

multiple servers
```
<VirtualHost *:80>
    ServerName a.com
    DocumentRoot /var/www/a
</VirtualHost>

<VirtualHost *:80>
    ServerName b.com
    DocumentRoot /var/www/b
</VirtualHost>
```
######Installing additional Apache modules
```
aptitude search libapache2-mod
```
or
```
apt-cache search libapache2-mod
```
view a list of modules built-in to Apache
```
apache2 -l
```
list al modules
```
a2enmod
```
######
selfsign
```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/certs/server.key -out /etc/apache2/certs/server.crt
```
(non self sign)need to submit csr:
```
openssl req -new -newkey rsa:2048 -nodes -keyout server.key -out server.csr
```
```


































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































#####
```
mkdir /git
chown ke:ke /git
cd /git
git init --bare apache2  #Initialized empty Git repository in /git/apache2/
```
clone:
```
git clone 192.168.1.101:/git/apache2
cp -rp /etc/apache2 /etc/apache2.bak   #create bak folder
mv /etc/apache2/* /git/apache2/
rm /etc/apache2
```

change privilege to root except .git
```
find /git/apache2 -name '.?*' -prune -o -exec chown root:root {} +
```
[details](http://stackoverflow.com/questions/1489277/how-to-use-prune-option-of-find-in-sh)  
linking
```
ln -s /git/apache2 /etc/apache2
```
