sqlmap web interface
=================
## 概述

1. sqlmap web interface是sqlmap REST-JSON API client的一个WEB操作界面，方便用户向 REST-JSON API server发送操作指令。
1. 分布式，支持部署多个REST-JSON API server，且可无限水平扩展。


##  REST-JSON API server部署
### 单REST-JSON API server模式

REST-JSON API server支持监听在指定的IP及端口上：
```python
root@awakenjoys:/data/sqlmap# python sqlmapapi.py 
Usage: sqlmapapi.py [options]

Options:
  -h, --help            show this help message and exit
  -s, --server          Act as a REST-JSON API server
  -c, --client          Act as a REST-JSON API client
  -H HOST, --host=HOST  Host of the REST-JSON API server
  -p PORT, --port=PORT  Port of the the REST-JSON API server
root@awakenjoys:/data/sqlmap# python sqlmapapi.py -H 127.0.0.1 -p 20001 -s
[21:31:51] [INFO] Running REST-JSON API server at '127.0.0.1:20001'..
[21:31:51] [INFO] Admin ID: 53c2083383c14614e9b5a8dde15d942b
[21:31:51] [DEBUG] IPC database: /tmp/sqlmapipc-FFJ_B7
[21:31:51] [DEBUG] REST-JSON API server connected to IPC database
```
### 分布式模式
1. 分布式REST-JSON API server部署：

```python
root@awakenjoys:/etc/supervisor# cat sqlmap.conf
[program:sqlmap_node0]
command=/usr/bin/python /data/sqlmap/sqlmapapi.py -H 127.0.0.1 -p 20000 -s
directory=/data/sqlmap/
autostart=true
startsecs=5
stdout_logfile=/tmp/sqlmap_node0_stdout.log
stderr_logfile=/tmp/sqlmap_node0_stderr.log

[program:sqlmap_node1]
command=/usr/bin/python /data/sqlmap/sqlmapapi.py -H 127.0.0.1 -p 20001 -s
directory=/data/sqlmap/
autostart=true
startsecs=5
stdout_logfile=/tmp/sqlmap_node1_stdout.log
stderr_logfile=/tmp/sqlmap_node1_stderr.log

[program:sqlmap_node2]
command=/usr/bin/python /data/sqlmap/sqlmapapi.py -H 127.0.0.1 -p 20002 -s
directory=/data/sqlmap/
autostart=true
startsecs=5
stdout_logfile=/tmp/sqlmap_node2_stdout.log
stderr_logfile=/tmp/sqlmap_node2_stderr.log

[program:sqlmap_node3]
command=/usr/bin/python /data/sqlmap/sqlmapapi.py -H 127.0.0.1 -p 20003 -s
directory=/data/sqlmap/
autostart=true
startsecs=5
stdout_logfile=/tmp/sqlmap_node3_stdout.log
stderr_logfile=/tmp/sqlmap_node3_stderr.log

root@awakenjoys:/etc/supervisor# supervisorctl reread
sqlmap_node0: available
sqlmap_node1: available
sqlmap_node2: available
sqlmap_node3: available
root@awakenjoys:/etc/supervisor# supervisorctl update
sqlmap_node0: added process group
sqlmap_node1: added process group
sqlmap_node2: added process group
sqlmap_node3: added process group
root@awakenjoys:/etc/supervisor# supervisorctl status
sqlmap_node0                     RUNNING    pid 11910, uptime 0:00:08
sqlmap_node1                     RUNNING    pid 11911, uptime 0:00:08
sqlmap_node2                     RUNNING    pid 11912, uptime 0:00:08
sqlmap_node3                     RUNNING    pid 11913, uptime 0:00:08
```


