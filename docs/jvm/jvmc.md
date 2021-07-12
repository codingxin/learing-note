

## JVM常用启动配置

 java -Xmx1024M -XX:+PrintGCDetails GCLogAnalysis

java -XX:+UseSerialGC -Xms512m -Xmx512m -Xloggc:gc.demo.log -XX:+PrintGCDetails -XX:+PrintGCDateStamps GCLogAnalysis

java -XX:+UseConcMarkSweepGC  -Xms512m -Xmx512m  -Xloggc:gc.demo.log  -XX:+PrintGCDetails -XX:+PrintGCDateStamps GCLogAnalysis

java -XX:+UseG1GC -Xms512m -Xmx512m  --XX:+PrintGC  -XX:+PrintGCDateStamps GCLogAnalysis

