[TOC]

### 媒体云升级

##### 一、数据备份

###### 1. 数据库备份

```powershell
shell> cp -r /data/db/mysql /data/db/mysql_bak-$(date +%F)
shell> mysqldump -u -p -h dbname 
```

###### 2. 网站备份

nfs:  先取消挂载
rsync:  把计划任务先注释   升级完了再开启
共享存储  不用管了

```powershell
shell> mv /data/www/Cloud /data/www/Cloud-$(date +%F)
shell> cd /data/www/
```

##### 二、程序文件升级

###### 1. 解压程序包 

```powershell
shell> unzip Cloud.1.4.0.2.zip
#拷贝license
shell> cp /data/www/Cloud-2016-05-16/Global/license.dat /data/www/Cloud/Global/
```

###### 2. 比较线上程序及最新程序的配置文件

```powershell
shell> wget http://mirrors.cmstop.inc/softs/mediacloud_deploy/diff_config.php
shell> php /opt/diff_config.php /data/www/Cloud-2016-05-16/Global/Config/.env.php /data/www/Cloud/Global/Config/.env.php 
#这一步是对比配置文件升级之后的不同点, 应该有6.7条左右不同 
shell> php /opt/diff_config.php /data/www/Cloud-2016-05-16/Global/Config/.env.php  /data/www/Cloud/Global/Config/.env.example.php 
#把不同的配置拷贝回去
shell> cp /data/www/Cloud-2016-05-16/Global/Config/.env.php /data/www/Cloud/Global/Config/.env.php
```

###### 3.还原线上备份的模板文件 

```powershell
shell> cp /data/www/Cloud-2016-05-16/Frontend/Data/Templates/site/ /data/www/Cloud/Frontend/Data/Templates/
shell> mount -t nfs -o rw 10.2.22.22:/data/www/Cloud/Frontend/Data/Templates/site /data/www/Cloud/Frontend/Data/Templates/site
shell> chown -R nginx:nginx /data/www/Cloud
```

##### 三、数据库升级

```powershell
shell> cd /data/www/Cloud/Global
shell> php automan migration:execute
```

##### 四、★更改静态编号

在.env.php搜索 STATIC_VERSION字段,将其所对应的编号更改为当前日期

### 视频转码升级

##### 一、备份线上程序目录解压最新程序文件

```powershell
shell> /data/service/VideoTranscode #转码目录
shell> mv VideoTranscode VideoTranscode.bk
shell> tar zxvf VideoTranscode.tar.gz
```

##### 二、修改自动配置

```powershell
shell> vim autoConf.sh
shell> rm -rf config/production/
shell> ./autoConf.sh  #自动生成配置     改一下里面的域名 端口  redis 密码  然后执行就行
shell> ./start.sh
```