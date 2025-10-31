
# Nginx


### concepts
1. `Web server` = web server is a software (or sometimes hardware) that receives requests from web browsers (clients) over HTTP or HTTPS and responds by sending back web pages, files, or data. Web servers like: 1. Nginx 2. Apache HTTP Server 3. Microsoft IIS
2. `Load Balancer` = load balancer is a system (software or hardware) that distributes incoming network traffic across multiple servers to avoid overloading a single server
3. `Proxy server` = A proxy server is a middleman between clients and servers and forwards client requests to other servers. [Using Nginx proxy reduce attacking since we just have one server publicly]
4. `caching` = Caching means proxy stores a copy of responses (like HTML pages, images) and for repeated requests use cache instead of hitting the backend and database again.
5. `compression` = Compression reduces the size of HTTP responses before sending them to the client → faster page load, lower bandwidth usage.
6. `segmention` = sending respone in chunks (like video straming)
7. Nginx is a web server and a proxy server and `load balancing` and `caching` are functionality of proxy server.




### Load balancer methods

1. Least Connections --->	Sends new request to the least busy server.
2. Round Robin --->	Distributes client requests in a cyclical manner to each server in the group.
3. IP Hash --->	Routes users consistently based on their IP.
4. Weighted --->	Prioritizes powerful servers with higher weight.


| Software       | **Is it a Web Server?** | **Main Purpose**                                        |
| -------------- | ----------------------- | ------------------------------------------------------- |
| **Nginx**      | ✅ **Yes**               | Full **web server** + reverse proxy + load balancer     |
| **Express.js** | ⚠️ **Partially**        | **Web framework** to **build web servers** with Node.js |




### Nginx commands

```bash
nginx -v               # version
nginx -V                # version and config information
nginx                  # run nginx without interactive logs
cat  e
cat /etc/nginx/nginx.conf         # default config file
cat /var/log/nginx/access.log     # see logs
cat /var/log/nginx/error.log       # see error logs
```


### Nginx conf
1. `directive`: key-value pair in nginx
2. `contexts`: block of directives like `http`, `server`, `location`

```bash
worker_processes 1;                  # for cuncurency how many worker processes (think: independent workers) NGINX will spawn 
worker_processes auto;                # Automatically sets to number of CPU cores (recommend)
events {                             # handle generalt settings for connections in events 
    worker_connections 1024;            # Max number of simultaneous connections per worker_processes  (RAM)
}

http {          # handling HTTP and HTTPS traffic     # Define servers (virtual hosts), logging, caching, in http block
    include mime.types;                # Define mime types for each file extension for helping client how to render it
    upstream nodejs_cluster {          # Define a group of backend servers for load balancing
        least_conn;                 # deafilt algorithm is round robin but we can change it
        server 127.0.0.1:3001;        # nodejs_cluster is the name of the group
        server 127.0.0.1:3002;         
        server 127.0.0.1:3003;
    }
    server {                                # Define virtual hosts 
        listen {
            listen 8080;                      # # Listen to this port 
            server_name localhost;          #  Domain name for this site
            root /var/www/html;                   # Root directory of the website
            index index.html index.htm;           # Default file to serve
            location / {                        # define a proxy with location
                proxy_pass http://nodejs_cluster;          # pass the request to the backend servers  [http + group name of upstream]
                proxy_set_header Host $host;         # by default proxy server pass its own hostname to the upstream server 
                # $host is a variable in nginx and stores the hostname of the actual client request
                proxy_set_header X_Real_Ip $remote_addr;     # pass the client IP to the upstream server
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;     #  Proxy chain of IPs
            }
        }
    }
}
```


















