```shell


# 1. debian 10 更新源
deb http://mirrors.ustc.edu.cn/debian buster main contrib non-free
deb http://mirrors.ustc.edu.cn/debian buster-updates main contrib non-free
deb http://mirrors.ustc.edu.cn/debian buster-backports main contrib non-free
deb http://mirrors.ustc.edu.cn/debian-security/ buster/updates main contrib non-free

# deb-src http://mirrors.ustc.edu.cn/debian buster main contrib non-free
# deb-src http://mirrors.ustc.edu.cn/debian buster-updates main contrib non-free
# deb-src http://mirrors.ustc.edu.cn/debian buster-backports main contrib non-free
# deb-src http://mirrors.ustc.edu.cn/debian-security/ buster/updates main contrib non-free
# 官方源
# deb http://deb.debian.org/debian buster main contrib non-free
# deb http://deb.debian.org/debian buster-updates main contrib non-free
# deb http://deb.debian.org/debian-security/ buster/updates main contrib non-free

# deb-src http://deb.debian.org/debian buster main contrib non-free
# deb-src http://deb.debian.org/debian buster-updates main contrib non-free
# deb-src http://deb.debian.org/debian-security/ buster/updates main contrib non-free

# 网易源

# deb http://mirrors.163.com/debian/ buster main non-free contrib
# deb http://mirrors.163.com/debian/ buster-updates main non-free contrib
# deb http://mirrors.163.com/debian/ buster-backports main non-free contrib
# deb http://mirrors.163.com/debian-security/ buster/updates main non-free contrib

# deb-src http://mirrors.163.com/debian/ buster main non-free contrib
# deb-src http://mirrors.163.com/debian/ buster-updates main non-free contrib
# deb-src http://mirrors.163.com/debian/ buster-backports main non-free contrib
# deb-src http://mirrors.163.com/debian-security/ buster/updates main non-free contrib

# 阿里云

# deb http://mirrors.aliyun.com/debian/ buster main non-free contrib
# deb http://mirrors.aliyun.com/debian/ buster-updates main non-free contrib
# deb http://mirrors.aliyun.com/debian/ buster-backports main non-free contrib
# deb http://mirrors.aliyun.com/debian-security buster/updates main

# deb-src http://mirrors.aliyun.com/debian/ buster main non-free contrib
# deb-src http://mirrors.aliyun.com/debian/ buster-updates main non-free contrib
# deb-src http://mirrors.aliyun.com/debian/ buster-backports main non-free contrib
# deb-src http://mirrors.aliyun.com/debian-security buster/updates main
```

```shell
2. 安装网络工具 

apt install net-tools iproute2 telnet -y
```

