---
layout: "post"
title: "自定义InputFormat"
date: "2016-10-15 12:47"
category: "hadoop"
tags: [大数据]
---


#### 数据准备
执行下面sql语句	

```sql
use hadoop
db.persons.insert({name:"zhangsan",age:15,sex:"M"})
db.persons.insert({name:"lisi",sex:"M"})
db.persons.insert({name:"wangwu",age:18})
db.persons.insert({name:"xiaoer",age:18,sex:"M"})
db.persons.insert({name:"tom",age:17})
db.persons.insert({name:"Helen",age:15,sex:"F"})
db.persons.insert({name:"Judy",age:15,sex:"F"})

db.persons.find().toArray()
[
	{
		"_id" : ObjectId("5801b5bcaed1f2ef17d38f47"),
		"name" : "zhangsan",
		"age" : 15,
		"sex" : "M"
	},
	{
		"_id" : ObjectId("5801b62aaed1f2ef17d38f48"),
		"name" : "lisi",
		"sex" : "M"
	},
	{
		"_id" : ObjectId("5801b62aaed1f2ef17d38f49"),
		"name" : "wangwu",
		"age" : 18
	},
	{
		"_id" : ObjectId("5801b62aaed1f2ef17d38f4a"),
		"name" : "xiaoer",
		"age" : 18,
		"sex" : "M"
	},
	{
		"_id" : ObjectId("5801b62aaed1f2ef17d38f4b"),
		"name" : "tom",
		"age" : 17
	},
	{
		"_id" : ObjectId("5801b62aaed1f2ef17d38f4c"),
		"name" : "Helen",
		"age" : 15,
		"sex" : "F"
	},
	{
		"_id" : ObjectId("5801b62caed1f2ef17d38f4d"),
		"name" : "Judy",
		"age" : 15,
		"sex" : "F"
	}
]
>	

```	
------

#### 业务逻辑	

本例子是在mongodb数据库中存在person表，该表中存在三个字段，分别为名字，年龄，性别。通过编写map/reduce来实现读取数据库中的数据，然后通过map/reduce计算没个年龄的人数，并将结果输出到hdfs文件系统中。

------

#### 实现逻辑	

由于从mongodb中读取person表中的数据，因此对于map阶段的输入来说，输入的key应该为行号，value应该为表中的一行记录。而reduce阶段的输出key应该为年龄，value为数量

------

#### 代码逻辑	

自定义InputFormat要点：	

1.继承InputFormat类并进行相关实现		
InputFormat需要实现两个方法：		

方法名称|作用	
|----|---|	
InputSplit[] getSplits(JobConf job, int numSplits);|定义了分片信息如何获取
RecordReader<K, V> getRecordReader(InputSplit split,JobConf job,Reporter reporter);|定义了如何读取记录数据	
2.继承RecordReader类并进行相关实现	
RecordReader需要实现两个方法:	
	
方法名称|作用	
|----|---|
public abstract void initialize(InputSplit split, TaskAttemptContext context );|对split初始化
public abstract boolean nextKeyValue();|判断是否还有数据
public abstract KEYIN getCurrentKey();|获取当前数据的key
public abstract VALUEIN getCurrentValue();|获取当前数据的value                        
public abstract float getProgress()|获取当前执行的进度
public abstract void close()|释放资源

------

#### 代码编写

1. 在pom.xml文件中添加mongod的依赖包	

```xml
<dependency>
	<groupId>org.mongodb</groupId>
	<artifactId>mongo-java-driver</artifactId>
	<version>3.2.1</version>
</dependency>
```
2.根据数据库表person自定义数据类型		

```java
package mac.cn.CustomInputFormat;

import org.apache.hadoop.io.Writable;

import java.io.DataInput;
import java.io.DataOutput;
import java.io.IOException;

/**
 * 自定义MongoDB数据类型
 *
 * @author lixiaojiao
 * @create 2016-10-17 20:55
 * @email lixiaojiao_hit@163.com
 * @QQ 2305886442
 * @graduate 哈尔滨工业大学
 * @address shanghai china
 * @blog https://CodeToSurvive1.github.io
 * @github https://github.com/CodeToSurvive1
 */
public class MongoDBWritable implements Writable {

    private String name;

    private Integer age;

    public void write(DataOutput out) throws IOException {
        out.writeUTF(name);
        //age为空时不序列化
        if (age == null) {
            out.writeBoolean(false);
        } else {
            out.writeBoolean(true);
            out.writeInt(age);
        }
    }

    public void readFields(DataInput in) throws IOException {
        this.name = in.readUTF();
        if (in.readBoolean()) {
            this.age = in.readInt();
        }
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}


```

3.自定义InputSplit分片信息	

