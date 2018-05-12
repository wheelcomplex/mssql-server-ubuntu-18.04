# workaround for install Microsoft's SQL Server (mssql) on Ubuntu 18.04 X86_64 LTS

## WARNING: FOR TESTING  ONLY

* download latest package mssql-server_14.0.3025.34-3_amd64.deb from https://packages.microsoft.com/ubuntu/16.04/mssql-server-2017/pool/main/m/mssql-server/
```
cd ${HOME} && mkdir -p tmp/mssql/newpkg/DEBIAN/ && cd tmp/mssql
wget https://packages.microsoft.com/ubuntu/16.04/mssql-server-2017/pool/main/m/mssql-server/mssql-server_14.0.3025.34-3_amd64.deb
```
* unpack
```
dpkg-deb -x mssql-server_14.0.3025.34-3_amd64.deb newpkg/
dpkg-deb -e mssql-server_14.0.3025.34-3_amd64.deb newpkg/DEBIAN/
```
* modify 
```
sed -i -e 's#openssl (<= 1.1.0)#openssl (<= 1.1.1)#g' newpkg/DEBIAN/control
cat newpkg/DEBIAN/control | grep openssl
```
* repackage
```
dpkg-deb -b newpkg/ 18.04-mssql-server_14.0.3025.34-3_amd64_.deb
```

* first try, this will fail for dependencies
```
sudo dpkg -i 18.04-mssql-server_14.0.3025.34-3_amd64_.deb
```

* install dependencies
```
sudo apt install -f
```
* second try, this should be done
```
sudo dpkg -i 18.04-mssql-server_14.0.3025.34-3_amd64_.deb
```
* follow the messages from package to setup the mssql server
# Trouble shooting
* the database directory /var/opt/mssql should not put on zfs dataset, if you are using zfs, create a zvol and format as ext4/xfs for it.
* Error: "Dump collecting thread [4404] hit exception [6]. Exiting.", Fix: sudo usermod -a -G disk mssql, reason: mssql user can not access zvol.


### reference: 
* https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-ubuntu?view=sql-server-linux-2017
* https://gist.github.com/shamil/3140558
