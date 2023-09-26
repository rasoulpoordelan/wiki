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

## enable mode security
2 – Install Prerequisite Packages
The first step is to install the packages required to complete the remaining steps in this tutorial. Run the following command, which is appropriate for a freshly installed Ubuntu/Debian system. The required packages might be different for RHEL/CentOS/Oracle Linux.
```sh
 apt-get install -y apt-utils autoconf automake build-essential git libcurl4-openssl-dev libgeoip-dev liblmdb-dev libpcre++-dev libtool libxml2-dev libyajl-dev pkgconf wget zlib1g-dev
```
3 – Download and Compile the ModSecurity 3.0 Source Code
With the required prerequisite packages installed, the next step is to compile ModSecurity as an NGINX dynamic module. In ModSecurity 3.0’s new modular architecture, libmodsecurity is the core component which includes all rules and functionality. The second main component in the architecture is a connector that links libmodsecurity to the web server it is running with. There are separate connectors for NGINX, Apache HTTP Server, and IIS. We cover the NGINX connector in the next section.

To compile libmodsecurity:

Clone the GitHub repository:
```sh
 git clone --depth 1 -b v3/master --single-branch https://github.com/SpiderLabs/ModSecurity

```
Change to the ModSecurity directory and compile the source code:
```sh
 cd ModSecurity
 git submodule init
 git submodule update
 ./build.sh
 ./configure
 make
 make install
 cd ..
```
The compilation takes about 15 minutes, depending on the processing power of your system.

Note: It’s safe to ignore messages like the following during the build process. Even when they appear, the compilation completes and creates a working object.

fatal: No names found, cannot describe anything.
4 – Download the NGINX Connector for ModSecurity and Compile It as a Dynamic Module
Compile the ModSecurity connector for NGINX as a dynamic module for NGINX.

Clone the GitHub repository:
```sh
 git clone --depth 1 https://github.com/SpiderLabs/ModSecurity-nginx.git
```
Determine which version of NGINX is running on the host where the ModSecurity module will be loaded:
```sh
 nginx -v
```
nginx version: nginx/1.13.1
Download the source code corresponding to the installed version of NGINX (the complete sources are required even though only the dynamic module is being compiled):
```sh
 wget http://nginx.org/download/nginx-1.13.1.tar.gz
 tar zxvf nginx-1.13.1.tar.gz
```
Compile the dynamic module and copy it to the standard directory for modules:
```sh
 cd nginx-1.13.1
 ./configure --with-compat --add-dynamic-module=../ModSecurity-nginx
 make modules
 cp objs/ngx_http_modsecurity_module.so /etc/nginx/modules
 cd ..
```
5 – Load the NGINX ModSecurity Connector Dynamic Module
Add the following load_module directive to the main (top‑level) context in /etc/nginx/nginx.conf. It instructs NGINX to load the ModSecurity dynamic module when it processes the configuration:

load_module modules/ngx_http_modsecurity_module.so;
6 – Configure, Enable, and Test ModSecurity
The final step is to enable and test ModSecurity.

Set up the appropriate ModSecurity configuration file. Here we’re using the recommended ModSecurity configuration provided by TrustWave Spiderlabs, the corporate sponsors of ModSecurity.
```sh
 mkdir /etc/nginx/modsec
 wget -P /etc/nginx/modsec/ https://raw.githubusercontent.com/SpiderLabs/ModSecurity/v3/master/modsecurity.conf-recommended
 mv /etc/nginx/modsec/modsecurity.conf-recommended /etc/nginx/modsec/modsecurity.conf
```
To guarantee that ModSecurity can find the unicode.mapping file (distributed in the top‑level ModSecurity directory of the GitHub repo), copy it to /etc/nginx/modsec.
```sh
 cp ModSecurity/unicode.mapping /etc/nginx/modsec
```
Change the SecRuleEngine directive in the configuration to change from the default “detection only” mode to actively dropping malicious traffic.
```sh
 sed -i 's/SecRuleEngine DetectionOnly/SecRuleEngine On/' /etc/nginx/modsec/modsecurity.conf
```
Configure one or more rules. For the purposes of this blog we’re creating a single simple rule that drops a request in which the URL argument called testparam includes the string test in its value. Put the following text in /etc/nginx/modsec/main.conf:

# From https://github.com/SpiderLabs/ModSecurity/blob/master/
# modsecurity.conf-recommended
#
# Edit to set SecRuleEngine On
Include "/etc/nginx/modsec/modsecurity.conf"

# Basic test rule
SecRule ARGS:testparam "@contains test" "id:1234,deny,status:403"
In a production environment, you presumably would use rules that actually protect against malicious traffic, such as the free OWASP core rule set.

Add the modsecurity and modsecurity_rules_file directives to the NGINX configuration to enable ModSecurity:

server {
    # ...
    modsecurity on;
    modsecurity_rules_file /etc/nginx/modsec/main.conf;
}
Issue the following curl command. The 403 status code confirms that the rule is working.
```sh
curl localhost?testparam=test

<html>
<head><title>403 Forbidden</title></head>
<body bgcolor="white">
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx/1.13.1</center>
</body>
</html>
```

[source](https://www.nginx.com/blog/compiling-and-installing-modsecurity-for-open-source-nginx/)




[mywiki]: <https://github.com/rasoulpoordelan/wiki/>
[compmod]: <https://www.nginx.com/blog/compiling-dynamic-modules-nginx-plus/>