```java
package mac.cn.CustomInputFormat;

import org.apache.hadoop.io.Writable;
import org.apache.hadoop.mapreduce.InputSplit;

import java.io.DataInput;
import java.io.DataOutput;
import java.io.IOException;

/**
 * 自定义mongodb分片
 *
 * @author lixiaojiao
 * @create 2016-10-17 21:03
 * @email lixiaojiao_hit@163.com
 * @QQ 2305886442
 * @graduate 哈尔滨工业大学
 * @address shanghai china
 * @blog https://CodeToSurvive1.github.io
 * @github https://github.com/CodeToSurvive1
 */
public class MongoDBInputSpilt extends InputSplit implements Writable {

    //定义分片起始位置
    private Long start;

    //定义分片终止位置
    private Long end;

    public MongoDBInputSpilt() {
        super();
    }

    public MongoDBInputSpilt(Long start, Long end) {
        super();
        this.start = start;
        this.end = end;
    }

    public long getLength() throws IOException, InterruptedException {
        return end - start;
    }

    public String[] getLocations() throws IOException, InterruptedException {
        return new String[0];
    }

    public void write(DataOutput out) throws IOException {
        out.writeLong(start);
        out.writeLong(end);
    }

    public void readFields(DataInput in) throws IOException {
        this.start = in.readLong();
        this.end = in.readLong();
    }

    public Long getStart() {
        return start;
    }

    public void setStart(Long start) {
        this.start = start;
    }

    public Long getEnd() {
        return end;
    }

    public void setEnd(Long end) {
        this.end = end;
    }
}



```	
4.自定义RecordReader	

```java
package mac.cn.CustomInputFormat;

import com.mongodb.MongoClient;
import com.mongodb.MongoClientOptions;
import com.mongodb.MongoClientURI;
import com.mongodb.client.FindIterable;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoCursor;
import com.mongodb.client.MongoDatabase;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.mapreduce.InputSplit;
import org.apache.hadoop.mapreduce.RecordReader;
import org.apache.hadoop.mapreduce.TaskAttemptContext;
import org.bson.Document;

import java.io.IOException;

/**
 * 自定义MongoDB的RecordReader封装类
 *
 * @author lixiaojiao
 * @create 2016-10-17 21:24
 * @email lixiaojiao_hit@163.com
 * @QQ 2305886442
 * @graduate 哈尔滨工业大学
 * @address shanghai china
 * @blog https://CodeToSurvive1.github.io
 * @github https://github.com/CodeToSurvive1
 */
public class MongoDBRecordReader<V extends MongoDBWritable> extends RecordReader<LongWritable, V> {

    private MongoDBInputSpilt inputSpilt;

    private Configuration config;

    private int index = 0;

    private MongoCursor<Document> documentIterator;

    private LongWritable key;

    private V value;

    public MongoDBRecordReader() {
        super();
    }

    public MongoDBRecordReader(InputSplit inputSpilt, TaskAttemptContext context) throws IOException, InterruptedException {
        this.initialize(inputSpilt, context);
    }

    public void initialize(InputSplit split, TaskAttemptContext context) throws IOException, InterruptedException {
        this.inputSpilt = (MongoDBInputSpilt) split;
        config = context.getConfiguration();

        this.key = new LongWritable();
        this.value = (V) new MongoDBWritable();
    }

    public boolean nextKeyValue() throws IOException, InterruptedException {

        if (documentIterator == null) {
            //定义url
            MongoClientURI uri = new MongoClientURI("mongodb://127.0.0.1:27017/hadoop",
                    MongoClientOptions.builder().cursorFinalizerEnabled(false));

            //获取连接
            MongoClient client = new MongoClient(uri);

            //获取数据库
            MongoDatabase hadoopDb = client.getDatabase("hadoop");

            //获取集合persons
            MongoCollection<Document> personsCollection = hadoopDb.getCollection("persons");

            FindIterable<Document> result = personsCollection.find().skip(this.inputSpilt.getStart().intValue()).limit((int) this.inputSpilt.getLength());

            documentIterator = result.iterator();
        }

        if (documentIterator.hasNext()) {
            Document doc = documentIterator.next();

            this.key = new LongWritable(this.inputSpilt.getStart() + index);

            value.setName(doc.get("name").toString());

            if (doc.get("age") != null) {
                value.setAge(Double.valueOf(doc.get("age").toString()).intValue());
            } else {
                value.setAge(null);
            }

            index++;

            return true;
        }

        return false;
    }

    public LongWritable getCurrentKey() throws IOException, InterruptedException {
        return key;
    }

    public V getCurrentValue() throws IOException, InterruptedException {
        return value;
    }

    public float getProgress() throws IOException, InterruptedException {
        return 0;
    }

    public void close() throws IOException {
        documentIterator.close();
    }
}


```	

5.自定义InputFormat	

