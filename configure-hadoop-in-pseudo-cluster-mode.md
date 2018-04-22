## Set the JAVA_HOME environment variable for Hadoop

Open the `hadoop-env.sh` file under `etc/hadoop` folder. At the bottom of the file, expose the JAVA_HOME to point at the installation path of Java.

```
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-10.0.1.jdk/Contents/Home
```

Please note that if you in Mac, can run `/usr/libexec/java_home` to get the Java installation path.

