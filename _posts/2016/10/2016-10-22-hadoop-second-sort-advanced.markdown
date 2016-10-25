---
layout: "post"
title: "hadoop二次排序(高级)"
date: "2016-10-22 18:21"
category: "hadoop"
tags: [大数据]
---

#### hadoop二次排序问题

从[hadoop二次排序中](https://codetosurvive1.github.io/posts/hadoop-second-sort.html)可以看出，虽然通过设计Reducer<CustomDatatype, NullWritable, CustomDatatype, NullWritable>这样的输出可以实现，但是发现reduce的输入key为CustomDatatype，也就是说只有map的输入的两列数据完全相同的时候才会进行合并，这样效率是很低的。因为数据到了reduce阶段会首先按照定义的group的规则进行分组，这里没有设置规则，因此按照默认的规则进行分组，只有相同的数据分组后进入reduce，重复该操作，但是两列重复的概率会有多大呢？这完全取决于数据本身。


#### map阶段的排序逻辑规则


map阶段的排序规则如下：

1.首先查看job是否有设置过setSortComparatorClass,如果有设置，则直接根据该类的实现规则。

2.如果1中没有设置该属性，那么会去自定义的key中查找是否注册过compartor类，如果注册过则使用。

3.如果2中也没有进行注册，则直接使用key中的comparable接口。


下面是改良后的二次排序的代码：

```java

package mac.cn.SecondSort.advanced;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.*;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

import java.io.DataInput;
import java.io.DataOutput;
import java.io.IOException;
import java.util.StringTokenizer;

/**
 * 二次排序启动类或者驱动类
 * http://www.superwu.cn/2013/08/18/492/
 * http://blog.csdn.net/w1014074794/article/details/51821727
 * https://www.iteblog.com/archives/1415
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
public class AdvancedSecondSortRunner {

    /**
     * 把第一列整数和第二列作为类的属性，并且实现WritableComparable接口
     */
    public static class IntPair implements WritableComparable<IntPair> {
        private int first = 0;
        private int second = 0;

        public void set(int left, int right) {
            first = left;
            second = right;
        }

        public int getFirst() {
            return first;
        }

        public int getSecond() {
            return second;
        }

        public void readFields(DataInput in) throws IOException {
            first = in.readInt();
            second = in.readInt();
        }

        public void write(DataOutput out) throws IOException {
            out.writeInt(first);
            out.writeInt(second);
        }

        @Override
        public int hashCode() {
            return first + "".hashCode() + second + "".hashCode();
        }

        @Override
        public boolean equals(Object right) {
            if (right instanceof IntPair) {
                IntPair r = (IntPair) right;
                return r.first == first && r.second == second;
            } else {
                return false;
            }
        }

        //这里的代码是关键，因为对key排序时，调用的就是这个compareTo方法
        public int compareTo(IntPair o) {
            if (first != o.first) {
                return first - o.first;
            } else if (second != o.second) {
                return second - o.second;
            } else {
                return 0;
            }
        }


        /** A Comparator that compares serialized IntPair. */
        public static class Comparator extends WritableComparator {
            public Comparator() {
                super(IntPair.class);
            }

            public int compare(byte[] b1, int s1, int l1,
                               byte[] b2, int s2, int l2) {
                return compareBytes(b1, s1, l1, b2, s2, l2);
            }
        }

        static {
            WritableComparator.define(IntPair.class, new Comparator());
        }

    }


    public static class SortComparator extends WritableComparator{


        public SortComparator(){
            super(IntPair.class, true);
        }

        @Override
        public int compare(WritableComparable a, WritableComparable b) {
            IntPair o1 = (IntPair) a;
            IntPair o2 = (IntPair) b;

            if (o1.first != o2.first) {
                return o1.first - o2.first;
            } else {
                return o1.second - o2.second;
            }
        }
    }


    /**
     * 在分组比较的时候，只比较原来的key，而不是组合key。
     */
    public static class GroupingComparator implements RawComparator<IntPair> {

        public int compare(byte[] b1, int s1, int l1, byte[] b2, int s2, int l2) {
            return WritableComparator.compareBytes(b1, s1, Integer.SIZE / 8, b2, s2, Integer.SIZE / 8);
        }

        public int compare(IntPair o1, IntPair o2) {
            int first1 = o1.getFirst();
            int first2 = o2.getFirst();
            return first1 - first2;
        }

    }


    public static class MapClass extends Mapper<LongWritable, Text, IntPair, IntWritable> {

        private final IntPair key = new IntPair();
        private final IntWritable value = new IntWritable();

        @Override
        public void map(LongWritable inKey, Text inValue,
                        Context context) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(inValue.toString());
            int left = 0;
            int right = 0;
            if (itr.hasMoreTokens()) {
                left = Integer.parseInt(itr.nextToken());
                if (itr.hasMoreTokens()) {
                    right = Integer.parseInt(itr.nextToken());
                }
                key.set(left, right);
                value.set(right);
                context.write(key, value);
            }
        }
    }

    public static class Reduce extends Reducer<IntPair, IntWritable, Text, IntWritable> {
        private static final Text SEPARATOR = new Text("------------------------------------------------");
        private final Text first = new Text();

        @Override
        public void reduce(IntPair key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
            context.write(SEPARATOR, null);
            first.set(Integer.toString(key.getFirst()));
            for (IntWritable value : values) {
                context.write(first, value);
                System.out.println("----");
            }
        }
    }


    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        conf.set("fs.defaultFS", "hdfs://mac.cn:8020");

        Job job = Job.getInstance(conf, "secondary sort");
        job.setJarByClass(AdvancedSecondSortRunner.class);
        job.setMapperClass(MapClass.class);
        job.setReducerClass(Reduce.class);

       job.setSortComparatorClass(SortComparator.class);

        job.setGroupingComparatorClass(GroupingComparator.class);

        job.setMapOutputKeyClass(IntPair.class);
        job.setMapOutputValueClass(IntWritable.class);

        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        FileInputFormat.addInputPath(job, new Path("/user/mac/mapreduce/sort/input"));
        FileOutputFormat.setOutputPath(job, new Path("/user/mac/mapreduce/sort/secondout/"));


        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }

}


```

对应上面总结的三点查看该改良后的二次排序的代码总结如下：  
1.如果在main方法中的，没有注释掉job.setSortComparatorClass(SortComparator.class);那么map阶段将key进行排序的时候会使用SortComparator中定义的方法。  
2.如果注释掉该行代码，则是使用IntPair中定义的static域中的             WritableComparator.define(IntPair.class, new Comparator());的Comparator
3.如果没有该注册过的Comparator，则直接使用IntPair中的compareto方法。  


#### reduce阶段

查看代码发现，在reduce阶段注册了一个叫做GroupingComparator的比较类，该类的作用是将reduce拉取的数据进行合并，比如<1,2>，<1,3>合并为<1,2,3>，即按照key进行分组，比较相同的分为一组，不同的则不进行分组。

这里需要主意的是，并不是全部数据全部分组完毕后才进入到reduce阶段的，而是在合并完一个分组后会进入reduce方法处理数据，然后继续合并下一个分组，再进入reduce。

比如，到达reduce阶段的数据如下，

```xml

1 2
1 3
1 6

5 7

6 10
6 11
6 13

```

那么reduce阶段会先进入分组方法中将<1 2>,<1 3>,<1 6>进行合并为<1,{2,3,6}>,然后进入reduce。然后再进行分组<5,7>合并为<5,7>，然后进入reduce，然后再进行分组<6 10>,<6 11>,<6 13>分组为<6,{10,11,13}>然后继续进入reduce中。

而上面的group为何只比较key呢，因为我们想在reduce阶段中按照key记性分组，这样效率更高。

本文源码地址为:[https://github.com/CodeToSurvive1/bigdataproject/blob/master/src/main/java/mac/cn/SecondSort/advanced/AdvancedSecondSortRunner.java](https://github.com/CodeToSurvive1/bigdataproject/blob/master/src/main/java/mac/cn/SecondSort/advanced/AdvancedSecondSortRunner.java)  
