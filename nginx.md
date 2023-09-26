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

## commands

|command | README |
| ------ | ------ |
|nginx -V | [show version and plugins][mywiki] |
|nginx -t | test nginx when you change the config |

## nginx extra 
### header filter
```sh
sudo apt install nginx-extras
```
in /etc/nginx/nginx.conf and added the following as first line:
load_module modules/ngx_http_headers_more_filter_module.so;

and then added the following to the http block:
more_set_headers              Server: Kaman;


[mywiki]: <https://github.com/rasoulpoordelan/wiki/>
