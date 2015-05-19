Load Balancing + Rails + SSL + NGINX
====================================

Nginx Passenger for Load Balancing 2 rails servers using a self signed SSL certificate.

Setup
-----

**Start Rails Server**

Start 2 instances of the rails application using the following commands - 

```
$ cd sampleapp          // Navigate to the rails application folder
$ rails server          // Start server on port 3000
$ rails s -p 3001 -P tmp/pids/server2.pid // Start server on port 3001 

```
**Create a self signed certificate**

Create a self signed certificate using a common-name such as "ruby-trail.com". 
```
$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt
```
This will create 2 files the nginx.key and nginx.crt file.

**Modifying the Nginx Configuration**

Make sure that you have setup Nginx with passenger. Installation details are provided [here](http://ershadk.com/blog/2012/04/05/set-up-rails-3-2-2-with-passenger-rvm-and-ngnix/)

Copy the [nginx.conf](https://github.com/haridnr/rails-nginx-ssl-setup/blob/master/nginx-setup/nginx.conf) file and make the following changes

1. Replace {username} and {path to ruby project} variables in the nginx.conf file as per your system
2. The load balancing setup is done using the upstream block
```
upstream web-cluster {
        ip_hash;
        server localhost:3001;   # backend rails server 1
        server localhost:3000;   # backend rails server 2
    }
```
There are other types of load balancing techniques other than ``` ip_hash ```.
Refer to this [article](http://nginx.org/en/docs/http/load_balancing.html) for more information.

3. Restart your nginx server
```
$ sudo service nginx restart
```

**Access the website to verify**

Access the site using https://ruby-trail.com/ and verify the load balancing by shutting down one of the rails servers.

References
----------

1. Excellent explaination by Justin about Nginx load balancing setup [here](http://spin.atomicobject.com/2013/07/08/nginx-load-balancing-reverse-proxy-updated/)
2. Nginx Load Balancing [example](http://cepa.io/devlog/secure-https-load-balancing-with-nginx)






