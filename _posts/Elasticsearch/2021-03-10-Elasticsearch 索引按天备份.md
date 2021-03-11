---
layout: post
categories: Elasticsearch
title: Elasticsearch 索引按天备份
meta: Elasticsearch 索引按天备份
---
* content
{:toc}

### 正文

由于生产es索引按天切分比较多，手动备份工作量比较大，且容易出错，所以我们通过python写了一个定时备份的脚本，
同时将备份信息记录到数据库(MySQL)，将备份文件上传至阿里云OSS。

#### 脚本

```
# -*- coding: utf-8 -*-
from __future__ import print_function
import os, sys
from datetime import datetime, date, timedelta
import pymysql
import subprocess
import json
import socket
import requests
backup_path_base = "/data8/upload/"
repo_path = "/data8/backup"
backup_date = (date.today() + timedelta(days = -7)).strftime("%Y.%m.%d")
backup_month = (date.today() + timedelta(days = -7)).strftime("%Y.%m")
oss_path = "elasticsearch/business_log/"+backup_month
def create_sign(params):
    import hashlib
    key = '**********'
    if isinstance(params, dict):
        params['type'] = "DB"
        sort_params = sorted(params.keys())
        sort_params.append('key')
        params['key'] = key
        sign_str = ''
        for k in sort_params:
            if params[k]:
                sign_str += "&" + str(k) + '=' + str(params[k])
        sign_str = sign_str.lstrip('&')
        m2 = hashlib.md5()
        m2.update(sign_str.encode("utf-8"))
        return m2.hexdigest().upper()
    return ''
def decrypt(decrypt_str):
    if not decrypt_str:
        return ''
    from Crypto.Cipher import DES
    from binascii import a2b_hex
    decrypt_str = bytes(decrypt_str, encoding="utf8")
    key = b'T6qCxy9F'
    des = DES.new(key, DES.MODE_ECB)
    return des.decrypt(a2b_hex(decrypt_str)).decode().rstrip(' ')
 
def get_host_ip():
    try:
        s=socket.socket(socket.AF_INET,socket.SOCK_DGRAM)
        s.connect(('8.8.8.8',80))
        ip=s.getsockname()[0]
    finally:
        s.close()
    return ip
backup_host = get_host_ip()
#发送邮件函数
def task_sendmail():
    params_post = {'title': 'Elasticsearch索引备份', 'content':backup_host+'Elasticsearch索引备份失败，请及时关注！' }
    params_post['sign'] = create_sign(params_post)
    task_sendmail_post = requests.post('http://172.16.xx.xxx:8080/db/send_mail/', data = params_post)
 
#数据库操作
def mysql_op(op_type,index_name,stage='',op_code=999,log_detail='',op_status='',es_host=backup_host):
    db_host = '172.16.6.75'
    port = 3306
    user = 'dbaadmin'
    password = '***********'
    conn = pymysql.connect(host = db_host,user = user,passwd = password,port = port,db = 'ops',charset = 'utf8')
    cursor = conn.cursor()
    try:
        if op_type == "get":
            sql= "select last_op_status from es_index_backup " + "where index_name='" + index_name + "' and host='"+es_host+"';"
            cursor.execute(sql)
            result = cursor.fetchone()
            if result == None:
                cursor.execute("insert into es_index_backup(host,index_name) values('"+es_host+"','" + index_name +"')")
                conn.commit()
                return 0
            elif result[0] != 1:
                return 0
            else:
                return 1
        elif op_type =='log':
            sql= "insert into es_index_backup_log(host,index_name,stage,op_code,log_detail,op_status)values('" +es_host+"','" +index_name  +"','"+stage+"','"+ str(op_code) +"','"+log_detail+"','" +op_status+"')"
            cursor.execute(sql)
            conn.commit()
        elif op_type=="update":
            sql= "update es_index_backup set last_op_status=1 " + " where index_name='" + index_name + "' and host='"+es_host+"';"
            cursor.execute(sql)
            conn.commit()
        conn.close()
    except:
        conn.close()
#ES操作
curl_get = "curl -H \"Content-Type: application/json\" -XGET http://" + backup_host +":9200/_cat/indices/*" + backup_date +"*?h=i > /opt/script/backup_index_daily.txt"
status, output = subprocess.getstatusoutput(curl_get)
if status !=0 :
   exit()
index_name_list = open('/opt/script/backup_index_daily.txt')
for index_name in index_name_list:
    index_name=index_name.strip('\n')
    if len(index_name) == 0:
       exit()
    #判断是否已经备份
    if mysql_op('get',index_name) == 0:
        curl_get = "curl -H \"Content-Type: application/json\" -XGET http://" + backup_host +":9200/_cat/indices/" + index_name + " 2>>/dev/null"
        status, output = subprocess.getstatusoutput(curl_get)
        if status == 0:
            e="\"error\""
            result = e in output
            if result:
                mysql_op(op_type='log',index_name=index_name,stage='get',op_code=status,log_detail=output,op_status="failure")
                task_sendmail()
                exit()
            else:
                mysql_op(op_type='log',index_name=index_name,stage='get',op_code=status,log_detail=output,op_status="success")
        else:
            mysql_op(op_type='log',index_name=index_name,stage='get',op_code=status,log_detail=output,op_status="failure")
            task_sendmail()
            exit()
        curl_put = "curl -H \"Content-Type: application/json\" -XPUT http://" + backup_host +":9200/_snapshot/es_backup/snapshot_" + index_name + "?wait_for_completion=true -d " +"'{\"indices\": \"" + index_name + "\"}' 2>>/dev/null "
        status, output = subprocess.getstatusoutput(curl_put)
        t='"state":"SUCCESS"'
        result= t in output
        #备份成功时
        if result:
            mysql_op(op_type='log',index_name=index_name,stage='backup',op_code=status,log_detail=output,op_status="success")
        else:
            mysql_op(op_type='log',index_name=index_name,stage='backup',op_code=status,log_detail=output,op_status="failure")
            task_sendmail()
            exit()
        #打包
        os.chdir(backup_path_base)
        compress_filename= index_name +".tar.gz"
        tar_cmd="tar czf "+ index_name +".tar.gz " + repo_path
        status, output = subprocess.getstatusoutput(tar_cmd)
        if status ==0 :
            mysql_op(op_type='log',index_name=index_name,stage='tar',op_code=status,log_detail="tar czf "+ index_name +".tar.gz " + repo_path + " success",op_status="success")
        else:
            mysql_op(op_type='log',index_name=index_name,stage='tar',op_code=status,log_detail=output,op_status="failure")
            task_sendmail()
            exit()
        #上传
        upload_cmd = "python /opt/script/upload_to_oss.py ****** ********* oss-cn-hangzhou-internal.aliyuncs.com vip-backup " + backup_path_base + compress_filename+" "  +  oss_path
        status, output = subprocess.getstatusoutput(upload_cmd)
        if status ==0:
            mysql_op(op_type='log',index_name=index_name,stage='upload',op_code=status,log_detail="upload "+ index_name +".tar.gz to oss success",op_status="success")
        else:
            mysql_op(op_type='log',index_name=index_name,stage='upload',op_code=status,log_detail="upload "+ index_name +".tar.gz to oss failure",op_status="failure")
            task_sendmail()
            exit()
        #删除打包文件
        rm_cmd="rm -rf "+backup_path_base+compress_filename
        status, output = subprocess.getstatusoutput(rm_cmd)
        if status ==0:
            mysql_op(op_type='log',index_name=index_name,stage='rm',op_code=status,log_detail="rm -rf  "+ index_name +".tar.gz success",op_status="success")
        else:
            mysql_op(op_type='log',index_name=index_name,stage='rm',op_code=status,log_detail="rm -rf  "+ index_name +".tar.gz failure",op_status="failure")
            task_sendmail()
            exit()
        #删除快照
        curl_cmd="curl -XDELETE http://" + backup_host + ":9200/_snapshot/es_backup/snapshot_"+index_name+" 2>>/dev/null"
        status, output = subprocess.getstatusoutput(curl_cmd)
        if status ==0:
            e="\"error\""
            result = e in output
            if result:
                mysql_op(op_type='log',index_name=index_name,stage='XDELETE',op_code=status,log_detail=output,op_status="failure")
                task_sendmail()
                exit()
            else:
                mysql_op(op_type='log',index_name=index_name,stage='XDELETE',op_code=status,log_detail="XDELETE snapshot_" + index_name + " success",op_status="success")
        else:
            mysql_op(op_type='log',index_name=index_name,stage='XDELETE',op_code=status,log_detail=output,op_status="failure")
            task_sendmail()
            exit()
        #更新备份状态为成功
        mysql_op('update',index_name)
    else:
        print('backuped')
index_name_list.close()
```

