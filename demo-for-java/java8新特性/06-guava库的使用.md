# guava库的使用

```java
@Test
public void test() throws Throwable {
    // 创建Optional对象
    // 1.创建空的Optional对象
    Optional<Object> empty = Optional.empty();
    // 2.使用非null值创建Optional对象
    Optional<String> hello = Optional.of("Hello");
    // 3.使用任意值创建Optional对象
    Optional<Object> o = Optional.ofNullable(null);

    // 判断是否引用缺失的方法，不建议直接使用
    boolean present = o.isPresent();

    // 当optional引用存在时则执行Consumer
    // 类似的方法：map、filter、flatMap
    o.ifPresent(System.out::println);

    // 当optional引用缺失时设置默认值
    Object hello1 = o.orElse("Hello");
    o.orElseGet(() -> {
        // 自定义引用缺失时的返回值，Supplier
        return new String("null");
    });
    Object e = o.orElseThrow(() -> {
        // 引用缺失时抛出自定义的异常
        throw new RuntimeException("引用缺失异常");
    });
}

public static <T> void stream(List<T> list) {
    // 传入数组，进行遍历，如果数组为null时，会报错的写法
    // list.stream().forEach(System.out::println);
    // 结合Optional来避免null指针异常
    Optional.ofNullable(list)
        .map(List::stream)
        .orElseGet(Stream::empty)
        .forEach(System.out::println);
}

@Test
public void testOptional() {
    stream(null);
}

private static void remove(List<Integer> list) {
    list.remove(0);
}

@Test
public void immutableObject() {
    // 不可变对象：创建对象的不可拷贝是一项很好的防御性编程技巧
    //   优点：
    //     1.当对象被不可信的库调用时，不可变形式是安全的
    //     2.不可变对象被多个线程调用时，不存在竞态条件问题
    //     3.不可变集合不需要考虑变化，因此可以节省时间和空间
    //     4.不可变对象因为有固定不变，可以当作常量来安全使用
    // jdk提供的unmodifiableXXX方法（将可变对象转换为不可变对象）缺点：
    //   1.笨重且累赘；2.不安全；3.低效
    // 不可变集合：Guava为所有的JDK标准集合类型和Guava新集合类型
    //   都提供了简单易用的不可变版本

    List<Integer> list = new ArrayList<Integer>() {
        {
            // 初始化集合
            for (int i = 0; i < 5; i++) {
                add(i * i);
            }
        }
    };
    // 传入的原本集合被方法改变，这个操作有可能不是所想的
    remove(list);
    // 使用遍历语句进行遍历
    stream(list);

    // 使用jdk提供的方法将list变为不可变对象
    List<Integer> list1 = Collections.unmodifiableList(list);
    // 这个时候不能改变，会报异常
    // remove(list1);
    stream(list1);

    // guava创建不可变集合的方式：
    // 1.copyOf方法，通过已存在的集合创建：
    //   ImmutableSet.copyOf(set)
    ImmutableSet<Integer> integers = ImmutableSet.copyOf(list);

    // 2.of方法，通过初始值，直接创建不可变集合：
    //   ImmutableSet.of("a","b")
    ImmutableSet<Integer> immutableSet = ImmutableSet.of(1, 2, 3, 4);

    // 3.Builder工具：ImmutableSet.builder().build()
    ImmutableSet.builder().add(1)
        .add(4)
        .addAll(immutableSet)
        .build();

    // guava创建的不可变集合的使用：与正常方式一样，只是对修改禁止
}

@Test
public void testNewStructure() {
    // guava提供的新集合类型：

    // MultiSet：可重复的无序集合，可以视为：
    // 1.没有元素顺序限制的ArrayList(E)，常用方法：
    //   add(E)：添加单个给定元素
    //   iterator()：返回一个迭代器，包含Multiset所有元素（包括重复元素）
    //   size()：返回所有元素的总个数（包括重复元素）
    // 2.Map<E,Integer>，键为元素，值为计数，常用方法：
    //   count(Object)：返回给定元素的计数
    //   entrySet()：返回Set<MultiSet.Entry<E>>，和Map的entrySet类似
    //   elementSet()：返回所有不重复元素的Set<E>，类似Map的keySet
    // 与Map的区别：
    //   1.元素计数只能是正数
    //   2.multiset.size()返回集合大小
    //   3.multiset.iterator()会迭代重复元素
    //   4.multiset支持直接设置元素的计数
    //   5.没有的元素，multiset.count(E)为0
    // 实现类：
    //   1.HashMultiset
    //   2.TreeMultiset
    //   3.LinkedHashMultiset
    //   4.ConcurrentHashMultiset
    //   5.ImmutableMultiset

    // 测试：用新集合来计算一篇文章中字符出现的个数
    String text = "击壤歌\n" +
        "先秦：佚名\n" +
        "日出而作，日入而息。\n" +
        "凿井而饮，耕田而食。\n" +
        "帝力于我何有哉！";
    // 创建multiset
    Multiset<Character> multiset = HashMultiset.create();
    // string 转换成 char 数组
    char[] chars = text.toCharArray();
    // 遍历数组，添加到multiset
    multiset.addAll(Chars.asList(chars));
    System.out.println(multiset.size() + "\n" +
                       multiset.count('日'));
}

@Test
public void testGuavaUtils() {
    // guava提供了许多工具类，如Lists、Sets、Maps等

    // Lists工具类的方法：反转、拆分
    List<Integer> list =
        Lists.newArrayList(1, 2, 3, 4, 5, 6);
    // 拆分List
    List<List<Integer>> partition = Lists.partition(list, 2);
    // 反转List
    List<Integer> reverse = Lists.reverse(list);

    // Sets工具类中的方法：返回的均是不可变的集合
    // 并集、交集、差集、
    // 分解集合中的所有子集、
    // 求两个集合的笛卡尔积
    Set<Integer> set1 = Sets.newHashSet(1, 2, 3);
    Set<Integer> set2 = Sets.newHashSet(3, 4, 5);
    // 并集
    Set<Integer> union = Sets.union(set1, set2);
    // 交集
    Set<Integer> intersection = Sets.intersection(set1, set2);
    // 差集：元素属于A且不属于B
    Set<Integer> difference = Sets.difference(set1, set2);
    // 相对差集：属于A且不属于B 或者 属于B且不属于A
    Set<Integer> symmetricDifference = Sets.symmetricDifference(set1, set2);
    // 分解集合中的所有子集，即空集到整个集合
    // 如[1,2]的子集：[]、[1]、[2]、[1,2]
    Set<Set<Integer>> sets = Sets.powerSet(set1);
    // 计算两个集合的笛卡尔积
    Set<List<Integer>> lists = Sets.cartesianProduct(set1, set2);
}

@Test
public void testIO() throws IOException {
    // guava对字节流和字符流提供的工具方法：
    //   ByteStreams：提供对InputStream/OutputStream的操作
    //   CharStreams：提供对Reader/Writer的操作
    // guava对源（Source）和汇（Sink）的抽象：
    //   源是可读的：ByteSource/CharSource
    //   汇是可写的：ByteSink/CharSink

    // 创建对应的Source和Sink
    CharSource charSource = Files.asCharSource(
        new File("hello.txt"), Charsets.UTF_8);
    CharSink charSink = Files.asCharSink(
        new File("copy.txt"), Charsets.UTF_8);
    // 文件拷贝
    charSource.copyTo(charSink);
    // 文件输出
    charSource.readLines().forEach(System.out::println);
}
```