```shell
base-data-api.war
email-api.war
mobile-data-api.war
open-short-receive.war
open-short-task.war
outbound-access-consume.war
outbound-application-monitoring.war
outbound-call-status-consume.war
outbound-call-status-load.war
outbound-call-status-push.war
outbound-call.war
outbound-cmpp-status-consume.war
outbound-data-handler.war
outbound-data-import.war
outbound-data-web.war
outbound-http-status-consume.war ✅
outbound-http-status-push.war   ✅
outbound-manager.war
outbound-send-part.war
outbound-sms-cmpp.war
outbound-sms.war
outbound-statistics.war
outbound-stats-web.war
outbound-timing-task.war


black-monitor.jar
black-starter.jar
outbound-distributed-executor.jar
outbound-distributed-generator-operator.jar
outbound-stats.jar
wh-push.jar
wh-starter.jar




base-data-api
email-api
mobile-data-api
open-short-receive
open-short-task
outbound-access-consume
outbound-application-monitoring
outbound-call-status-consume
outbound-call-status-load
outbound-call-status-push
outbound-call
outbound-cmpp-status-consume
outbound-data-handler
outbound-data-import
outbound-data-web
outbound-http-status-consume
outbound-http-status-push
outbound-manager
outbound-send-part
outbound-sms-cmpp
outbound-sms
outbound-statistics
outbound-stats-web
outbound-timing-task


black-monitor
black-starter
outbound-distributed-executor
outbound-distributed-generator-operator
outbound-stats
wh-push
wh-starter




22 。69

45:    container_name: base-data-api
46-    restart: always
--
57:    ports:
58-      - "8480:8080"

sed -i '69s/port="8080"/port="8480"/g; 22s/port="8005"/port="8580"/g' /apps/outbound/base-data-api/conf/server.xml
--
72:    container_name: email-api
73-    restart: always
--
84:    ports:
85-      - "8481:8080"
sed -i '69s/port="8080"/port="8481"/g; 22s/port="8005"/port="8581"/g' /apps/outbound/email-api/conf/server.xml
--
94:    container_name: mobile-data-api
95-    restart: always
--
106:    ports:
107-      - "8482:8080"
sed -i '69s/port="8080"/port="8482"/g; 22s/port="8005"/port="8582"/g' /apps/outbound/mobile-data-api/conf/server.xml

--
116:    container_name: open-short-receive
117-    restart: always
--
128:    ports:
129-      - "8483:8080"
sed -i '69s/port="8080"/port="8483"/g; 22s/port="8005"/port="8583"/g' /apps/outbound/open-short-receive/conf/server.xml
--
138:    container_name: open-short-task
139-    restart: always
--
150:    ports:
151-      - "8484:8080"
sed -i '69s/port="8080"/port="8484"/g; 22s/port="8005"/port="8584"/g' /apps/outbound/open-short-task/conf/server.xml
--
160:    container_name: outbound-access-consume
161-    restart: always
--
172:    ports:
173-      - "8485:8080"
sed -i '69s/port="8080"/port="8485"/g; 22s/port="8005"/port="8585"/g' /apps/outbound/outbound-access-consume/conf/server.xml
--
182:    container_name: outbound-application-monitoring
183-    restart: always
--
194:    ports:
195-      - "8486:8080"
sed -i '69s/port="8080"/port="8486"/g; 22s/port="8005"/port="8586"/g' /apps/outbound/outbound-application-monitoring/conf/server.xml

--
204:    container_name: outbound-call-status-consume
205-    restart: always
--
216:    ports:
217-      - "8487:8080"
sed -i '69s/port="8080"/port="8487"/g; 22s/port="8005"/port="8587"/g' /apps/outbound/outbound-call-status-consume/conf/server.xml
--
226:    container_name: outbound-call-status-load
227-    restart: always
--
238:    ports:
239-      - "8488:8080"
sed -i '69s/port="8080"/port="8488"/g; 22s/port="8005"/port="8588"/g' /apps/outbound/outbound-call-status-load/conf/server.xml

--
248:    container_name: outbound-call-status-push
249-    restart: always
--
260:    ports:
261-      - "8489:8080"
sed -i '69s/port="8080"/port="8489"/g; 22s/port="8005"/port="8588"/g' /apps/outbound/outbound-call-status-load/conf/server.xml
--
270:    container_name: outbound-call
271-    restart: always
--
282:    ports:
283-      - "8490:8080"
sed -i '69s/port="8080"/port="8490"/g; 22s/port="8005"/port="8590"/g' /apps/outbound/outbound-call/conf/server.xml
--
292:    container_name: outbound-cmpp-status-consume
293-    restart: always
--
304:    ports:
305-      - "8491:8080"
sed -i '69s/port="8080"/port="8491"/g; 22s/port="8005"/port="8591"/g' /apps/outbound/outbound-cmpp-status-consume/conf/server.xml
--
314:    container_name: outbound-data-handler
315-    restart: always
--
326:    ports:
327-      - "8492:8080"
sed -i '69s/port="8080"/port="8492"/g; 22s/port="8005"/port="8592"/g' /apps/outbound/outbound-data-handler/conf/server.xml
--
336:    container_name: outbound-data-import
337-    restart: always
--
348:    ports:
349-      - "8493:8080"
sed -i '69s/port="8080"/port="8493"/g; 22s/port="8005"/port="8593"/g' /apps/outbound/outbound-data-import/conf/server.xml
--
358:    container_name: outbound-data-web
359-    restart: always
--
370:    ports:
371-      - "8494:8080"
sed -i '69s/port="8080"/port="8494"/g; 22s/port="8005"/port="8594"/g' /apps/outbound/outbound-data-web/conf/server.xml
--
380:    container_name: outbound-http-status-consume
381-    restart: always
--
392:    ports:
393-      - "8495:8080"
sed -i '69s/port="8080"/port="8495"/g; 22s/port="8005"/port="8595"/g' /apps/outbound/outbound-http-status-consume/conf/server.xml
--
402:    container_name: outbound-http-status-push
403-    restart: always
--
414:    ports:
415-      - "8496:8080"
sed -i '69s/port="8080"/port="8496"/g; 22s/port="8005"/port="8596"/g' /apps/outbound/outbound-http-status-push/conf/server.xml
--
424:    container_name: outbound-manager
425-    restart: always
--
436:    ports:
437-      - "8497:8080"
sed -i '69s/port="8080"/port="8497"/g; 22s/port="8005"/port="8597"/g' /apps/outbound/outbound-manager/conf/server.xml
--
446:    container_name: outbound-send-part
447-    restart: always
--
458:    ports:
459-      - "8498:8080"
sed -i '69s/port="8080"/port="8498"/g; 22s/port="8005"/port="8598"/g' /apps/outbound/outbound-send-part/conf/server.xml
--
468:    container_name: outbound-sms-cmpp
469-    restart: always
--
480:    ports:
481-      - "8499:8080"
sed -i '69s/port="8080"/port="8499"/g; 22s/port="8005"/port="8599"/g' /apps/outbound/outbound-sms-cmpp/conf/server.xml
--
490:    container_name: outbound-sms
491-    restart: always
--
502:    ports:
503-      - "8500:8080"
sed -i '69s/port="8080"/port="8500"/g; 22s/port="8005"/port="8600"/g' /apps/outbound/outbound-sms/conf/server.xml
--
512:    container_name: outbound-statistics
513-    restart: always
--
524:    ports:
525-      - "8501:8080"
sed -i '69s/port="8080"/port="8501"/g; 22s/port="8005"/port="8601"/g' /apps/outbound/outbound-statistics/conf/server.xml
--
534:    container_name: outbound-stats-web
535-    restart: always
--
546:    ports:
547-      - "8502:8080"
sed -i '69s/port="8080"/port="8502"/g; 22s/port="8005"/port="8602"/g' /apps/outbound/outbound-stats-web/conf/server.xml
--
556:    container_name: outbound-timing-task
557-    restart: always
--
568:    ports:
569-      - "8503:8080"
sed -i '69s/port="8080"/port="8503"/g; 22s/port="8005"/port="8603"/g' /apps/outbound/outbound-timing-task/conf/server.xml
--
579:    container_name: black-monitor
580-    restart: always
--
592:    ports:
593-      - "8504:8504"
--
602:    container_name: black-starter
603-    restart: always
--
615:    ports:
616-      - "8505:8504"
--
625:    container_name: outbound-distributed-executor
626-    restart: always
--
638:    ports:
639-      - "8506:8504"
--
648:    container_name: outbound-distributed-generator-operator
649-    restart: always
--
661:    ports:
662-      - "8507:8504"
--
671:    container_name: outbound-stats
672-    restart: always
--
684:    ports:
685-      - "8508:8504"
--
694:    container_name: wh-push
695-    restart: always
--
707:    ports:
708-      - "8509:8504"
--
717:    container_name: wh-starter
718-    restart: always
--
730:    ports:
731-      - "8510:8504"
```

