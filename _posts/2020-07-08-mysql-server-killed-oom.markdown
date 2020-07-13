---
layout: post
title:  "MySQL DB startup 직후 바로 죽을 때 (OOM)"
date:   2020-07-08 11:35:44 +0900
categories: jekyll update mysql
---

MySQL DB를 기동시켰는데, 정상적으로 기동되지 않고 바로 죽는다?

물론 이 현상 하나만 보고서 '이것이 문제다!' 라고 정의하기란 어렵다. 여러가지 이유가 있을 수 있기 때문이다.

다양한 사례 중 개인 테스트 환경에서 DB를 띄우려다가, 문제에 봉착했던 경험에 대해 간단하게 적어본다. 


### 증상

아래와 같이 MySQL 서버가 뜨자마자 바로 죽어버린다. 별다른 로그 없이 Killed 되었단다.

```bash
[root@test-sj mysql56]# bin/mysqld_safe --defaults-file=/usr/local/mysql56/my.cnf
200127 15:33:42 mysqld_safe Logging to '/usr/local/mysql56/log/mysql.err'.
200127 15:33:42 mysqld_safe Starting mysqld daemon with databases from /usr/local/mysql56/data

bin/mysqld_safe: line 183:  2936 Killed
                  nohup /usr/local/mysql56/bin/mysqld --defaults-file=/usr/local/mysql56/my.cnf ..(생략)..
200127 15:33:44 mysqld_safe mysqld from pid file /usr/local/mysql56/log/mysql.pid ended
```



### 확인할 것

단서는 /var/log/messages에 있었다.

Out of Memory!!! OOM이 발생하여 DB를 죽인 것이다.

```bash
[root@test-sj mysql56]# cat /var/log/messages 

Jan 27 15:34:06 test-sj kernel: Out of memory: Kill process 3161 (mysqld) score 626 or sacrifice child
Jan 27 15:34:06 test-sj 
    kernel: Killed process 3161 (mysqld), UID 0, total-vm:493444kB, anon-rss:387328kB, file-rss:4kB, shmem-rss:0kB
```


> **참고자료**
>
> * MySQL keeps going down [https://www.virtualmin.com/node/52044](https://www.virtualmin.com/node/52044){:target="_blank"}
>
> * mysqld is getting killed by own [https://stackoverflow.com/questions/47268142/mysqld-is-getting-killed-by-own](https://stackoverflow.com/questions/47268142/mysqld-is-getting-killed-by-own){:target="_blank"}