```java
package mac.cn.CustomInputFormat;

import com.mongodb.MongoClient;
import com.mongodb.MongoClientOptions;
import com.mongodb.MongoClientURI;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.mapreduce.*;
import org.bson.Document;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

/**
 * 自定义input format输入格式化器
 *
 * @author lixiaojiao
 * @create 2016-10-17 21:00
 * @email lixiaojiao_hit@163.com
 * @QQ 2305886442
 * @graduate 哈尔滨工业大学
 * @address shanghai china
 * @blog https://CodeToSurvive1.github.io
 * @github https://github.com/CodeToSurvive1
 */
public class MongoDBInputFormat<V extends MongoDBWritable> extends InputFormat<LongWritable, V> {

    public List<InputSplit> getSplits(JobContext context) throws IOException, InterruptedException {

        //定义url
        MongoClientURI uri = new MongoClientURI("mongodb://127.0.0.1:27017/hadoop",
                MongoClientOptions.builder().cursorFinalizerEnabled(false));

        //获取连接
        MongoClient client = new MongoClient(uri);

        //获取数据库
        MongoDatabase hadoopDb = client.getDatabase("hadoop");

        //获取集合persons
        MongoCollection<Document> personsCollection = hadoopDb.getCollection("persons");

        //获取总记录数
        long count = personsCollection.count();

        //定义分片的大小为2
        long chunkSize = 2;

        //计算分片数量
        long mappers = count / chunkSize;

        List<InputSplit> splits = new ArrayList<InputSplit>();

        //组装所有的分片位置信息
        for (int i = 0; i < mappers; i++) {
            if (i + 1 == mappers) {
                //如果最后一组数据不恰巧可分,则最后的数据全部放在最后一个mapper中,比如七个数据,最后一组为5,6,7
                splits.add(new MongoDBInputSpilt(i * chunkSize, count));
            } else {
                splits.add(new MongoDBInputSpilt(i * chunkSize, i * chunkSize + chunkSize));
            }
        }

        return splits;
    }

    public RecordReader createRecordReader(InputSplit split, TaskAttemptContext context) throws IOException, InterruptedException {
        return new MongoDBRecordReader(split, context);
    }
}

```		

6.编写测试类	

```java
package mac.cn.CustomInputFormat;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

import java.io.IOException;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;
import java.util.Set;

/**
 * MongoDBInputFormat启动类
 *
 * @author lixiaojiao
 * @create 2016-10-17 21:59
 * @email lixiaojiao_hit@163.com
 * @QQ 2305886442
 * @graduate 哈尔滨工业大学
 * @address shanghai china
 * @blog https://CodeToSurvive1.github.io
 * @github https://github.com/CodeToSurvive1
 */
public class MongoDBInputFormatRunner {

    static class MongoDBMapper extends Mapper<LongWritable, MongoDBWritable, LongWritable, MongoDBWritable> {
        @Override
        protected void map(LongWritable key, MongoDBWritable value, Context context) throws IOException, InterruptedException {
            if(value.getAge()==null){
                return;
            }
            context.write(new LongWritable(value.getAge()), value);
        }
    }

    static class MongoDBReducer extends Reducer<LongWritable, MongoDBWritable, IntWritable, IntWritable> {

        @Override
        protected void reduce(LongWritable key, Iterable<MongoDBWritable> values, Context context) throws IOException, InterruptedException {

            Map<String,Integer> ages = new HashMap<String, Integer>();

            for(MongoDBWritable value : values){
                if(ages.containsKey(value.getAge().toString())){
                    Integer number = ages.get(value.getAge().toString());
                    number++;
                    ages.put(value.getAge().toString(),number);
                }else{
                    ages.put(value.getAge().toString(),1);
                }
            }

            Set<Map.Entry<String, Integer>> entries = ages.entrySet();

            Iterator<Map.Entry<String, Integer>> iterator = entries.iterator();
            while(iterator.hasNext()){
                Map.Entry<String, Integer> next = iterator.next();
                context.write(new IntWritable(Integer.parseInt(next.getKey())),new IntWritable(next.getValue()));
            }

        }
    }

    public static void main(String args[]) throws IOException, ClassNotFoundException, InterruptedException {

        Configuration config = new Configuration();
        config.set("fs.defaultFS","hdfs://mac.cn:8020");


        Job job = Job.getInstance(config, "MongoDb input format");

        job.setInputFormatClass(MongoDBInputFormat.class);

        job.setMapperClass(MongoDBMapper.class);
        job.setMapOutputKeyClass(LongWritable.class);
        job.setMapOutputValueClass(MongoDBWritable.class);


        job.setReducerClass(MongoDBReducer.class);
        job.setOutputKeyClass(IntWritable.class);
        job.setOutputValueClass(IntWritable.class);

        FileOutputFormat.setOutputPath(job,new Path("/user/mac/mapreduce/output3"));

        System.exit(job.waitForCompletion(true)?0:1);
    }

}
```	

------

#### 代码测试	

1. 启动mongodb服务端
2. 运行runner测试类
3. 查看hdfs中的数据
4. ./bin/hdfs dfs -cat /user/mac/mapreduce/output3/*

年龄|数量
---|----
15 | 3	
17 | 1	
18 | 2



代码地址: [https://github.com/CodeToSurvive1/bigdataproject/tree/master/src/main/java/mac/cn/CustomInputFormat](https://github.com/CodeToSurvive1/bigdataproject/tree/master/src/main/java/mac/cn/CustomInputFormat)
