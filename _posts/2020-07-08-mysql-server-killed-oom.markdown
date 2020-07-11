---
layout: post
title:  "MySQL DB startup 직후 바로 죽을 때 (OOM)"
date:   2020-07-08 11:35:44 +0900
categories: jekyll update mysql
---


### 증상

아래와 같이 MySQL 서버가 뜨자마자 바로 죽어버린다. 별다른 로그 없이 Killed 되었단다.

```bash
[root@test-sj mysql56]# bin/mysqld_safe --defaults-file=/usr/local/mysql56/my.cnf
200127 15:33:42 mysqld_safe Logging to '/usr/local/mysql56/log/mysql.err'.
200127 15:33:42 mysqld_safe Starting mysqld daemon with databases from /usr/local/mysql56/data

bin/mysqld_safe: line 183:  2936 Killed                  nohup /usr/local/mysql56/bin/mysqld --defaults-file=/usr/local/mysql56/my.cnf --basedir=/usr/local/mysql56 --datadir=/usr/local/mysql56/data --plugin-dir=/usr/local/mysql56/lib/plugin --user=mysql --log-error=/usr/local/mysql56/log/mysql.err --pid-file=/usr/local/mysql56/log/mysql.pid --socket=/tmp/mysql.sock < /dev/null > /dev/null 2>&1
200127 15:33:44 mysqld_safe mysqld from pid file /usr/local/mysql56/log/mysql.pid ended
```



### 확인할 것

단서는 /var/log/messages에 있었다.

Out of Memory!!! OOM이 발생하여 DB를 죽인 것이다.

```bash
[root@test-sj mysql56]# cat /var/log/messages 

Jan 27 15:34:06 test-sj kernel: Out of memory: Kill process 3161 (mysqld) score 626 or sacrifice child
Jan 27 15:34:06 test-sj kernel: Killed process 3161 (mysqld), UID 0, total-vm:493444kB, anon-rss:387328kB, file-rss:4kB, shmem-rss:0kB
```

{% highlight bash %}
[root@test-sj mysql56]# cat /var/log/messages 

Jan 27 15:34:06 test-sj kernel: Out of memory: Kill process 3161 (mysqld) score 626 or sacrifice child
Jan 27 15:34:06 test-sj kernel: Killed process 3161 (mysqld), UID 0, total-vm:493444kB, anon-rss:387328kB, file-rss:4kB, shmem-rss:0kB
{% endhighlight %}



> **참고자료**
>
> * MySQL keeps going down [https://www.virtualmin.com/node/52044](https://www.virtualmin.com/node/52044)
>
> * mysqld is getting killed by own [https://stackoverflow.com/questions/47268142/mysqld-is-getting-killed-by-own](https://stackoverflow.com/questions/47268142/mysqld-is-getting-killed-by-own)
