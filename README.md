# 要求：
##1、安装nodejs
```shell script
curl --silent --location https://rpm.nodesource.com/setup_14.x | bash -
yum install -y nodejs

npm install -g n
n 14.15.0
echo '
# NODE
export NODE_HOME=/usr/local/n/versions/node/14.15.0
export PATH=$NODE_HOME/bin:$PATH' >> /etc/profile
source /etc/profile
```

##2、安装yarn
```shell script
curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo
sudo yum install yarn
npm install -g bower
npm install -g gulp
```

##3、安装c++
```shell script
yum install gcc-c++
# 若是Mac，需要安装Xcode
#wget https://download.developer.apple.com/Developer_Tools/Xcode_10/Xcode_10.xip
#xattr -d com.apple.quarantine Xcode_10.xip 
#brew install unxip
#unxip Xcode_10.xip .
#mv Xcode.app /Applications
```

##4、JDK安装（假设JDK已下载完毕）
```shell script
tar zxvf jdk-8u271-linux-x64.tar.gz -C /usr/local
echo '
# JDK
export JAVA_HOME=/usr/local/jdk1.8.0_271
export JRE_HOME=/usr/local/jdk1.8.0_271/jre
export CLASSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$PATH' >> /etc/profile
source /etc/profile 
```

##5、安装MAVEN（~/.m2/settings.xml）：
```shell script
tar zxvf apache-maven-3.6.3-bin.tar.gz -C /usr/local
echo '
# MAVEN
export MAVEN_HOME=/usr/local/apache-maven-3.6.3
export PATH=$MAVEN_HOME/bin:$PATH' >> /etc/profile
source /etc/profile 

echo '
<?xml version="1.0"?>
<settings>
  <mirrors>
        <mirror>
            <id>alimaven</id>
            <name>aliyun maven</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
            <mirrorOf>central</mirrorOf>
        </mirror>
  </mirrors>
</settings>
' >> ~/.m2/settings.xml
```



#编译：
```shell script
mvn versions:set -DnewVersion=2.7.3.0.0
pushd ambari-metrics
mvn versions:set -DnewVersion=2.7.3.0.0
popd

mvn -B clean install rpm:rpm -DnewVersion=2.7.3.0.0 -DbuildNumber=4295bb16c439cbc8fb0e7362f19768dde1477868 -DskipTests -Dpython.ver="python >= 2.6" -Drat.ignoreErrors=true
```

# QA
## Q1:
```
[INFO] Downloading https://nodejs.org/dist/v4.5.0/node-v4.5.0-darwin-x64.tar.gz to /root/.m2/repository/com/github/eirslett/node/4.5.0/node-4.5.0-darwin-x64.tar.gz
[INFO] No proxies configured
[INFO] No proxy was configured, downloading directly

[INFO] Downloading https://nodejs.org/dist/v8.6.0/node-v8.6.0-darwin-x64.tar.gz to /root/.m2/repository/com/github/eirslett/node/8.6.0/node-8.6.0-darwin-x64.tar.gz
[INFO] No proxies configured
[INFO] No proxy was configured, downloading directly
```

##A1:
```
使用自主下载方式下载该版本node到~/.m2/repository/com/github/eirslett/node/4.5.0目录。
mkdir -p ~/.m2/repository/com/github/eirslett/node/4.5.0
wget -P ~/.m2/repository/com/github/eirslett/node/4.5.0 https://nodejs.org/dist/v4.5.0/node-v4.5.0-darwin-x64.tar.gz -O node-4.5.0-darwin-x64.tar.gz

mkdir -p ~/.m2/repository/com/github/eirslett/node/8.6.0
wget -P ~/.m2/repository/com/github/eirslett/node/8.6.0 https://nodejs.org/dist/v8.6.0/node-v8.6.0-darwin-x64.tar.gz -O node-8.6.0-darwin-x64.tar.gz
```


##Q2:
```
[INFO] Downloading https://github.com/yarnpkg/yarn/releases/download/v0.23.2/yarn-v0.23.2.tar.gz to /root/.m2/repository/com/github/eirslett/yarn/0.23.2/yarn-0.23.2./yarn-v0.23.2.tar.gz
[INFO] No proxies configured
[INFO] No proxy was configured, downloading directly
```

