---
layout: post
title: Shell每日发邮件
category: 技术
tags: Shell
keywords: Shell
description: Shell每日发邮件
---

很是好奇运维工作，刚好有幸接触到，每日发邮件脚本，多用来发送服务器日志，

```
LOGFILE="$fank/"`date +"%Y%m%d"`"data"#每日文件

from="abc@123.com"#发件人

recipients="fank@123.com"#收件人


subject="[REPORT]every day check" #主题
content="$(cat ${LOGFILE})"  #内容

/usr/sbin/sendmail "${recipients}" <<EOF
subject:${subject}
from:${from}
${content}
EOF
```
 

*该脚本需要部署在服务器定时任务crontab上
