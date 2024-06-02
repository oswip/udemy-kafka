Install Kafka with Zookeeper

For maximum compatibility purposes, it is recommended to start Apache Kafka with Zookeeper. If you would like to run Kafka without Zookeeper, take a look [here](https://www.conduktor.io/kafka/how-to-install-apache-kafka-on-linux-without-zookeeper-kraft-mode/).

## How to install Kafka with Zookeeper on Linux

1.  Install Java JDK version 11
    
2.  Download Apache Kafka from [https://kafka.apache.org/downloads](https://kafka.apache.org/downloads) under Binary Downloads
    
3.  Extract the contents on Linux
    
4.  Start Zookeeper using the binaries
    
5.  Start Kafka using the binaries in another process
    
6.  Setup the $PATH environment variables for easy access to the Kafka binaries
    

## Installing Java JDK 11

To install Apache Kafka on Linux, Java 11 is the only prerequisite.

1.  Navigate to [Amazon Corretto 11 Linux install page](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/linux-info.html) and follow the steps, which work for Debian, RPM, Alpine and Amazon Linux. Alternatively, you can download from the [Amazon Corretto 11 download page](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/downloads-list.html) and install the correct package for your Linux distribution (x64, aarch64, x86, arch32, etc...).
    
2.  For example on Ubuntu (Debian-based systems)
    

`1 2 3` `wget -O- https://apt.corretto.aws/corretto.key | sudo apt-key add - sudo add-apt-repository 'deb https://apt.corretto.aws stable main' sudo apt-get update; sudo apt-get install -y java-11-amazon-corretto-jdk`

Please follow the instructions [here](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/generic-linux-install.html) to verify your installation of Amazon Corretto 11 and set the JDK as your default Java in your Linux system.

Upon completion, you should get a similar output when doing `java -version`:

`1 2 3` `openjdk version "11.0.10" 2021-01-19 LTS OpenJDK Runtime Environment Corretto-11.0.10.9.1 (build 11.0.10+9-LTS) OpenJDK 64-Bit Server VM Corretto-11.0.10.9.1 (build 11.0.10+9-LTS, mixed mode)shell`

1\. Download the latest version of Apache Kafka from [https://kafka.apache.org/downloads](https://kafka.apache.org/downloads) under Binary downloads.

![The download page for Apache Kafka where you can download and install Kafka.](markdown-images/.png "Install Kafka - Apache Kafka Download")

2\. Click on any of the binary downloads (it is preferred to choose the most recent Scala version - example 2.13). For this illustration, we will assume version `2.13-3.0.0`.

Alternatively you can run a wget command

`1` `wget https://archive.apache.org/dist/kafka/3.0.0/kafka_2.13-3.0.0.tgz`

3\. Download and extract the contents to a directory of your choice, for example `~/kafka_2.13-3.0.0` .

`1 2` `tar xzf kafka_2.13-3.0.0.tgz mv kafka_2.13-3.0.0 ~`

4\. Open a Shell and navigate to the root directory of Apache Kafka. For this example, we will assume that the Kafka download is expanded into the `~/kafka_2.13-3.0.0` directory.

## Start Zookeeper

Apache Kafka depends on Zookeeper for cluster management. Hence, prior to starting Kafka, Zookeeper has to be started. There is no need to explicitly install Zookeeper, as it comes included with Apache Kafka.

Make sure your `JAVA_HOME` environment variable is set first, as instructed in the Install Java section, so that Java 11 is used when doing `java -version`

From the root of Apache Kafka, run the following command to start Zookeeper:

`1` `~/kafka_2.13-3.0.0/bin/zookeeper-server-start.sh ~/kafka_2.13-3.0.0/config/zookeeper.properties`

![Screenshot showing the process for launching Zookeeper for Kafka on Linux](markdown-images/.1.png "Starting Zookeeper for Apache Kafka on Linux")

Daemon Mode

You can use the -daemon flag to run Zookeeper in daemon mode in the background

## Start Apache Kafka

**Open another Shell window** and run the following command from the root of Apache Kafka to start Apache Kafka.

`1` `~/kafka_2.13-3.0.0/bin/kafka-server-start.sh ~/kafka_2.13-3.0.0/config/server.properties`

![Screenshot showing Kafka launching on Linux](markdown-images/.2.png "Starting Apache Kafka on Linux")

Ensure to keep both shell windows opened, otherwise you will shut down Kafka or Zookeeper.

**Kafka is now started, congratulations!**

## Setup the $PATH environment variable

In order to easily access the Kafka binaries, you can edit your PATH variable by adding the following line to your system run commands (for example `~/.zshrc` if you use zshrc or `~/.bashrc` for Bash ):

`PATH="$PATH:~/kafka_2.13-3.0.0/bin"`

This ensures that you can now run the kafka commands without prefixing them.

After reloading your shell, the following should work from any directory

`1 2` `This ensures that you can now run the kafka commands without prefixing them. After reloading your shell, the following should work from any directory`

## Optional: Changing the Kafka and Zookeeper data storage directory

For Zookeeper:

-   edit the `zookeeper.properties` file at `~/kafka_2.13-3.0.0/config/zookeeper.properties` and set the following to your heart's desire `dataDir=/your/path/to/data/zookeeper`
    
-   start Zookeeper using the update `zookeeper.properties` file as shown above
    
-   you can also make a copy of the `zookeeper.properties` file anywhere in your computer and edit that file instead, and reference it in the Zookeeper start command shown above
    

For Kafka:

-   edit the `server.properties` file at `~/kafka_2.13-3.0.0/config/server.properties` and set the following to your heart's desire `log.dirs=/your/path/to/data/kafka`
    
-   start Zookeeper using the update `server.properties` file as shown above
    
-   you can also make a copy of the `server.properties` file anywhere in your computer and edit that file instead, and reference it in the Zookeeper start command shown above