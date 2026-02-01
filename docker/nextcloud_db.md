```
version: '3.8'

services:
  db:
    image: mariadb:10.11
    command: --transaction-isolation=READ-COMMITTED --log-bin=binlog --binlog-format=ROW
    restart: always
    volumes:
      - db_data:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=your_secure_root_pass
      - MYSQL_PASSWORD=your_nextcloud_db_pass
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud

  redis:
    image: redis:alpine
    restart: always

  app:
    image: nextcloud:production
    restart: always
    ports:
      - 8080:80
    depends_on:
      - db
      - redis
    volumes:
      - /home/paadmin/nextcloud/data:/var/www/html
      - /mnt/usb:/mnt/usb
    environment:
      - MYSQL_HOST=db
      - MYSQL_PASSWORD=your_nextcloud_db_pass
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - REDIS_HOST=redis
      # Optimization for Pi memory management
      - PHP_MEMORY_LIMIT=512M
      - PHP_UPLOAD_LIMIT=512M

volumes:
  db_data:
```