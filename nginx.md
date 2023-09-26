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
```sh
load_module modules/ngx_http_headers_more_filter_module.so;
``` 

and then added the following to the http block:  
```sh
more_set_headers              Server: Kaman;  
```

## dynamic module
first install
```sh
sudo apt-get install build-essential
sudo apt install libperl-dev libgeoip-dev libgd-dev
sudo apt-get install libpcre3-dev

```

Step 1: Obtain the NGINX Open Source Release
Determine the NGINX Open Source version that corresponds to your NGINX Plus installation. In this example, it’s NGINX 1.11.5.

```sh
 nginx -v
```
nginx version: nginx/1.11.5 (nginx-plus-r11)
Download the corresponding NGINX Open Source package at nginx.org/download:

```sh
 wget https://nginx.org/download/nginx-1.11.5.tar.gz
 tar -xzvf nginx-1.11.5.tar.gz
```
Step 2: Obtain the Module Sources
Obtain the source for the ‘Hello World’ NGINX module from GitHub:

```sh
 git clone https://github.com/perusio/nginx-hello-world-module.git
```
Step 3: Compile the Dynamic Module
Compile the module by first running the configure script with the --with-compat argument, which creates a standard build environment supported by both NGINX Open Source and NGINX Plus. Then run make modules to build the module:
```sh
 cd nginx-1.11.5/
 ./configure --with-compat --add-dynamic-module=../nginx-hello-world-module
 make modules
```
Copy the module library (.so file) to /etc/nginx/modules:
```sh
 sudo cp objs/ngx_http_hello_world_module.so /etc/nginx/modules/
```
Step 4: Load and Use the Module
To load the module into NGINX Plus, add the load_module directive in the top‑level (main) context of your nginx.conf configuration file (not within the http or stream context):

load_module modules/ngx_http_hello_world_module.so;
[source](https://www.nginx.com/blog/compiling-dynamic-modules-nginx-plus/)


[mywiki]: <https://github.com/rasoulpoordelan/wiki/>
[compmod]: <https://www.nginx.com/blog/compiling-dynamic-modules-nginx-plus/>
