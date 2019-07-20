- Bundle: docker-compose run jekyll jekyll build
- Copy /tiktok-blog to server /etc/nginx/sites-available
- Copy /tiktok-blog/default to /etc/nginx/sites-available
- sudo systemctl restart nginx


docker run --rm -it -v "/root/letsencrypt/log:/var/log/letsencrypt" -v "/etc/nginx/sites-available/tiktok-blog/dist:/var/www/" -v "/etc/letsencrypt:/etc/letsencrypt" -v "/root/letsencrypt/lib:/var/lib/letsencrypt" lojzik/letsencrypt certonly --webroot --webroot-path /var/www --email lephuocmy668@gmail.com -d romeo.vn


docker run --rm -it -v "/root/letsencrypt/log:/var/log/letsencrypt" -v "/etc/nginx/sites-available/tiktok-blog/dist:/var/www/" -v "/etc/letsencrypt:/etc/letsencrypt" -v "/root/letsencrypt/lib:/var/lib/letsencrypt" lojzik/letsencrypt certonly --webroot --webroot-path /var/www --email lephuocmy668@gmail.com -d moliza.vn
