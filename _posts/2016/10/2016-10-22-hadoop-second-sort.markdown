---
layout: "post"
title: "hadoop二次排序"
date: "2016-10-22 08:21"
category: "hadoop"
tags: [大数据]
---

####map/reduce简单介绍  

正常的map/reduce程序而言，在map阶段会根据key自动进行排序，因此key必须会实现CompareWritable接口，即要能排序又要能够序列化，因此为了测试map的输出能够按照key自动进行排序，下面简单的例子是将文本中两列文本按照第一列进行排序，这里权且叫做一次排序。

hdfs上/user/mac/mapreduce/sort/input/input.txt文件中的内容信息为：

```txt
hello world
word hello
li xiaojiao
angle baby
good study
china shanghai
safari chrome
hello li
hello xiao
hello xiao
good study
hello jiao
china shandong
china qingdao
```

####一次排序实现逻辑  

1.编写mapper类

该类是将上面的文本由hadoop自己内部实现的机制(LineRecordReader)实现的将文件按照行为单位解析为<key,value>-><偏移量，每行内容>的结构作为map阶段的输入，比如上面的保存在/user/mac/mapreduce/sort/input/input.txt会被解析为：

```txt
0---->hello world
12---->word hello
23---->li xiaojiao
35---->angle baby
46---->good study
57---->china shanghai
72---->safari chrome
86---->hello li
95---->hello xiao
106---->hello xiao
117---->good study
128---->hello jiao
139---->china shandong
154---->china qingdao
```

因此map阶段的工作需要将上面的结果转变为正常的/user/mac/mapreduce/sort/input/input.txt内容输出
因此map阶段的输出应该为：  

```txt
hello world
word hello
li xiaojiao
angle baby
good study
china shanghai
safari chrome
hello li
hello xiao
hello xiao
good study
hello jiao
china shandong
china qingdao
```

因此这里map阶段的逻辑代码可以按照下面实现  

```java
package mac.cn.SecondSort.before;

import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

import java.io.IOException;

/**
 * 排序mapper
 *
 * @author lixiaojiao
 * @create 2016-10-22 09:52
 * @email lixiaojiao_hit@163.com
 * @QQ 2305886442
 * @graduate 哈尔滨工业大学
 * @address shanghai china
 * @blog https://CodeToSurvive1.github.io
 * @github https://github.com/CodeToSurvive1
 */
public class SortMapper extends Mapper<LongWritable, Text, Text, Text> {

    @Override
    protected void setup(Context context) throws IOException, InterruptedException {
        System.out.println("----mapper---start");
    }

    /**
     * 输入为1->key    value
     * 35->key    value
     * 转换为key1 value1
     * key2  value2
     *
     * @param key
     * @param value
     * @param context
     * @throws IOException
     * @throws InterruptedException
     */
    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {

        System.out.println(key.toString() + "---->" + value.toString());
        String[] values = value.toString().split(" ");
        String valueKey = values[0];
        String valueValue = values[1];

        context.write(new Text(valueKey), new Text(valueValue));
    }


    @Override
    protected void cleanup(Context context) throws IOException, InterruptedException {
        System.out.println("----mapper---end");
    }
}


2.编写reducer类  

由于在map阶段和reduce阶段中存在shuffle阶段，而shuffle阶段中会包含partition分区，sort排序，combine合并等操作，因此到了reduce阶段后的数据会按照key进行了相关的合并操作，以便节省网络开销及本地磁盘的开销，因此到了reduce阶段后的数据也就变为了下面的格式:

```txt  
angle------baby,
china------shandong,qingdao,shanghai,
good------study,study,
hello------world,jiao,xiao,xiao,li,
li------xiaojiao,
safari------chrome,
word------hello,
```

这里可以看出key已经按照字母顺序进行了排序，因此reduce阶段的输出只需要再重新将上面的数据解析为key，value的格式即可。
因此这里reducer阶段的逻辑代码可以按照下面实现   

```java
package mac.cn.SecondSort.before;

import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

import java.io.IOException;

/**
 * sortReducer
 *
 * @author lixiaojiao
 * @create 2016-10-22 09:55
 * @email lixiaojiao_hit@163.com
 * @QQ 2305886442
 * @graduate 哈尔滨工业大学
 * @address shanghai china
 * @blog https://CodeToSurvive1.github.io
 * @github https://github.com/CodeToSurvive1
 */
public class SortReducer extends Reducer<Text, Text, Text, Text> {

    @Override
    protected void setup(Context context) throws IOException, InterruptedException {

        System.out.println("----reduce---start");
    }

    /**
     * <key,value,value>
     * <key1,value1,value1>
     * 转换为
     * key value
     * key value
     * key1 value1
     * key1 value1
     *
     * @param key
     * @param values
     * @param context
     * @throws IOException
     * @throws InterruptedException
     */
    @Override
    protected void reduce(Text key, Iterable<Text> values, Context context) throws IOException, InterruptedException {

        StringBuilder valueStr = new StringBuilder();
        for (Text value : values) {
            valueStr.append(value).append(",");
            context.write(key, value);
        }

        System.out.println(key.toString() + "------" + valueStr);
    }