##A2:
```
使用自主下载方式下载该版本yarn到~/.m2/repository/com/github/eirslett/yarn/0.23.2/yarn-0.23.2.目录。
mkdir -p ~/.m2/repository/com/github/eirslett/yarn/0.23.2/yarn-0.23.2.
wget -P ~/.m2/repository/com/github/eirslett/yarn/0.23.2/yarn-0.23.2. https://github.com/yarnpkg/yarn/releases/download/v0.23.2/yarn-v0.23.2.tar.gz
```


##Q3:
```
[ERROR] Failed to execute goal com.github.eirslett:frontend-maven-plugin:1.4:yarn (yarn install) on project ambari-web: Failed to run task: 'yarn install --ignore-engines --pure-lockfile' failed. org.apache.commons.exec.ExecuteException: Process exited with an error: 1 (Exit value: 1) -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoFailureException
[ERROR] 
[ERROR] After correcting the problems, you can resume the build with the command
[ERROR]   mvn <goals> -rf :ambari-web
```

##A3:
```
移除./ambari-web/yarn.lock文件
```


##Q4：
```
[INFO] Downloading http://registry.npmjs.org/npm/-/npm-2.15.0.tgz to /root/.m2/repository/com/github/eirslett/npm/2.15.0/npm-2.15.0.tar.gz
[INFO] No proxies configured
[INFO] No proxy was configured, downloading directly
```

##A4：
```
使用自主下载方式下载该版本npm到~/.m2/repository/com/github/eirslett/npm/2.15.0目录。
mkdir -p ~/.m2/repository/com/github/eirslett/npm/2.15.0
wget -P ~/.m2/repository/com/github/eirslett/npm/2.15.0 http://registry.npmjs.org/npm/-/npm-2.15.0.tgz -O npm-2.15.0.tar.gz
```


##Q5:
```
Stack trace:
SyntaxError: Unexpected token {
    at exports.runInThisContext (vm.js:53:16)
    at Module._compile (module.js:373:25)
    at Object.Module._extensions..js (module.js:416:10)
    at Module.load (module.js:343:32)
    at Function.Module._load (module.js:300:12)
    at Module.require (module.js:353:17)
    at require (internal/module.js:12:17)
    at Object.<anonymous> (/root/Projects/OpenSourceProjects/Ambari/ambari-admin/src/main/resources/ui/admin-web/node_modules/bower/node_modules/bower-registry-client/node_modules/request/lib/cookies.js:3:13)
    at Module._compile (module.js:409:26)
    at Object.Module._extensions..js (module.js:416:10)

Console trace:
Trace
    at StandardRenderer.error (/root/Projects/OpenSourceProjects/Ambari/ambari-admin/src/main/resources/ui/admin-web/node_modules/bower/lib/renderers/StandardRenderer.js:72:17)
    at Logger.<anonymous> (/root/Projects/OpenSourceProjects/Ambari/ambari-admin/src/main/resources/ui/admin-web/node_modules/bower/bin/bower:111:22)
    at emitOne (events.js:77:13)
    at Logger.emit (events.js:169:7)
    at Logger.emit (/root/Projects/OpenSourceProjects/Ambari/ambari-admin/src/main/resources/ui/admin-web/node_modules/bower/node_modules/bower-logger/lib/Logger.js:29:39)
    at /root/Projects/OpenSourceProjects/Ambari/ambari-admin/src/main/resources/ui/admin-web/node_modules/bower/lib/commands/index.js:40:20
    at _rejected (/root/Projects/OpenSourceProjects/Ambari/ambari-admin/src/main/resources/ui/admin-web/node_modules/bower/node_modules/q/q.js:797:24)
    at /root/Projects/OpenSourceProjects/Ambari/ambari-admin/src/main/resources/ui/admin-web/node_modules/bower/node_modules/q/q.js:823:30
    at Promise.when (/root/Projects/OpenSourceProjects/Ambari/ambari-admin/src/main/resources/ui/admin-web/node_modules/bower/node_modules/q/q.js:1035:31)
    at Promise.promise.promiseDispatch (/root/Projects/OpenSourceProjects/Ambari/ambari-admin/src/main/resources/ui/admin-web/node_modules/bower/node_modules/q/q.js:741:41)

...

[ERROR] Failed to execute goal org.codehaus.mojo:exec-maven-plugin:1.2.1:exec (Bower install) on project ambari-admin: Command execution failed. Process exited with an error: 1 (Exit value: 1) -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
[ERROR] 
[ERROR] After correcting the problems, you can resume the build with the command
[ERROR]   mvn <goals> -rf :ambari-admin
```

