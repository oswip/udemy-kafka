## Install Kafka with KRaft (without Zookeeper) on Mac OS X

1. Install Java JDK version 11

2. Download Apache Kafka from [https://kafka.apache.org/downloads](https://kafka.apache.org/downloads) under 'Binary Downloads'

3. Extract the contents on your Mac

4. Generate a cluster ID and format the storage using `kafka-storage.sh`

5. Start Kafka using the binaries

6. Setup the $PATH environment variables for easy access to the Kafka binaries

### Installing Java JDK 11

To install Apache Kafka on Mac, Java 11 is the only prerequisite.

- Navigate to [Amazon Corretto 11 download page](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/downloads-list.html) (the Amazon distribution of OpenJDK 11) install [amazon-corretto-11-x64-macos-jdk.pkg](https://corretto.aws/downloads/latest/amazon-corretto-11-x64-macos-jdk.pkg)

- Double click on the downloaded file and proceed with the installation steps.

- You have successfully installed Java JDK 11. You may delete the .pkg file.

![Screenshot of Amazon Corretto installer that will give you the distribution of OpenJDK 11 needed to install Apache Kafka on Mac](markdown-images/.png "Amazon Corretto Installer - the Amazon distribution of OpenJDK 11")

Please follow the instructions [here](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/macos-install.html#macos-install-instruct) to verify your installation of Amazon Corretto 11. Instructions on how to set the default version of Java on a Mac can be found [here](https://stackoverflow.com/a/24657630).

`1 2 3` `openjdk version "11.0.10" 2021-01-19 LTS OpenJDK Runtime Environment Corretto-11.0.10.9.1 (build 11.0.10+9-LTS) OpenJDK 64-Bit Server VM Corretto-11.0.10.9.1 (build 11.0.10+9-LTS, mixed mode)shell`

### Install Apache Kafka

Download the latest version of Apache Kafka from [https://kafka.apache.org/downloads](https://kafka.apache.org/downloads) under Binary downloads.

![The download page for Apache Kafka where you can download and install Kafka.](markdown-images/.1.png "Install Kafka - Apache Kafka Download")

2\. Click on any of the binary downloads (it is preferred to choose the most recent Scala version - example 2.13). For this illustration, we will assume version `2.13-3.0.0`.

3\. Download and extract the contents (double click in the Finder) to a directory of your choice, for example `~/kafka_2.13-3.0.0` .

4\. Navigate to the root of the Apache Kafka folder and open a Terminal. Or Open a Terminal and navigate to the root directory of Apache Kafka. For this example, we will assume that the Kafka download is expanded into the `~/kafka_2.13-3.0.0` directory.

### Start Kafka

The first step is to generate a new ID for your cluster

`1` `~/kafka_2.13-3.0.0/bin/kafka-storage.sh random-uuid`

This returns a UUID, for example `76BLQI7sT_ql1mBfKsOk9Q`

Next, format your storage directory (replace <uuid> by your UUID obtained above)

`1` `~/kafka_2.13-3.0.0/bin/kafka-storage.sh format -t <uuid> -c ~/kafka_2.13-3.0.0/config/kraft/server.properties`

This will format the directory that is in the `log.dirs` in the `config/kraft/server.properties` file (by default `/tmp/kraft-combined-logs`)

Now you can launch the broker itself in daemon mode by running this command.

`1` `~/kafka_2.13-3.0.0/bin/kafka-server-start.sh ~/kafka_2.13-3.0.0/config/kraft/server.properties`

![Screenshot of Apache Kafka setup in KRaft mode on Mac via Terminal](markdown-images/.2.png "Starting Kafka KRaft via Terminal on Mac")

Don’t close this terminal window as it will shutdown the broker.

**Congratulations, the broker is now running on its own in KRaft mode!**

### Setup the $PATH environment variable

In order to easily access the Kafka binaries, you can edit your PATH variable by adding the following line (edit the content to your system) to your system run commands (for example `~/.zshrc` if you use zshrc):

`PATH="$PATH:/Users/stephanemaarek/kafka_2.13-3.0.0/bin"`

This ensures that you can now run the kafka commands without prefixing them.

After reloading your terminal, the following should work from any directory

### Read more

You can read and learn more about the KRaft mode in Kafka [here](https://github.com/apache/kafka/blob/trunk/config/kraft/README.md).