#### 数据表

```
mysql> show create table es_index_backup\G
*************************** 1. row ***************************
       Table: es_index_backup
Create Table: CREATE TABLE `es_index_backup` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `host` varchar(15) DEFAULT '',
  `index_name` varchar(100) NOT NULL DEFAULT '',
  `last_op_status` tinyint(4) NOT NULL DEFAULT '0',
  `create_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uniq_host_indexname` (`host`,`index_name`)
) ENGINE=InnoDB AUTO_INCREMENT=34322 DEFAULT CHARSET=utf8
1 row in set (0.30 sec)
 
mysql> show create table es_index_backup_log\G
*************************** 1. row ***************************
       Table: es_index_backup_log
Create Table: CREATE TABLE `es_index_backup_log` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `host` varchar(15) DEFAULT '',
  `index_name` varchar(100) NOT NULL DEFAULT '',
  `stage` varchar(20) NOT NULL COMMENT '所处阶段',
  `op_code` int(11) NOT NULL DEFAULT '0' COMMENT '返回码',
  `op_status` varchar(20) NOT NULL COMMENT '状态',
  `log_detail` text NOT NULL,
  `create_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=205919 DEFAULT CHARSET=utf8
1 row in set (0.63 sec)
```

<br/><br/><br/><br/><br/>
### 参考资料