##A5:
```
修改./ambari-admin/pom.xml文件：
<argument>${basedir}/src/main/resources/ui/admin-web/node_modules/bower/bin/bower</argument>
--->
<argument>bower</argument>
```


##Q6:
```
[ERROR] Failed to execute goal on project ambari-metrics-storm-sink-legacy: Could not resolve dependencies for project org.apache.ambari:ambari-metrics-storm-sink-legacy:jar:2.7.3.0.0: Failed to collect dependencies at org.apache.storm:storm-core:jar:0.10.0.2.3.0.0-2557: Failed to read artifact descriptor for org.apache.storm:storm-core:jar:0.10.0.2.3.0.0-2557: Could not transfer artifact org.apache.storm:storm-core:pom:0.10.0.2.3.0.0-2557 from/to apache-hadoop (http://nexus-private.hortonworks.com/nexus/content/groups/public): Connect to nexus-private.hortonworks.com:80 [nexus-private.hortonworks.com/52.42.98.163] failed: timeout (Connection timed out) -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/DependencyResolutionException
[ERROR] 
[ERROR] After correcting the problems, you can resume the build with the command
[ERROR]   mvn <goals> -rf :ambari-metrics-storm-sink-legacy
```

##A6:
```
修改./ambari-metrics/pom.xml文件：
<url>http://nexus-private.hortonworks.com/nexus/content/groups/public</url>
--->
<url>https://repo.hortonworks.com/content/groups/public</url>
```


##Q7:
```
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (default) on project ambari-metrics-timelineservice: An Ant BuildException has occured: Can't get http://dev.hortonworks.com.s3.amazonaws.com/HDP/centos7/3.x/BUILDS/3.0.0.0-1634/tars/hbase/hbase-2.0.0.3.0.0.0-1634-bin.tar.gz to /root/Projects/OpenSourceProjects/Ambari/ambari-metrics/ambari-metrics-timelineservice/target/embedded/hbase.tar.gz
[ERROR] around Ant part ...<get usetimestamp="true" src="http://dev.hortonworks.com.s3.amazonaws.com/HDP/centos7/3.x/BUILDS/3.0.0.0-1634/tars/hbase/hbase-2.0.0.3.0.0.0-1634-bin.tar.gz" dest="/root/Projects/OpenSourceProjects/Ambari/ambari-metrics/ambari-metrics-timelineservice/target/embedded/hbase.tar.gz"/>... @ 5:298 in /root/Projects/OpenSourceProjects/Ambari/ambari-metrics/ambari-metrics-timelineservice/target/antrun/build-Download HBase.xml
[ERROR] -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
[ERROR] 
[ERROR] After correcting the problems, you can resume the build with the command
[ERROR]   mvn <goals> -rf :ambari-metrics-timelineservice
```

