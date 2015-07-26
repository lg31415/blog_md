title: "Hadoop_codes"
date: 2015-04-19 09:12:01
tags: Hadoop
categories: Hadoop
---

##Day02
`HdfsUtil.java`
```java
public class HdfsUtil {

    private FileSystem fs;

    @Before
    public void init() throws IOException {
        Configuration conf = new Configuration();
        // conf.set("dfs.replication", "1");
        // conf.set("fs.defaultFS", "hdfs://yun-11:9000/");
        // get the instance of FileSystem
        fs = FileSystem.get(conf);

    }

    /**
     * 
     * upload local file to hdfs
     * 
     * @throws IOException
     * 
     */
    @Test
    public void uploadFile() throws IOException {

        Path f = new Path("/baby.jpg");
        FSDataOutputStream os = fs.create(f);

        FileInputStream is = new FileInputStream(new File(
                "/home/hadoop/baby.jpg"));

        IOUtils.copy(is, os);

    }

    /**
     * 
     * upload local file to hdfs method 2
     * 
     * @throws IOException
     * 
     */
    @Test
    public void uploadFileSimple() throws IOException {

        // src ---- local path
        Path src = new Path("/home/hadoop/baby.jpg");

        // dst ---- hdfs path
        Path dst = new Path("/aa/bb/baby.jpg");

        fs.copyFromLocalFile(src, dst);

    }

    /**
     * 
     * download file from hdfs to local filesystem
     * 
     * @throws IOException
     * @throws IllegalArgumentException
     * 
     */
    @Test
    public void downloadFile() throws Exception {

        FSDataInputStream is = fs.open(new Path("/baby.jpg"));

        FileOutputStream os = new FileOutputStream(new File(
                "/home/hadoop/baby.jpg.jpg"));

        IOUtils.copy(is, os);

        // simple version
        // fs.copyToLocalFile(new Path("/aa/bb/baby.jpg"), new
        // Path("/home/hadoop/baby2.jpg"));

    }

    @Test
    public void mkdir() throws Exception {

        boolean ok = fs.mkdirs(new Path("/aa/bb/cc"));
        System.out.println(ok ? "it is ok" : "it is failed");

    }

    @Test
    public void rmFileOrDir() throws Exception {

        fs.delete(new Path("/aa/bb/cc"), true);

    }

    @Test
    public void listFiles() throws FileNotFoundException,
            IllegalArgumentException, IOException {

        RemoteIterator<LocatedFileStatus> listFiles = fs.listFiles(
                new Path("/"), true);

        while (listFiles.hasNext()) {

            LocatedFileStatus file = listFiles.next();
            System.out.println(file.getPath());
            BlockLocation[] blockLocations = file.getBlockLocations();
            for (int i = 0; i < blockLocations.length; i++) {

                System.out.println(blockLocations[i].getHosts()[0]);
            }
        }

    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        conf.set("fs.defaultFS", "hdfs://yun-11:9000/");
        FileSystem fs = FileSystem.get(conf);

        FSDataInputStream is = fs.open(new Path("/eclipse-jee-luna-SR1-linux-gtk.tar.gz"));

        FileOutputStream os = new FileOutputStream(new File("c:/eclipse.tgz"));
        
        IOUtils.copy(is, os);

    }

}
```


## RPC
### RpcServer
```java
public class RpcServer {
    public static void main(String[] args) throws Exception {

        //用RPC框架拿到一个server的builder（server构造器）
        Builder builder = new RPC.Builder(new Configuration());
        //给构造器传递一些必要的构造参数,服务端所监听的 ip地址和端口号，服务的真实业务实例，业务接口 
        builder.setBindAddress("yun-11").setPort(10000).setInstance(new LoginServiceImpl()).setProtocol(LoginServiceInterface.class);
        
        //再用builder创建服务实例
        Server server = builder.build();
        
        //启动该服务实例
        server.start();
        
    }
}
```

+ LoginServiceInterface
```java
public interface LoginServiceInterface {
    //要事先设置好版本号
    public static final long versionID=1L;
    public String login(String name,String password);
    
}
```

+ LoginServiceImpl
```java
public class LoginServiceImpl implements LoginServiceInterface {

    @Override
    public String login(String name, String password) {
        
        return "" + name +"logged in successfully";
    }

}
```
+ LoginController
``` java
public class LoginController {

    public static void main(String[] args) throws IOException {
        //通过RPC的工具拿到服务的一个动态代理对象（是本端的socket程序的动态代理，但是它也实现了服务的接口协议）
        LoginServiceInterface ls = RPC.getProxy(LoginServiceInterface.class, 1L, new InetSocketAddress("yun-11", 10000), new Configuration());
        //用这个动态代理对象调用服务方法，就可以拿到调用结果
        String result = ls.login("baby", "1314520");
        System.out.println(result);
        //最好关闭链接
        RPC.stopProxy(ls);
        
    }
    
    
}
```

## Day03

