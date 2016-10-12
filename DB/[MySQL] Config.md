# MySQL Configuration

sudo vi /etc/mysql/my.cnf

bind_address 127.0.0.1 을 지워야 워크벤치 외부에서 사용 가능!
max_connection
log_slow_query 

**ohouse slave**
```
# Remember to edit /etc/mysql/debian.cnf when changing the socket location.
[client]
port            = 3306
socket          = /var/run/mysqld/mysqld.sock
default-character-set = utf8mb4

# Here is entries for some specific programs
# The following values assume you have at least 32M ram

# This was formally known as [safe_mysqld]. Both versions are currently parsed.
[mysqld_safe]
socket          = /var/run/mysqld/mysqld.sock
nice            = 0
skip_name_resolve = off

[mysqld]
#
# * Basic Settings
#
init_connect='SET collation_connection = utf8mb4_unicode_ci'
init_connect='SET NAMES utf8mb4'
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci
character-set-client-handshake = FALSE
skip-character-set-client-handshake

user            = mysql
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
port            = 3306
basedir         = /usr
datadir         = /var/lib/mysql
tmpdir          = /tmp
lc-messages-dir = /usr/share/mysql
skip-external-locking
skip-host-cache
skip-name-resolve

#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
# bind-address          = 127.0.0.1
#
# * Fine Tuning
#
key_buffer              = 256M
max_allowed_packet      = 16M
thread_stack            = 192K
thread_cache_size       = 8
# This replaces the startup script and checks MyISAM tables if needed
# the first time they are touched
myisam-recover         = BACKUP
max_connections        = 4096
wait_timeout           = 600
interactive_timeout    = 600
table_cache            = 40960
#thread_concurrency     = 10
#
# * Query Cache Configuration
#
query_cache_limit       = 256M
query_cache_size        = 512M
#
# * Logging and Replication
#
# Both location gets rotated by the cronjob.
# Be aware that this log type is a performance killer.
# As of 5.1 you can enable the log at runtime!
#general_log_file        = /var/log/mysql/mysql.log
#general_log             = 1
#
# Error log - should be very few entries.
#
log_error = /var/log/mysql/error.log
#
# Here you can see queries with especially long duration
log_slow_queries        = /var/log/mysql/mysql-slow.log
long_query_time = 2
#log-queries-not-using-indexes
#
# The following can be used as easy to replay backup logs or for replication.
# note: if you are setting up a replication slave, see README.Debian about
#       other settings you may need to change.
server-id               = 102
binlog-format           = mixed
log_bin                 = /var/log/mysql/mysql-bin.log
relay-log               = mysql-relay-bin
log-slave-updates       = 1
read-only               = 1
expire_logs_days        = 3
max_binlog_size         = 100M
#binlog_do_db           = include_database_name
#binlog_ignore_db       = include_database_name
#
# * InnoDB
#
# InnoDB is enabled by default with a 10MB datafile in /var/lib/mysql/.
# Read the manual for more InnoDB related options. There are many!
#
# * Security Features
#
# Read the manual, too, if you want chroot!
# chroot = /var/lib/mysql/
#
# For generating SSL certificates I recommend the OpenSSL GUI   
# ssl-cert=/etc/mysql/server-cert.pem
# ssl-key=/etc/mysql/server-key.pem
innodb_thread_concurrency = 0
innodb_buffer_pool_size = 1GB
innodb_buffer_pool_instances = 15
innodb_use_sys_malloc = 0
innodb_fast_shutdown = 0
innodb_log_buffer_size = 512M

join_buffer_size        = 256K
tmp_table_size          = 512M
max_heap_table_size     = 512M



[mysqldump]
quick
quote-names
max_allowed_packet      = 16M

[mysql]
#no-auto-rehash # faster start of mysql but no tab completition

[isamchk]
key_buffer              = 16M

#
# * IMPORTANT: Additional settings that can override those from this file!
#   The files must end with '.cnf', otherwise they'll be ignored.
#
!includedir /etc/mysql/conf.d/
```