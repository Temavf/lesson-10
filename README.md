
## Выполнение

Проверяем работу сценария

```bash
./notifier.sh access.log
LOG REPORT
-----------------------------------------
Start date-time:  1970-01-01
Finish date-time: 2023-06-24T20:26:56+03:00

Top 10 IPs
-----------------------------------------
    128 195.208.184.200
    100 89.208.230.2
     28 176.57.208.169
     26 195.208.184.200
     26 176.57.208.169
     22 176.57.208.169
     21 83.222.11.43
     20 89.191.229.231
     20 176.57.208.169
     20 176.57.208.169

Top 8 Requests
-----------------------------------------
    516 POST /api/bx/deal/update
    317 GET /metrics
     26 POST /api/deal/update
     23 POST /auth/token
      8 GET /wp-commentin.php
      8 GET /wp-cc.php
      4 GET /
      2 POST /users/sign_in

Top Requests With Server Errors
-----------------------------------------
    196 POST /api/bx/deal/update HTTP/1.1" 503

HTTP Codes
-----------------------------------------
    335 404
    245 200
    196 503
    168 301
     20 401
      2 204


./notifier.sh access.log "2023-06-22T00:39:10+0300"
LOG REPORT
-----------------------------------------
Start date-time:  2023-06-22T00:39:10+0300
Finish date-time: 2023-06-24T20:26:22+03:00

Top 10 IPs
-----------------------------------------
      4 176.57.208.169
      2 217.15.62.253
      2 217.15.62.253
      1 176.57.208.169

Top 8 Requests
-----------------------------------------
      5 GET /metrics
      1 GET /api/v1/purchases?page=1&per=20&purchase_created_at_before=2023-05-15&sorted_by=purchase_updated_at_asc
      1 GET /api/v1/purchases?page=1&per=20&purchase_created_at_after=2023-05-15&sorted_by=purchase_updated_at_asc

Top Requests With Server Errors
-----------------------------------------

HTTP Codes
-----------------------------------------
      4 200
      3 301
```

Настройка отправки почтовых сообщений

```bash
sudo apt install mailutils ssmtp
```

Конфигурация исходящей почты
```bash
# /etc/ssmtp/ssmtp.conf
#
# Config file for sSMTP sendmail
#
# The person who gets all mail for userids < 1000
# Make this empty to disable rewriting.
root=zoid@onrails.ru

# The place where the mail goes. The actual machine name is required no 
# MX records are consulted. Commonly mailhosts are named mail.domain.com
mailhub=smtp.timeweb.ru

# Where will the mail seem to come from?
rewriteDomain=onrails.ru

# The full hostname
hostname=npc

# Are users allowed to set their own From: address?
# YES - Allow the user to specify their own From: address
# NO - Use the system generated From: address
#FromLineOverride=YES

AuthUser=zoid@onrails.ru
AuthPass=secret
```


Запускаем сценарий с отправкой результата на почту:

```bash
./notifier.sh access.log "2023-06-22T00:39:10+0300" | mail -s "LOG REPORT" "email@example.com"
```

> Лог почтовой программы, если что-то не работает */var/log/mail.log*.

Запускаем через cron

```bash
crontab -e
```
```cron
0 */1 * * * /bin/bash -l -c '/path/to/notifier.sh /path/to/access.log | mail -s "LOG REPORT" "email@example.com"'
```
