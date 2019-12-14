# window命令

## 查询端口号

netstat -ano    ---   列出所有端口的情况

tasklist|findstr "端口号"   ---   查看是哪个进程或者程序占用了该端口

## ssl证书转换

keytool -importkeystore -srckeystore C:\Users\wangrunfa\Desktop\3135863_www.cedar8.cn.pfx -destkeystore 520oo.jks -srcstoretype PKCS12 -deststoretype JKS