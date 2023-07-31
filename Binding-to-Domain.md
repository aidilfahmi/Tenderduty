## ${\color{lightgreen}Binding \space to \space Domain}$
- Install Package
```
sudo apt update && sudo apt upgrade -y
sudo apt install nginx certbot python3-certbot-nginx -y
```
- Create your domain/sub domain
  > example mine : `status.dnsarz.xyz`
- Create nginx configuration file <br>
  > Replace `status.dnsarz.xyz` with your domain/sub-domain.
```
sudo nano /etc/nginx/sites-enabled/status.dnsarz.xyz
```

- Insert this code and change `server_name` with your own.
```javascript
server {
    server_name status.dnsarz.xyz;
    listen 80;
    location / {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host;
        proxy_pass http://127.0.0.1:8888;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```
- Install Certificate SSL
```
sudo certbot --nginx --register-unsafely-without-email
```
![image](https://github.com/aidilfahmi/Tenderduty/assets/16186519/3dcfb4e9-d6eb-4024-af4e-0b23f611130e)

> Select the domain that will be installed SSL
- Restart Nginx
```
sudo systemctl restart nginx
```
- Test your domain on Browser
  
  
