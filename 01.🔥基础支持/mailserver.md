## zimbra

使用这个邮件服务器。具体功能比较强，符合需要。使用docker-zimbra进行安装，但没有成功，mlgbd网速太慢了。

~~开源[邮件服务器](https://github.com/tomav/docker-mailserver)，无图形化界面，docker方式安装。~~
~~下载慢，使用镜像加速，不要更改daemon的内容和顺序。~~

```json
{
  "registry-mirrors" : [
    "http://f1361db2.m.daocloud.io",
    "https://fo8v7zw4.mirror.aliyuncs.com",
    "https://registry.docker-cn.com",
    "https://docker.mirrors.ustc.edu.cn"
  ],
  "experimental" : true,
  "insecure-registries" : [
    "f1361db2.m.daocloud.io",
    "registry.docker-cn.com",
    "docker.mirrors.ustc.edu.cn"
  ],
  "debug" : true
}
```

~~安装可行，但无法发送邮件，似乎没有做dns配置和域名配置的关系。被退回的邮件内容。~~

```
This is the mail system at host mail.jicu.vip.

I'm sorry to have to inform you that your message could not
be delivered to one or more recipients. It's attached below.

For further assistance, please send mail to postmaster.

If you do so, please include this problem report. You can
delete your own text from the attached returned message.

                  The mail system

<geniusmickymouse@qq.com>: host mx3.qq.com[183.232.93.177] said: 550 Ip
   frequency limited [Oaj1ioVTF4gnIZdRer4tvauWt2IFP/aBShai+Jv6cUI2Aro7xiHMpCM=
   Blocked IP 183.197.157.132].
   http://service.mail.qq.com/cgi-bin/help?subtype=1&&id=20022&&no=1000725 (in
   reply to end of DATA command)
Reporting-MTA: dns; mail.jicu.vip
X-Postfix-Queue-ID: 03FE23A1799
X-Postfix-Sender: rfc822; mk@jicu.vip
Arrival-Date: Sun, 29 Sep 2019 06:35:44 +0000 (UTC)

Final-Recipient: rfc822; geniusmickymouse@qq.com
Original-Recipient: rfc822;geniusmickymouse@qq.com
Action: failed
Status: 5.0.0
Remote-MTA: dns; mx3.qq.com
Diagnostic-Code: smtp; 550 Ip frequency limited
   [Oaj1ioVTF4gnIZdRer4tvauWt2IFP/aBShai+Jv6cUI2Aro7xiHMpCM= Blocked IP
   183.197.157.132].
   http://service.mail.qq.com/cgi-bin/help?subtype=1&&id=20022&&no=1000725

发件人: 许忠亮 <mk@jicu.vip>
主题: mk
日期: 2019年9月29日 GMT+8 14:35:44
收件人: 许忠亮 <geniusmickymouse@qq.com>




test
```

~~docker logs~~

```
Sep 29 06:34:45 mail dovecot: imap-login: Login: user=<mk@jicu.vip>, method=PLAIN, rip=172.18.0.1, lip=172.18.0.2, mpid=2534, TLS, session=<b7GuUKuTZtusEgAB>
Sep 29 06:34:45 mail dovecot: imap(mk@jicu.vip)<2534><b7GuUKuTZtusEgAB>: Logged out in=32 out=515 deleted=0 expunged=0 trashed=0 hdr_count=0 hdr_bytes=0 body_count=0 body_bytes=0
Sep 29 06:35:19 mail dovecot: imap-login: Login: user=<mk@jicu.vip>, method=PLAIN, rip=172.18.0.1, lip=172.18.0.2, mpid=2646, TLS, session=<hH6oUquTaNusEgAB>
Sep 29 06:35:44 mail postfix/submission/smtpd[2256]: connect from unknown[172.18.0.1]
Sep 29 06:35:44 mail postfix/submission/smtpd[2256]: Anonymous TLS connection established from unknown[172.18.0.1]: TLSv1.2 with cipher ECDHE-RSA-AES256-GCM-SHA384 (256/256 bits)
Sep 29 06:35:44 mail postfix/submission/smtpd[2256]: AE4A43A178F: client=unknown[172.18.0.1], sasl_method=PLAIN, sasl_username=mk@jicu.vip
Sep 29 06:35:44 mail postfix/sender-cleanup/cleanup[2736]: AE4A43A178F: replace: header Mime-Version: 1.0 (Mac OS X Mail 12.4 \(3445.104.11\)) from unknown[172.18.0.1]; from=<mk@jicu.vip> to=<geniusmickymouse@qq.com> proto=ESMTP helo=<api.jicu.vip>: Mime-Version: 1.0
Sep 29 06:35:44 mail postfix/sender-cleanup/cleanup[2736]: AE4A43A178F: message-id=<E9DFCDA2-CEE7-4D5A-83DB-6FD301308F22@jicu.vip>
Sep 29 06:35:44 mail opendkim[143]: AE4A43A178F: DKIM-Signature field added (s=mail, d=jicu.vip)
Sep 29 06:35:44 mail postfix/qmgr[909]: AE4A43A178F: from=<mk@jicu.vip>, size=357, nrcpt=1 (queue active)
Sep 29 06:35:44 mail postfix/smtpd[2739]: connect from localhost[127.0.0.1]
Sep 29 06:35:45 mail postfix/smtpd[2739]: 03FE23A1799: client=localhost[127.0.0.1]
Sep 29 06:35:45 mail postfix/cleanup[2740]: 03FE23A1799: message-id=<E9DFCDA2-CEE7-4D5A-83DB-6FD301308F22@jicu.vip>
Sep 29 06:35:45 mail postfix/qmgr[909]: 03FE23A1799: from=<mk@jicu.vip>, size=1062, nrcpt=1 (queue active)
Sep 29 06:35:45 mail amavis[595]: (00595-01) Passed CLEAN {RelayedOutbound}, LOCAL [172.18.0.1]:59942 <mk@jicu.vip> -> <geniusmickymouse@qq.com>, Queue-ID: AE4A43A178F, Message-ID: <E9DFCDA2-CEE7-4D5A-83DB-6FD301308F22@jicu.vip>, mail_id: sSBAL6dtsKcR, Hits: -, size: 848, queued_as: 03FE23A1799, 202 ms
Sep 29 06:35:45 mail postfix/smtp[2737]: AE4A43A178F: to=<geniusmickymouse@qq.com>, relay=127.0.0.1[127.0.0.1]:10024, delay=0.36, delays=0.12/0.01/0.01/0.21, dsn=2.0.0, status=sent (250 2.0.0 from MTA(smtp:[127.0.0.1]:10025): 250 2.0.0 Ok: queued as 03FE23A1799)
Sep 29 06:35:45 mail postfix/qmgr[909]: AE4A43A178F: removed
Sep 29 06:35:45 mail postfix/smtp[2741]: Trusted TLS connection established to mx3.qq.com[183.232.93.177]:25: TLSv1.2 with cipher ECDHE-RSA-AES128-GCM-SHA256 (128/128 bits)
Sep 29 06:35:46 mail postfix/smtp[2741]: 03FE23A1799: to=<geniusmickymouse@qq.com>, relay=mx3.qq.com[183.232.93.177]:25, delay=2, delays=0.04/0.03/0.79/1.1, dsn=5.0.0, status=bounced (host mx3.qq.com[183.232.93.177] said: 550 Ip frequency limited [Oaj1ioVTF4gnIZdRer4tvauWt2IFP/aBShai+Jv6cUI2Aro7xiHMpCM= Blocked IP 183.197.157.132]. http://service.mail.qq.com/cgi-bin/help?subtype=1&&id=20022&&no=1000725 (in reply to end of DATA command))
Sep 29 06:35:47 mail postfix/cleanup[2740]: 10A783A179C: message-id=<20190929063547.10A783A179C@mail.jicu.vip>
Sep 29 06:35:47 mail postfix/bounce[2748]: 03FE23A1799: sender non-delivery notification: 10A783A179C
Sep 29 06:35:47 mail postfix/qmgr[909]: 10A783A179C: from=<>, size=3286, nrcpt=1 (queue active)
Sep 29 06:35:47 mail postfix/qmgr[909]: 03FE23A1799: removed
Sep 29 06:35:47 mail dovecot: lmtp(2750): Connect from local
Sep 29 06:35:47 mail dovecot: lmtp(mk@jicu.vip)<2750><lMI4BsNQkF2+CgAA/IakEA>: msgid=<20190929063547.10A783A179C@mail.jicu.vip>: saved mail to INBOX
Sep 29 06:35:47 mail postfix/lmtp[2749]: 10A783A179C: to=<mk@jicu.vip>, relay=mail.jicu.vip[/var/run/dovecot/lmtp], delay=0.05, delays=0.01/0.01/0.02/0.01, dsn=2.0.0, status=sent (250 2.0.0 <mk@jicu.vip> lMI4BsNQkF2+CgAA/IakEA Saved)
Sep 29 06:35:47 mail postfix/qmgr[909]: 10A783A179C: removed
Sep 29 06:35:47 mail dovecot: lmtp(2750): Disconnect from local: Client has quit the connection (state=READY)
Sep 29 06:36:44 mail postfix/submission/smtpd[2256]: disconnect from unknown[172.18.0.1] ehlo=2 starttls=1 auth=1 mail=1 rcpt=1 data=1 quit=1 commands=8
Sep 29 06:40:04 mail postfix/anvil[2259]: statistics: max connection rate 4/60s for (submission:172.18.0.1) at Sep 29 06:34:19
Sep 29 06:40:04 mail postfix/anvil[2259]: statistics: max connection count 1 for (submission:172.18.0.1) at Sep 29 06:33:30
Sep 29 06:40:04 mail postfix/anvil[2259]: statistics: max cache size 1 at Sep 29 06:33:30
Sep 29 06:40:44 mail postfix/smtpd[2739]: timeout after END-OF-MESSAGE from localhost[127.0.0.1]
Sep 29 06:40:44 mail postfix/smtpd[2739]: disconnect from localhost[127.0.0.1] ehlo=1 mail=1 rcpt=1 data=1 commands=4
```

## ~~参考文献~~

1. ~~[利用Docker自建多功能加密邮件服务器~~](https://www.itmanbu.com/docker-mail-server.html)