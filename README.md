# workaround for install Microsoft's SQL Server (mssql) on Ubuntu 18.04 X86_64 LTS

## WARNING: FOR TESTING  ONLY

* download latest package mssql-server_14.0.3029.16-1_amd64.deb from https://packages.microsoft.com/ubuntu/16.04/mssql-server-2017/pool/main/m/mssql-server/
```
cd ${HOME} && mkdir -p tmp/mssql/ && cd tmp/mssql
wget https://packages.microsoft.com/ubuntu/16.04/mssql-server-2017/pool/main/m/mssql-server/mssql-server_14.0.3029.16-1_amd64.deb -O mssql-server.deb
```
* unpack
```
mkdir -p newpkg/DEBIAN/
dpkg-deb -x mssql-server.deb newpkg/
dpkg-deb -e mssql-server.deb newpkg/DEBIAN/
```
* modify 
```
sed -i -e 's#openssl (<= 1.1.0)#openssl (<= 1.1.1)#g' newpkg/DEBIAN/control

cat newpkg/DEBIAN/control | grep openssl
```
* optional modify(from [Mike](https://askubuntu.com/questions/1032532/how-do-i-install-ms-sql-for-ubuntu-18-04-lts/1035144?noredirect=1#comment1690259_1035144), thanks)
```
sed -i -e 's#libcurl3#libcurl4#g' newpkg/DEBIAN/control

cat newpkg/DEBIAN/control | grep libcurl
```
* repackage
```
dpkg-deb -b newpkg/ 18.04-mssql-server.deb
```

* install dependencies
```
sudo apt install libjemalloc1 libc++1 libsss-nss-idmap0
```

* install
```
sudo dpkg -i 18.04-mssql-server.deb
```

* follow the messages from package to setup the mssql server

# Trouble shooting
* the database directory /var/opt/mssql should not put on zfs dataset, if you are using zfs, create a zvol and format as ext4/xfs for it.
* Error: "Dump collecting thread [4404] hit exception [6]. Exiting.", Fix: sudo usermod -a -G disk mssql, reason: mssql user can not access zvol.


### reference: 
* https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-ubuntu?view=sql-server-linux-2017
* https://gist.github.com/shamil/3140558
