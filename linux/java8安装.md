# java8安装

* **命令安装**

```shell
sudo yum install java-1.8.0-openjdk-devel
```

* 配置环境变量

  * 在/etc/profile文件中，新增以下两行

  ```shell
  export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk 
  export PATH=$JAVA_HOME/bin:$PATH
  ```

  * 使变量文件生效

  ```shell
  source /etc/profile
  ```

  