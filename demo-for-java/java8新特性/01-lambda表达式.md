# lambda表达式

### -> 表达式

箭头表达式 `()->{}` ：

- 左边为表达式的参数列表
- 右边为表达式中需要执行的功能

情况：

1. 无参无返回值：`()->{System.out.println("hello world!")}`
2. 有参无返回值：
   - `e1 -> {System.out.println("hello world!")}`
   - `(e1 , e2) -> {System.out.println("hello world!")}`
3. 有返回值：
   - `()->{return true;}`
   - `n -> n*n`

Lambda表达式基于函数式接口，即Lambda只能用在函数式接口上：

- 函数式接口：有且只有一个方法的接口
- 可以使用 `@FunctionInterface` 标注在接口上，用来检查是否是函数式接口

### 内置函数式接口

Java 8 内置的基础函数式接口：

1. `Consumer<T>`：消费型接口，方法为 `void accept(T t);`
2. `Supplier<T>`：供给型接口，方法为 `T get();`
3. `Function<T,R>`：函数型接口，方法为 `R apply(T t);`
4. `Predicate<T>`：断言型接口，方法为 `boolean test(T t);`

### 方法引用

方法引用：若Lambda体中的内容方法已经实现了，可以使用"方法引用"，方法引用有三种语法格式：

1. `对象::实例方法名` ： 如`        Consumer<String> consumer = System.out::println;`
2. `类::静态方法名` ：如 `Supplier<Double> supplier = Math::random;`
3. `类::实例方法名`：
   - 如 `Function<String ,String> fun = String::toLowerCase;`
   - 注意：函数式接口的第一个参数为实例方法的调用者，第二个参数为实例方法的参数
4. 自定义：`Predicate<Integer> predicate = x -> x >= 0;`
5. 注意事项：方法引用中调用方法的参数列表和返回值类型要与函数式接口中的函数列表和返回值保持一致

### 构造器引用

语法格式：`ClassName::new`

- 调用的构造器根据传入参数的多少来决定调用哪个构造方法

```java
public class Employee {
    String name;
    int salary;
    int age;

    public Employee() {
    }

    public Employee(String name, int salary, int age) {
        this.name = name;
        this.salary = salary;
        this.age = age;
    }

    public Employee(int salary) {
        this.salary = salary;
    }

    @Override
    public String toString() {
        return "Employee{" +
            "name='" + name + '\'' +
            ", salary=" + salary +
            ", age=" + age +
            '}';
    }
}
// 调用的是只有一个参数的构造方法
Function<Integer, Employee> function = Employee::new;
// 调用无参构造器
Supplier<Employee> consumer = Employee::new;
```

### 数组引用

语法格式：`Type::new`

```java
// 创建一个长度为 n 的char数组
Function<Integer, char[]> function = char[]::new;
```

