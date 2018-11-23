# Certbot-https

安装Certbot
yum install epel-release yum-utils python2-certbot-nginx -y

申请证书
# yum安装的ng执行该命令
certbot --nginx certonly
# 手动安装的ng执行该命令
certbot --nginx certonly --nginx-server-root /usr/local/nginx/conf --nginx-ctl /usr/local/nginx/sbin/nginx

选择需要配置https的域名如下,输入:1
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator nginx, Installer nginx
Starting new HTTPS connection (1): acme-v02.api.letsencrypt.org
Which names would you like to activate HTTPS for?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: x.com.cn
2: xx.com.cn
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate numbers separated by commas and/or spaces, or leave input
blank to select all options shown (Enter 'c' to cancel): 1

执行完成显示生成的证书信息
IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/x.com.cn/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/x.com.cn/privkey.pem
   Your cert will expire on 2019-02-17. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
   
 配置Nginx
 server
{
    listen 443;
    server_name xx.com.cn;
    ssl on;
    ssl_certificate /etc/letsencrypt/live/xx.com.cn/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/xx.com.cn/privkey.pem;
    #charset koi8-r;
    access_log /var/log/nginx/access.log main;
    location / {
        #proxy_redirect off;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://192.168.1.1:8080/;
    }
}

自动续期
certbot有访问频次控制，为避免测试超限，先用以下命令验证
certbot renew --dry-run
验证通过后设置定时任务
crontal -e
0 1 * * * certbot renew --quiet --renew-hook "nginx -s reload"
