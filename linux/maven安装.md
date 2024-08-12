# maven安装

* **下载安装包**

```shell
wget https://archive.apache.org/dist/maven/maven-3/3.6.0/binaries/apache-maven-3.6.0-bin.tar.gz
```

* **解压安装包**

```shell
tar -zxvf apache-maven-3.6.0-bin.tar.gz
```

* **配置环境变量**

  * 在/etc/profile文件中，新增以下三行

  ```shell
  MAVEN_HOME=/usr/local/apache-maven-3.6.0
  export MAVEN_HOME
  export PATH=${PATH}:${MAVEN_HOME}/bin
  ```

  * 使变量文件生效

  ```shell
  source /etc/profile
  ```

* **创建软连接**

```shell
ln -s /root/apache-maven-3.6.0 /usr/local/apache-maven-3.6.0
```

