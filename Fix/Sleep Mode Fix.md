## 참고자료 출처
* https://blog.naver.com/asksays/220154371240
* https://community.wd.com/t/firmware-release-04-05-00-320-11-28-2017-discussion/217996/28
* http://m.ppomppu.co.kr/new/bbs_view.php?id=nas&no=22708&page=1&keyword=675226

## 아래 명령어 `전체`를 복사하고 실행
```
chmod 644 /etc/init.d/atop & \
chmod 644 /etc/init.d/itunes & \
chmod 644 /etc/init.d/mDNSResponder & \
chmod 644 /etc/init.d/netatalk & \
chmod 644 /etc/init.d/nfs-common & \
chmod 644 /etc/init.d/nfs-kernel-server & \
chmod 644 /etc/init.d/openvpn & \
chmod 644 /etc/init.d/restsdk-serverd & \
chmod 644 /etc/init.d/skeleton & \
chmod 644 /etc/init.d/twonky & \
chmod 644 /etc/init.d/upnp_nas & \
chmod 644 /etc/init.d/wdmcserverd & \
chmod 644 /etc/init.d/wdnotifierd & \
chmod 644 /etc/init.d/wdphotodbmergerd & \
chmod 644 /etc/init.d/winbind & \
chmod 755 /CacheVolume/user-start & \
nano /CacheVolume/user-start /etc/crontab /usr/local/sbin/monitorio.sh /etc/standby.conf ; \
reboot
```

## 아래 내용 `user-start` 파일에 넣고 저장
```
/etc/init.d/nfs-kernel-server stop      #Stop NFS
/etc/init.d/nfs-common stop             #Stop NFS
/etc/init.d/upnp_nas stop
/etc/init.d/mDNSResponder stop
/etc/init.d/wdnotifierd stop            #Stop crawling
/etc/init.d/wdphotodbmergerd stop       #Stop crawling
/etc/init.d/restsdk-serverd stop        #Stop crawling
#/etc/init.d/commgrd stop               #Stop WD official apps
/etc/init.d/wdmcserverd stop
#/etc/init.d/openvpn stop
/etc/init.d/netatalk stop
#/etc/init.d/wdAutoMount stop           #Stop USB auto mount
/etc/init.d/atop stop                   #Stop system status auto save
/etc/init.d/twonky stop
/etc/init.d/winbind stop
/etc/init.d/itunes stop
/bin/mount -o remount,noatime,nodiratime /dev/root /
/usr/sbin/ntpdate time.google.com
```

## 아래 내용 `crontab` 파일에 넣고 저장
```
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
#17 *   * * *   root    cd / && run-parts --report /etc/cron.hourly
#0 3    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
#10 3   * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
#20 3   1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )

15 10   * * *   root    /usr/sbin/ntpdate time.google.com
10 10   * * 1,4 root    /etc/cron.daily/ramlog
15 10   * * 1   root    /etc/cron.daily/logrotate

@reboot         root    /etc/cron.daily/apache2
@reboot         root    /etc/cron.daily/apt
@reboot         root    /etc/cron.daily/aptitude
@reboot         root    /etc/cron.daily/bsdmainutils
@reboot         root    /etc/cron.daily/cracklib-runtime
@reboot         root    /etc/cron.daily/dpkg
@reboot         root    /etc/cron.daily/logrotate
@reboot         root    /etc/cron.daily/man-db
@reboot         root    /etc/cron.daily/mdadm
@reboot         root    /usr/sbin/ntpdate time.google.com
@reboot         root    /etc/cron.daily/passwd
@reboot         root    /etc/cron.daily/ramlog
@reboot         root    /etc/cron.daily/sysstat
```

## 아래 내용 `monitorio.sh` 파일의 `echo "Enter standby"` 위에 추가하고 저장
```
                # added sync/sleep code to deal with 7/8 second wakeups
                sync
                sleep 3
                sync
                sleep 3
                sync
                sleep 3
```

## `standby.conf` 파일을 아래와 같이 수정
* `standby_time`에 원하는 시간(분) 입력

## reboot 후 아래 명령어 `전체`를 복사하고 실행
```
find / -name .nflc_data -exec rm -rf {} \; & \
find / -name .wdmc -exec rm -rf {} \; & \
find / -name .twonky -exec rm -rf {} \; & \
find / -name .wdphotos -exec rm -rf {} \;
```