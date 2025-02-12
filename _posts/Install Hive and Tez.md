# Installing Hive, Tez and using MariaDB as metastore

## Download and extract hive

    Download and extract hive from apache hive website. Download version that support your hadoop version. If hadoop is 3.x download 3.x version of hive else 2.x version of hive.

## Setup MariaDB

### To Install 

``` sudo pacman -S mariadb ```

``` sudo mysql_install_db --user=mysql --basedir=/usr --datadir=/var/lib/mysql ```

``` sudo systemctl enable mariadb ```

``` sudo systemctl start mariadb ```

``` sudo mysql_secure_installation ```

### Download and Install JDBC Connector

```wget https://downloads.mariadb.com/Connectors/java/connector-java-2.4.2/mariadb-java-client-2.4.2.jar```

Copy the jar to HIVE_HOME/lib folder

### Setup metastore

``` sudo su - ``` //if you have access issue, login as root and try

or follow 

https://stackoverflow.com/questions/41645309/mysql-error-access-denied-for-user-rootlocalhost


``` mysql -u root -p ```

``` create database metastore ```

``` use metastore ```

``` source /home/appuser/hive/scripts/metastore/upgrade/mysql/hive-schema-3.1.0.mysql.sql ```

``` CREATE USER 'appuser'@'%' IDENTIFIED BY 'o'; ``` 

```GRANT all on *.* to 'appuser'@localhost identified by 'o';```

```flush privileges;```

* Note replace 'appuser' and 'o' with actual one


* Note : (All commands can be used for mysql as well with slight changes)

## Setup Hive

For this document purpose, we assume hive and tez are extracted in location "/home/appuser/hive" and /home/appuser/tez

### Setup HDFS Location for hive
```
hdfs dfs -mkdir /tmp
hdfs dfs -mkdir -p /user/hive/warehouse
hdfs dfs -chmod -R g+w  /tmp
hdfs dfs -chmod -R g+w /user/hive/warehouse
```
### Setup enviroment variable in .bashrc

```
export HADOOP_HOME=<HADOOP_HOME_DIR>
export HIVE_HOME=<HIVE_HOME_DIR>

export PATH=$PATH:$HIVE_HOME/bin
```

### Setup hive.xml

create or edit hive-site.xml in HIVE_HOME/conf folder and add the below lines

```

<configuration>
   <property>
      <name>javax.jdo.option.ConnectionURL</name>
      <value>jdbc:mysql://localhost/metastore</value>
   </property>
   <property>
      <name>javax.jdo.option.ConnectionDriverName</name>
      <value>com.mariadb.jdbc.Driver</value>
   </property>
   <property>
      <name>javax.jdo.option.ConnectionUserName</name>
      <value>appuser</value>
   </property>
   <property>
      <name>javax.jdo.option.ConnectionPassword</name>
      <value>o</value>
   </property>
</configuration>

```


## setup Tez

### Copy lib to hdfs

```
hdfs dfs -put ~/tez/share/tez.tar.gz /apps/lib/.

```

### Setup enviroment variable

Copy below lines as part of your .bashrc file
```
export TEZ_CONF_DIR=~/tez/conf
export TEZ_JARS=~/tez/*:~/tez/lib/*
export HADOOP_CLASSPATH=$TEZ_CONF_DIR:$TEZ_JARS:$HADOOP_CLASSPATH
```

### configure tez-site.xml
```
<property>                                                       <name>tez.lib.uris</name>
     <value>/apps/lib/tez.tar.gz</value>
</property>
```

### Start Server
hiveserver2

beeline -u jdbc:hive://localhost:10000