    @Override
    protected void cleanup(Context context) throws IOException, InterruptedException {
        System.out.println("----reduce---end");
    }
}

```

3.编写驱动类进行测试

```java
package mac.cn.SecondSort.before;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

/**
 * sort启动类
 *
 * @author lixiaojiao
 * @create 2016-10-22 09:59
 * @email lixiaojiao_hit@163.com
 * @QQ 2305886442
 * @graduate 哈尔滨工业大学
 * @address shanghai china
 * @blog https://CodeToSurvive1.github.io
 * @github https://github.com/CodeToSurvive1
 */
public class SortRunner implements Tool {

    private Configuration conf;

    public int run(String[] args) throws Exception {


        Configuration conf = this.getConf();

        Job job = Job.getInstance(conf, "sort test");


        job.setMapperClass(SortMapper.class);
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(Text.class);

        job.setReducerClass(SortReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);

        FileInputFormat.addInputPath(job, new Path("/user/mac/mapreduce/sort/input"));
        FileOutputFormat.setOutputPath(job, new Path("/user/mac/mapreduce/sort/output"));

        return job.waitForCompletion(true) ? 0 : 1;
    }

    public void setConf(Configuration conf) {
        this.conf = conf;
    }

    public Configuration getConf() {
        return conf;
    }


    public static void main(String args[]) throws Exception {

        Configuration config = new Configuration();
        config.set("fs.defaultFS", "hdfs://mac.cn:8020");

        ToolRunner.run(config, new SortRunner(), args);
    }

}

```

4.准备数据进行测试  


```xml  
//新建sort目录
./bin/hdfs dfs -mkdir /user/mac/mapreduce/sort/input
//创建输入文件
./bin/hdfs dfs -touchz /user/mac/mapreduce/sort/input/input.txt
//在输入文件中追加文本信息
./bin/hdfs dfs -appendToFile - /user/mac/mapreduce/sort/input/input.txt

hello world
word hello
li xiaojiao
angle baby
good study
china shanghai
safari chrome
hello li
hello xiao
hello xiao
good study
hello jiao
china shandong
china qingdao

```

5.运行SortRunner类进行测试

测试结果为

```xml  
mac:hadoop-2.5.0-cdh5.3.6 mac$ ./bin/hdfs dfs -cat /user/mac/mapreduce/sort/output/*
16/10/22 11:28:09 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
hello world
word hello
li xiaojiao
angle baby
good study
china shanghai
safari chrome
hello li
hello xiao
hello xiao
good study
hello jiao
china shandong
china qingdao
```

6.总结：

hadoop中的map/reduce的map阶段确实经过了排序的操作，而且是按照key进行排序，但是value是没有经过排序的。
上面一次排序源代码连接：[一次排序源代码](https://github.com/CodeToSurvive1/bigdataproject/tree/master/src/main/java/mac/cn/SecondSort/before)


####二次排序  

从上面的一次排序中可以看出，在map阶段会把key按照排序规则进行排序，因此，如果我想对上面的原始数据进行二次排序呢，也就是说在一次排序把第一列进行排序的基础上，也要求把第二列同样按照排序的规则进行排序，这里该如何做呢？
其实很简单，就是将每行数据拆分成多个字段，将这多个字段定义为map的一个key，也就是自定义key数据类型，该类型中包含要排序的这几列而已。  

1.自定义数据类型，封装该行记录  

```java
package mac.cn.SecondSort.after;

import org.apache.hadoop.io.WritableComparable;

import java.io.DataInput;
import java.io.DataOutput;
import java.io.IOException;

/**
 * 二次排序中自定义数据类型
 *
 * @author lixiaojiao
 * @create 2016-10-22 11:55
 * @email lixiaojiao_hit@163.com
 * @QQ 2305886442
 * @graduate 哈尔滨工业大学
 * @address shanghai china
 * @blog https://CodeToSurvive1.github.io
 * @github https://github.com/CodeToSurvive1
 */
public class CustomDatatype implements WritableComparable<CustomDatatype> {

    private String columnA;

    private String columnB;

    public int compareTo(CustomDatatype o) {
        int result = this.getColumnA().compareTo(o.getColumnA());
        if (result == 0) {
            return this.getColumnB().compareTo(o.getColumnB());
        }
        return result;
    }

    public void write(DataOutput out) throws IOException {
        out.writeUTF(columnA);
        out.writeUTF(columnB);
    }

    public void readFields(DataInput in) throws IOException {
        this.columnA = in.readUTF();
        this.columnB = in.readUTF();
    }

    @Override
    public String toString() {
        return this.columnA+" "+this.columnB;
    }

    public String getColumnA() {
        return columnA;
    }

    public void setColumnA(String columnA) {
        this.columnA = columnA;
    }

    public String getColumnB() {
        return columnB;
    }

    public void setColumnB(String columnB) {
        this.columnB = columnB;
    }
}

```

2.自定义mapper类，mapper的输出的key设置为自定义数据类型CustomDatatype即可，而输出类型直接设置为空NullWritable就可以了，这样mapper输出到reducer阶段的时候会自动的进行分区，排序，合并等操作。  

```java
package mac.cn.SecondSort.after;

