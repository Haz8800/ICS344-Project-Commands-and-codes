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
  
