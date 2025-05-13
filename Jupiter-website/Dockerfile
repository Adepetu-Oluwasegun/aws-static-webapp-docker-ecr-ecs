FROM amazonlinux:latest

# install dependencies
RUN yum update -y && \
    yum install -y httpd && \
    yum search wget && \
    yum install wget -y && \
    yum install unzip -y

# change directory
RUN command cd /var/www/html

# download webfiles
RUN wget http://github.com/azeezsalu/techmax/archive/refs/heads/main.zip

# unzip folder
RUN unzip main.zip

# copy files from main into html directory
RUN cp -r techmax-main/* /var/www/html

# remove unwanted folders
RUN rm -rf techmax-main main.zip

# open port 80 on the container
EXPOSE 80

# set default app that will start when the container starts
ENTRYPOINT ["/usr/sbin/httpd", "-D", "FOREGROUND"]