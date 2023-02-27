---
title: "Deploying shiny applications throught shiny server"
format: html
editor: visual
---

There are quite a number of ways of deploying your Rshiny applications namely:\
* Shinyproxy   
* Shinyapps.io    
* RStudio connect     
* Shinyserver     

On this tutorial we'll focus on Shiny Server (a do-it-yourself) approach and below are the steps needed to configure the open source version of shiny server on ubuntu 20.04 server.

1.  Install the latest version of R

-   Add the relevant GPG Key\
    GPG (Gnu Privacy Guard) is an encryption technique that allows secure transmission of information between endpoints. It can be used to confirm that the source of a message is trustworthy.


    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9

-   Add the relevant repository\

    sudo add-apt-repository 'deb https://cloud.r-project.org/bin/linux/ubuntu focal-cran40/'

Notice: the last bit of the above command ends with *focal-cran40*, this is coined from the first name of ubuntu 20.04 (Focal Fossa) and the latest R version which is 4, as such, you might need to adjust accordingly if you're on a different release of ubuntu say ubuntu 18.04 (Bionic Beaver) or different version of R say version 3.

    sudo apt update   

We can now install R with the command below

    sudo apt install r-base  

Note: if prompted with y/n respond with y to continue with the installations.

To start an R session enter the command below

    sudo -i R 

The essence of starting an R session as root is so that the packages you install will be available to all users on the server.

2.  Install shiny package\
    We will need to install shiny R package. It's important to note that the r shiny package will have to be installed for all users and to achieve that you will need to either start the r session as root (explained above on the r installation steps) or use the command below in case you started r session as a non-root.


    sudo su - -c "R -e \"install.packages('shiny', repos='http://cran.rstudio.com/')\""

The su - means you're running the command as root and the -c flag passes the command to the user referenced by su -, for this case its sudo.

3.  Install shiny server\

-   Download shiny server\
    To get the latest shiny server run the command below. The latest release is

    wget https://download3.rstudio.org/ubuntu-18.04/x86_64/shiny-server-1.5.20.1002-amd64.deb

-   install gdebi\
    gdebi is used to install Shiny server and all of its dependencies.

    sudo apt-get install gdebi-core

-   Install shiny server

    sudo gdebi shiny-server-1.5.20.1002-amd64.deb

-   Allow traffic to port 3838\
    By default shiny makes use of port 3838 and seeing that mostly its port 80 and 443 which are open, we will need to allow traffic to shiny server through port 3838. To achieve that run the command below


    sudo ufw allow 3838  

To confirm all is well run the address below (of course with your server ip)

    http://your-server-ip:3838 

The above command should bring up the default shiny server homepage as below

Alternatively you could check if shiny server is running on the system by running the below command

    systemctl status shiny-server  

In case the system breaks, you might want to begin by having a look at the log accessible on the following path.

    log_dir /var/log/shiny-server

To note: \* Remember to install system dependencies for packages that need such i.e sf

-   Should you want to edit the config file, find it at: */etc/shiny-server/shiny-server.conf*

## Configuring shiny server to run behind nginx reverse proxy  

### Approach 1
To begin with, you might want to go through the [nginx documentation](put the url here) first as I'll assume you already have the basics. 

Open the nginx config file using the editor of your choice(nano/vim) as below   

```
sudo nano /etc/nginx/nginx.conf   

```
Have your config file as below    

```

user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

http {
  map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
  }

  server {
    listen 80;
    
    
    location / {
      proxy_pass http://localhost:3838;
      proxy_redirect / $scheme://$http_host/;
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_read_timeout 20d;
      proxy_buffering off;
    }
  }
}

```

Use the commands below to check if the syntax is okay and reload the configuration file. 

```
sudo nginx -t #this checks if the syntax is correct

sudo nginx -s reload #this reloads the configuration file to update the changes  

```
### Approach 2  

Alternatively, if you want to use nginx server blocks follow the below steps. 

* Initialize server block for the shiny server
```
sudo nano /etc/nginx/sites-available/shiny

```

* Put the following text into the shiny file you've opened with nano editor
```
server {

        server_name your_shiny_server_url/Ip.address;

        location / {
                proxy_pass http://localhost:3838;
                proxy_redirect / $scheme://$http_host/;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection $connection_upgrade;
                proxy_read_timeout 20d;
                proxy_buffering off;
        }
```

Save and exit the editor.  
You might want to confirm if you have successfully created the shiny server block in the sites-available  
Navigate into the sites-available and check if there's a file named shiny

```
ls -l /etc/nginx/sites-available

```
* Symlinking the shiny server block to sites-enabled
This is done with the command below  

```
sudo ln -s /etc/nginx/sites-available/shiny /etc/nginx/sites-enabled/shiny

```

once you're done reload nginx 

```
sudo nginx -s reload 
```


References
1. https://www.linode.com/docs/guides/how-to-deploy-rshiny-server-on-ubuntu-and-debian/
