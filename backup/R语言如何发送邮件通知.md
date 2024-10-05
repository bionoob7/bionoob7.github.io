## 问题描述
当你运行一个需要很久才能完成的任务，不确定什么时候才能运行完，但是肯定不想一直打开它，查看有没有运行完成，这个时候就需要`R语言程序运行结束的提示邮件`。

## 如何通过邮件提醒

以163邮箱为例，需要去[邮箱设置页面，开启SMTP验证](https://blog.csdn.net/liuyuinsdu/article/details/113878840)，并且得到授权码，填写到 `password = "xxxxxxx"` 这里，其他内容按照我的模板填写即可

```R
# 安装并加载R包
if(!require(devtools))install.packages('devtools')
if(!require(sendmailR))install_github("olafmersmann/sendmailR")
 library(sendmailR)
sendmail(from = "aaaaaa@163.com", 
         to=c("bbbbbb@163.com"), 
         subject="anaysies have done!", 
         msg=mime_part("all the results are here!"), 
         engine = "curl", 
         engineopts = list(username = "aaaaaa@163.com", password = "xxxxxxx"), 
         control=list(smtpServer="smtp.163.com:465",
                      verbose = TRUE)
)
```