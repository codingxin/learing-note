

## JVM常用启动配置

 java -Xmx1024M -XX:+PrintGCDetails GCLogAnalysis

java -XX:+UseSerialGC -Xms512m -Xmx512m -Xloggc:gc.demo.log -XX:+PrintGCDetails -XX:+PrintGCDateStamps GCLogAnalysis

java -XX:+UseConcMarkSweepGC  -Xms512m -Xmx512m  -Xloggc:gc.demo.log  -XX:+PrintGCDetails -XX:+PrintGCDateStamps GCLogAnalysis

java -XX:+UseG1GC -Xms512m -Xmx512m  --XX:+PrintGC  -XX:+PrintGCDateStamps GCLogAnalysis



1.查看当前gc

jps -lvm

jmap -heap

1H50Min

2H48min

3H28min