import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

import java.io.IOException;

/**
 * 二次排序mapper类
 *
 * @author lixiaojiao
 * @create 2016-10-22 12:04
 * @email lixiaojiao_hit@163.com
 * @QQ 2305886442
 * @graduate 哈尔滨工业大学
 * @address shanghai china
 * @blog https://CodeToSurvive1.github.io
 * @github https://github.com/CodeToSurvive1
 */
public class SecondSortMapper extends Mapper<LongWritable, Text, CustomDatatype, NullWritable> {

    private CustomDatatype customDatatype = new CustomDatatype();

    @Override
    protected void setup(Context context) throws IOException, InterruptedException {
        System.out.println("-----mapper-----start");
    }

    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {

        String[] valueLine = value.toString().split(" ");
        String columnA = valueLine[0];
        String columnB = valueLine[1];

        customDatatype.setColumnA(columnA);
        customDatatype.setColumnB(columnB);
        context.write(customDatatype,NullWritable.get());
    }

    @Override
    protected void cleanup(Context context) throws IOException, InterruptedException {
        System.out.println("-----mapper-----end");
    }
}

```

3.自定义reducer类，该类的输出key也是CustomDatatype，value为空  

```java
package mac.cn.SecondSort.after;

import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.mapreduce.Reducer;

import java.io.IOException;

/**
 * 二次排序reducer类
 *
 * @author lixiaojiao
 * @create 2016-10-22 12:19
 * @email lixiaojiao_hit@163.com
 * @QQ 2305886442
 * @graduate 哈尔滨工业大学
 * @address shanghai china
 * @blog https://CodeToSurvive1.github.io
 * @github https://github.com/CodeToSurvive1
 */
public class SecondSortReducer extends Reducer<CustomDatatype, NullWritable, CustomDatatype, NullWritable> {
    @Override
    protected void setup(Context context) throws IOException, InterruptedException {
        System.out.println("-----mapper----start");
    }

    /**
     * 按照注释中的实现逻辑会遗漏掉行记录相同的数据,比如有三行数据相同,那么结果就合并为了一行
     *
     * @param key
     * @param values
     * @param context
     * @throws IOException
     * @throws InterruptedException
     */
    @Override
    protected void reduce(CustomDatatype key, Iterable<NullWritable> values, Context context) throws IOException, InterruptedException {
        // context.write(key,NullWritable.get());

        for (NullWritable value : values) {
            context.write(key, NullWritable.get());
        }
    }

    @Override
    protected void cleanup(Context context) throws IOException, InterruptedException {
        System.out.println("-----mapper----end");
    }
}

```

4.编写启动类

```java
package mac.cn.SecondSort.after;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

/**
 * 二次排序启动类或者驱动类
 *
 * @author lixiaojiao
 * @create 2016-10-22 12:36
 * @email lixiaojiao_hit@163.com
 * @QQ 2305886442
 * @graduate 哈尔滨工业大学
 * @address shanghai china
 * @blog https://CodeToSurvive1.github.io
 * @github https://github.com/CodeToSurvive1
 */
public class SecondSortRunner implements Tool {

    private Configuration config;

    public int run(String[] args) throws Exception {

        Configuration conf = this.getConf();

        Job job = Job.getInstance(conf,"second sort");


        job.setMapperClass(SecondSortMapper.class);
        job.setMapOutputKeyClass(CustomDatatype.class);
        job.setMapOutputValueClass(NullWritable.class);

        job.setReducerClass(SecondSortReducer.class);
        job.setOutputKeyClass(CustomDatatype.class);
        job.setOutputValueClass(NullWritable.class);

        FileInputFormat.addInputPath(job,new Path("/user/mac/mapreduce/sort/input"));
        FileOutputFormat.setOutputPath(job,new Path("/user/mac/mapreduce/sort/secondout"));

        return job.waitForCompletion(true)?0:1;
    }

    public void setConf(Configuration conf) {
        this.config = conf;
    }

    public Configuration getConf() {
        return config;
    }

    public static void main(String args[]) throws Exception {
        Configuration config = new Configuration();
        config.set("fs.defaultFS", "hdfs://mac.cn:8020");
        int exit = ToolRunner.run(config, new SecondSortRunner(),args);
        System.exit(exit);
    }


}

```

5.运行后查看输出结果

```xml
mac:hadoop-2.5.0-cdh5.3.6 mac$ ./bin/hdfs dfs -cat /user/mac/mapreduce/sort/secondout/*
16/10/22 12:55:12 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
angle baby
china qingdao
china shandong
china shanghai
good study
good study
hello jiao
hello li
hello world
hello xiao
hello xiao
li xiaojiao
safari chrome
word hello
```

6.总结  

注意reduce阶段的输出，要根据nullwritable的个数循环输出，不然会漏掉记录相同的数据。
源代码为：
[二次排序元源代码](https://github.com/CodeToSurvive1/bigdataproject/tree/master/src/main/java/mac/cn/SecondSort/after)