##A7:
```
配置
<hbase.tar>http://dev.hortonworks.com.s3.amazonaws.com/HDP/centos7/3.x/BUILDS/3.0.0.0-1634/tars/hbase/hbase-2.0.0.3.0.0.0-1634-bin.tar.gz</hbase.tar>
<hbase.folder>hbase-2.0.0.3.0.0.0-1634</hbase.folder>
<hadoop.tar>http://dev.hortonworks.com.s3.amazonaws.com/HDP/centos7/3.x/BUILDS/3.0.0.0-1634/tars/hadoop/hadoop-3.1.0.3.0.0.0-1634.tar.gz</hadoop.tar>
<hadoop.folder>hadoop-3.1.0.3.0.0.0-1634</hadoop.folder>
<grafana.folder>grafana-2.6.0</grafana.folder>
<grafana.tar>https://grafanarel.s3.amazonaws.com/builds/grafana-2.6.0.linux-x64.tar.gz</grafana.tar>
<phoenix.tar>http://dev.hortonworks.com.s3.amazonaws.com/HDP/centos7/3.x/BUILDS/3.0.0.0-1634/tars/phoenix/phoenix-5.0.0.3.0.0.0-1634.tar.gz</phoenix.tar>
<phoenix.folder>phoenix-5.0.0.3.0.0.0-1634</phoenix.folder>
--->
<hbase.tar>file:///root/Downloads/ApacheAmbariCompile/HDP/3.0.0.0-1634/hbase-2.0.0.3.0.0.0-1634-bin.tar.gz</hbase.tar>
<hbase.folder>hbase-2.0.0.3.0.0.0-1634</hbase.folder>
<hadoop.tar>file:///root/Downloads/ApacheAmbariCompile/HDP/3.0.0.0-1634/hadoop/hadoop-3.1.0.3.0.0.0-1634.tar.gz</hadoop.tar>
<hadoop.folder>hadoop-3.1.0.3.0.0.0-1634</hadoop.folder>
<grafana.folder>grafana-2.6.0</grafana.folder>
<grafana.tar>file:///root/Downloads/ApacheAmbariCompile/HDP/3.0.0.0-1634/grafana-2.6.0.linux-x64.tar.gz</grafana.tar>
<phoenix.tar>file:///root/Downloads/ApacheAmbariCompile/HDP/3.0.0.0-1634/phoenix-5.0.0.3.0.0.0-1634.tar.gz</phoenix.tar>
<phoenix.folder>phoenix-5.0.0.3.0.0.0-1634</phoenix.folder>
```


##Q8:
```
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (default) on project ambari-infra-solr-client: An Ant BuildException has occured: The following error occurred while executing this line:
[ERROR] /home/huangpengbo/Ambari/ambari-infra/ambari-infra-solr-client/build.xml:39: Can't get http://central.maven.org/maven2/org/apache/lucene/lucene-core/6.6.2/lucene-core-6.6.2.jar to /home/huangpengbo/Ambari/ambari-infra/ambari-infra-solr-client/target/migrate/lucene-core-6.6.2.jar
[ERROR] around Ant part ...<ant antfile="build.xml">... @ 4:28 in /home/huangpengbo/Ambari/ambari-infra/ambari-infra-solr-client/target/antrun/build-main.xml
[ERROR] -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
[ERROR] 
[ERROR] After correcting the problems, you can resume the build with the command
[ERROR]   mvn <goals> -rf :ambari-infra-solr-client
```

##A8:
```
使用自主下载方式下载该版本npm到~/.m2/repository/com/github/eirslett/npm/2.15.0目录。
<lucene6.version>6.6.2</lucene6.version>
<lucene6-core-jar.name>lucene-core-${lucene6.version}.jar</lucene6-core-jar.name>
<lucene6-core.url>http://central.maven.org/maven2/org/apache/lucene/lucene-core/${lucene6.version}/${lucene6-core-jar.name}</lucene6-core.url>
<lucene6-backward-codecs-jar.name>lucene-backward-codecs-${lucene6.version}.jar</lucene6-backward-codecs-jar.name>
<lucene6-backward-codecs.url>http://central.maven.org/maven2/org/apache/lucene/lucene-backward-codecs/${lucene6.version}/${lucene6-backward-codecs-jar.name}</lucene6-backward-codecs.url>
--->
<lucene6.version>6.6.2</lucene6.version>
<lucene6-core-jar.name>lucene-core-${lucene6.version}.jar</lucene6-core-jar.name>
<lucene6-core.url>file:///root/Downloads/ApacheAmbariCompile/HDP/3.0.0.0-1634/jars/${lucene6-core-jar.name}</lucene6-core.url>
<lucene6-backward-codecs-jar.name>lucene-backward-codecs-${lucene6.version}.jar</lucene6-backward-codecs-jar.name>
<lucene6-backward-codecs.url>file:///root/Downloads/ApacheAmbariCompile/HDP/3.0.0.0-1634/jars/${lucene6-backward-codecs-jar.name}</lucene6-backward-codecs.url>
```

