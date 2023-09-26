# nginx install 
add source list

```sh
sudo nano /etc/apt/sources.list.d/nginx.list
```

add this repo 
```sh
deb [arch=amd64] http://nginx.org/packages/mainline/ubuntu/ bionic nginx
deb-src http://nginx.org/packages/mainline/ubuntu/ bionic nginx
```

verify key
```sh
wget http://nginx.org/keys/nginx_signing.key
sudo apt-key add nginx_signing.key
sudo apt update
```

In Ubuntu 22 need to install libssl1_1.1.1
```sh
sudo wget http://archive.ubuntu.com/ubuntu/pool/m/libssl1.1_1.1.1f-1ubuntu2_amd64.deb
sudo dpkg -i libssl1.1_1.1.1f-1ubuntu2_amd64.deb
```
install specific version
```sh
sudo apt-get install nginx=1.23.4-1~bionic
```


|command | README |
| ------ | ------ |
|ngnix -V | show version and plugins |
