```
version: '3'

volumes:
  mariadb:

services:
  mariadb:
    image: mariadb:latest
    restart: always
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW --innodb-file-per-table=1 --innodb-read-only-compressed=0
    volumes:
      - mariadb:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=<Password>
      - MYSQL_PASSWORD=<Password>
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud

  app:
    image: nextcloud:latest
    restart: always
    ports:
      - 8080:80
    depends_on:
      - mariadb
    volumes:
      - /home/paadmin/nextcloud/var/www/html:/var/www/html
      - /mnt/usb:/mnt/usb
    environment:
      - MYSQL_PASSWORD=<Password>
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=mariadb
```