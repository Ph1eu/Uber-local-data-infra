
# Hadoop Standalone Mode Installation Guide

This guide walks you through the process of setting up Apache Hadoop in **Standalone Mode** on macOS or Linux. In standalone mode, Hadoop runs on a single machine and uses the local filesystem. No HDFS, YARN, or distributed computing features are used.

## Prerequisites

Before setting up Hadoop, ensure that the following prerequisites are met:

1. **Java JDK 8 or later** is installed on your system.
2. **SSH** is set up (though not required in standalone mode).

### Step 1: Install Java

If Java is not installed, follow these steps:

```bash
# Check if Java is installed
java -version

# If not installed, install OpenJDK
sudo apt-get update
sudo apt-get install -y openjdk-8-jdk

# Set JAVA_HOME (add this to ~/.bashrc or ~/.zshrc)
export JAVA_HOME=$(dirname $(dirname $(readlink -f $(which javac))))
```

### Step 2: Download and Extract Hadoop

Download the latest version of Hadoop from the Apache website:

- Linux version : 

```bash
# Download Hadoop (replace version number as needed)
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz

# Extract the tarball
tar -xzvf hadoop-3.3.6.tar.gz

# Move Hadoop to /usr/local (optional)
sudo mv hadoop-3.3.6 /usr/local/hadoop
```
- Mac OS version:
```bash
brew install hadoop
```
### Step 3: Configure Hadoop Environment Variables
- Linux version:

Add Hadoop environment variables to your `.bashrc` or `.zshrc`:

```bash
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

Reload your shell configuration:

```bash
source ~/.bashrc
```
- Mac OS Version:

Bin,Configuration files contain in this directory :
```bash
cd opt/homebrew/Cellar/hadoop/$${{version}}
```
### Step 4: Configure Hadoop for Standalone Mode

1. **Edit `core-site.xml`**

Modify the `core-site.xml` file to use the local file system:

```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>file://</value>
    </property>
</configuration>
```

2. **Edit `mapred-site.xml`**

Copy the template file and set Hadoop to run in local mode:

```bash
cp $HADOOP_HOME/etc/hadoop/mapred-site.xml.template $HADOOP_HOME/etc/hadoop/mapred-site.xml
```

Edit `mapred-site.xml` to specify local execution:

```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>local</value>
    </property>
</configuration>
```

3. **Edit `hdfs-site.xml` and `yarn-site.xml`**

No changes are needed for standalone mode, but ensure the files are present in `$HADOOP_HOME/etc/hadoop/`. These files can remain empty.

### Step 5: Test the Installation

Check that Hadoop is properly installed by running:

```bash
hadoop version
```

You should see the installed Hadoop version details.

### Step 6: Running a Sample Hadoop Job

In standalone mode, you can run Hadoop jobs locally. Use the following command to run a sample word count job:

```bash
# Create input directory and sample file
mkdir input
echo "Hello Hadoop" > input/file.txt

# Run word count job
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar wordcount input output

# Check output
cat output/*
```

### Troubleshooting

- **Permission Denied (SSH)**: If you encounter SSH errors, ensure that you are not trying to start HDFS or YARN (`start-dfs.sh`, `start-yarn.sh`), as they are not required in standalone mode.
- **Native-Hadoop Library Warning**: This is normal, and Hadoop will fallback to using Java classes.

## Conclusion

You have successfully set up Hadoop in standalone mode! This setup is ideal for testing and debugging Hadoop applications on a single machine. For distributed setups, consider configuring pseudo-distributed or fully-distributed modes.