+ `WordCountMapper`
```java
public class WordCountMapper extends Mapper<LongWritable, Text, Text, LongWritable>{
    @Override
    protected void map(LongWritable key, Text value,Context context)
            throws IOException, InterruptedException {

        //拿到一行的内容
        String line = value.toString();
        //将这一行切分成单词数组
        String[] words = StringUtils.split(line, " ");
        
        //遍历单词数组
        for(String word: words){
            //输出<单词，1>这样的key-value对
            context.write(new Text(word), new LongWritable(1));
        }
    }
}
```
+ `WordCountReducer`
```java
public class WordCountReducer extends Reducer<Text, LongWritable, Text, LongWritable>{
    //调用reduce方法时，传递进来的数据是这样的 ：<hello,{1,1....}>
    @Override
    protected void reduce(Text key, Iterable<LongWritable> values,Context context)
            throws IOException, InterruptedException {
        //定义一个累加计数器
        long counter=0;
        //遍历values，累加到counter里面
        for(LongWritable value:values){
            //累加每一个value
            counter += value.get();
        }
        // 输出这一个单词的统计结果
        context.write(key, new LongWritable(counter));       
    }
}
```
+ `WordCountRunner`
```java
public class WordCountRunner {
    /**
     * 描述本次处理任务的一些必要信息
     * @param args
     * @throws IOException 
     * @throws InterruptedException 
     * @throws ClassNotFoundException 
     */
    public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
        //封装任务信息的对象为Job对象,所以要先构造一个Job对象
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf);
        //设置本次job作业所在的jar包
        job.setJarByClass(WordCountRunner.class);
        //本次job作业使用的mapper类是哪个？
        job.setMapperClass(WordCountMapper.class);
        //本次job作业使用的reducer类是哪个？
        job.setReducerClass(WordCountReducer.class);
        //本次job作业mapper类的输出数据key类型
        job.setMapOutputKeyClass(Text.class);
        //本次job作业mapper类的输出数据value类型
        job.setMapOutputValueClass(LongWritable.class);
        //本次job作业reducer类的输出数据key类型
        job.setOutputKeyClass(Text.class);
        //本次job作业reducer类的输出数据value类型
        job.setOutputValueClass(LongWritable.class);
        //本次job作业要处理的原始数据所在的路径
        FileInputFormat.setInputPaths(job, new Path("hdfs://yun-11:9000/wordcount/srcdata"));
        //本次job作业产生的结果输出路径
        FileOutputFormat.setOutputPath(job, new Path("hdfs://yun-11:9000/wordcount/output"));
        //提交本次作业
        job.waitForCompletion(true);      
    }
   
}
```
## 手机流量分析
+ `FlowBean`
```java
public class FlowBean implements Writable{
    private long up_flow;
    private long d_flow;
    private long sum_flow;
    //反序列化时需要用到反射机制，所以必须要有一个默认构造方法
    public FlowBean(){}
    public FlowBean(long up_flow,long d_flow) {
        this.up_flow = up_flow;
        this.d_flow = d_flow;
        this.sum_flow = up_flow + d_flow;
    }
    public long getUp_flow() {
        return up_flow;
    }
    public long getD_flow() {
        return d_flow;
    }
    public long getSum_flow() {
        return sum_flow;
    }
    /**
     * 序列化，将对象的各个组成部分按byte流顺序写入output流里去
     * 
     */
    @Override
    public void write(DataOutput out) throws IOException {
        out.writeLong(up_flow);
        out.writeLong(d_flow);
        out.writeLong(sum_flow);
        //    sum_flow-d_flow-up_flow --->
    }
    /**
     * 
     * 反序列化，从一个数据输入流中按顺序读出对象中的各个组成部分
     * 反序列化时字段的读取顺序一定要与序列化时的顺序匹配
     * 
     */
    @Override
    public void readFields(DataInput in) throws IOException {
        // ---> sum_flow-d_flow-up_flow --->
        up_flow = in.readLong();
        d_flow = in.readLong();
        sum_flow = in.readLong();
    }

    //为了直接输出这个bean，应该重写toString方法
    @Override
    public String toString() {
        return up_flow + "\t" + d_flow + "\t" + sum_flow;
    }

}
```
+ `FlowBeanMapper`
```java
public class FlowBeanMapper extends Mapper<LongWritable, Text, Text, FlowBean>{
    @Override
    protected void map(LongWritable key, Text value,Context context)
            throws IOException, InterruptedException {
        //先拿到一行数据 
        String line = value.toString();
        //切分出各个字段
        String[] fields = StringUtils.split(line, "\t");
        //拿到手机号字段
        String phoneNbr = fields[1];
        //拿到上行流量字段值
        long up_flow = Long.parseLong(fields[fields.length-3]);
        //拿到下行流量字段值
        long d_flow = Long.parseLong(fields[fields.length-2]);
        //将上下行流量封装到flowBean中去
        FlowBean flowBean = new FlowBean(up_flow, d_flow);
        //将这个手机号的号码和流量bean输出为一对key-value
        context.write(new Text(phoneNbr), flowBean);
    }
}
```
+ `FlowBeanReducer`
```java
public class FlowBeanReducer extends Reducer<Text, FlowBean, Text, FlowBean>{
    @Override
    protected void reduce(Text key, Iterable<FlowBean> values,Context context)
            throws IOException, InterruptedException {
        // 拿到的数据：   key：手机号    values: <flowBean,flowBean,flowBean......>
        //定义上下行流量的计数器
        long up_flow_counter=0;
        long d_flow_counter=0;
        //循环遍历values进行累加
        for(FlowBean value : values){
            up_flow_counter += value.getUp_flow();
            d_flow_counter += value.getD_flow();
        }
        //将统计结果输出
        FlowBean resultBean = new FlowBean(up_flow_counter, d_flow_counter);
        context.write(key, resultBean);
    }
}
```
+ `FlowBeanRunner`
```java
public class FlowBeanRunner {

    
    public static void main(String[] args) throws Exception {
        
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf);
        
        job.setJarByClass(FlowBeanRunner.class);
        job.setMapperClass(FlowBeanMapper.class);
        job.setReducerClass(FlowBeanReducer.class);
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(FlowBean.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(FlowBean.class);
        FileInputFormat.setInputPaths(job, new Path("hdfs://yun-11:9000/flow/sourcedata"));
        FileOutputFormat.setOutputPath(job, new Path("hdfs://yun-11:9000/flow/output"));
        
        job.waitForCompletion(true);
    }
}
```
```java

```
