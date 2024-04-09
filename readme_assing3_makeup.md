#creating a static file server
1
-  install vim:
```sudo pacman -S vim```

- if asked "Proceed with installation? [Y/n]", type "Y" and press enter
2
-  install nginx:
```sudo pacman -S nginx```
3
- if asked "Proceed with installation? [Y/n]", type "Y" and press enter
4
- make the project root directory:
```sudo mkdir -p /web/html/nginx-2420```
5

6
- create the directory site-enabled:
```sudo mkdir /etc/nginx/sites-enabled```
7
- create a document directory:
```sudo mkdir /srv/2420-files```

- populate the document directory with files (can name files anything):
```sudo touch example.txt example2.txt example3.txt```

- make a sites-available directory in nginx (mandatory naming):
`sudo mkdir /etc/nginx/sites-available`
- make a sites-enabled directory in nginx (mandatory naming):
`sudo mkdir /etc/nginx/sites-enabled`
-  make a new configuration file in the sites-available directory (name of file must be based on instructions: nginx-2420):
`sudo vim /etc/nginx/sites-available/nginx-2420.conf`
-  append to the configuration file, using heredoc, the following code:
    - Explanation of code: 
    cat concatentates the heredoc to the file, tee writes the heredoc to the file, (the file being nginx-2420.conf).
    - listen to the default port of 80
    - `location / {
    try_files $uri $uri/ $uri.html =404;
}` -  if neither the original URI nor the URI with the appended trailing slash resolve into an existing file or directory, the request is redirected to the named location which passes it to a proxied server.
    - `autoindex on;` -  To configure NGINX to return an automatically generated directory listing instead of HTTP code 404 (source - https://docs.nginx.com/nginx/admin-guide/web-server/serving-static-content/)

```bash 
sudo cat <<'EOF' | sudo tee /etc/nginx/sites-available/nginx-2420.conf
server {
    listen 80;
    server_name localhost;
    root /srv/2420-files;
    location / {
        try_files $uri $uri/ $uri.html =404;
        autoindex on;
    }
}
EOF

 ```


9
- edit the nginx configuration file:
`sudo vim /etc/nginx/nginx.conf`

- comment out the server directive in the http block to avoid conflicts with the new configuration file:

```#server {
#    listen       80;
#    server_name  localhost;
...

#}```

- add the following line to the http block:
```include /etc/nginx/sites-enabled/*;```

- save the file: `esc :wq`
- create a symbolic link to the sites-enabled directory:
`sudo ln -s /etc/nginx/sites-available/nginx-2420.conf /etc/nginx/sites-enabled`
-  start the nginx service:
`sudo systemctl start nginx`
- enable the nginx service to run at boot:
`sudo systemctl enable nginx`
-  get the ip address of the droplet (the number following right
after inet, excluding the /20 under eth0):
`ip addr show`
-  open a web browser and type in the ip address of the droplet (ex:
http://64.23.250.17)