:book: 青空的霞光B站课程

:date: 2024年4月17日

#### 迭代器

:star: 集合类都是支持`foreach`语句的，但是虽然你在代码里面写的是`foreach`写法实际上是语法糖，但是在编译后就会切换成迭代器的语法

```java
//foreach语法
    public static void main(String[] args) {
        List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));
        for(String s : list){
            System.out.println(s);
        }
    }

//编译后
    public static void main(String[] args) {
        List<String> list = new ArrayList(Arrays.asList("A", "B", "C"));
        Iterator var2 = list.iterator(); //使用了list的迭代器	

        while(var2.hasNext()) {
            String s = (String)var2.next();
            System.out.println(s);
        }
```

:one: 自己来解读一下这个啥意思，`List`的父类`Collection`继承了可迭代接口 `Iterable` ，里面有一个`Itertor`的迭代器方法需要实现

```java 
public interface Collection<E> extends Iterable<E> //继承

public interface Iterable<T> {
    /**
     * Returns an iterator over elements of type {@code T}.
     *
     * @return an Iterator.
     */
    Iterator<T> iterator(); //迭代器方法
}
```

:two: 可以看看`Iterator`迭代器内的方法

```java
public interface Iterator<E> {
    //看看是否还有下一个元素，放回boolean值
    boolean hasNext();

    //遍历当前元素，并将下一个元素作为待遍历元素
    E next();

    //移除上一个被遍历的元素（某些集合不支持这种操作）
    default void remove() {
        throw new UnsupportedOperationException("remove");
    }

    //对剩下的元素进行自定义遍历操作
    default void forEachRemaining(Consumer<? super E> action) {
        Objects.requireNonNull(action);
        while (hasNext())
            action.accept(next());
    }
}
```

:three: 在回头去看看编译后的代码，这样就好理解了

```java
//编译后
    public static void main(String[] args) {
        List<String> list = new ArrayList(Arrays.asList("A", "B", "C"));
        Iterator var2 = list.iterator(); //使用了list的迭代器	

        while(var2.hasNext()) { //去调用Itertor迭代器hasNext方法，查看下一个元素有就返回true
            String s = (String)var2.next(); //遍历当前的元素
            System.out.println(s); //打印当前元素
        }
```

:star: 迭代器的运作机制大概是

![image-20221002150914323](https://image.itbaima.cn/markdown/2022/10/02/8KS5jbTv7LoAVOs.png)

一个新的迭代器就像上面这样，默认有一个指向集合中第一个元素的指针：

![image-20221002151110991](https://image.itbaima.cn/markdown/2022/10/02/HxjfipVB9TlEbz5.png)

每一次`next`操作，都会将指针后移一位，直到完成每一个元素的遍历，此时再调用`next`将不能再得到下一个元素。至于为什么要这样设计，是因为集合类的实现方案有很多，可能是链式存储，也有可能是数组存储，不同的实现有着不同的遍历方式，而迭代器则可以将多种多样不同的集合类遍历方式进行统一，只需要各个集合类根据自己的情况进行对应实现就行了

:one: 在不同的集合类型中，实现`next`的方法也是不一样的可以看看

```java
//ArrayList直接访问下标
public E next() {
    ...
    cursor = i + 1;   //移动指针
    return (E) elementData[lastRet = i];  //直接返回指针所指元素
}


//LinkedList不断向后寻找终点

public E next() {
    ...
    next = next.next;   //向后继续寻找结点
    nextIndex++;
    return lastReturned.item;  //返回结点内部存放的元素
}
```

:two: 但是无论内部是如何实现的，我们在使用的时候直接使用迭代器就好了，使用方法都是一样的

```
public static void main(String[] args) {
    List<String> list = Arrays.asList("A", "B", "C");
    Iterator<String> iterator = list.iterator();
    while (iterator.hasNext()) {    //每次循环一定要判断是否还有元素剩余
        System.out.println(iterator.next());  //如果有就可以继续获取到下一个元素
    }
}
```

:star: 迭代器使用时一次性的，也就是一个迭代器对象，只能使用一次，如果想要再次遍历就需要在去生成一个迭代器对象，所以为了简便就直接使用`foreach`就好了

<img src="https://image-1305907375.cos.ap-chengdu.myqcloud.com/Flower_bound_manage_boundimage-20240417160604150.png" alt="image-20240417160604150" style="zoom:150%;" />

```java
    public static void main(String[] args) {
        List<String> list = Arrays.asList("A", "B", "C");
        for (String s : list) {   
            System.out.println(s);  
        }
    }
```

:three: 在`JAVA8`中提供了一个支持`Lambda`表达式的`forEach`方法，这个方法需要接受一个`Consumer`对象(可以去泛型笔记看看函数式接口)，

```java
    public static void main(String[] args) {
        List<String> list = Arrays.asList("A", "B", "C");
        list.forEach(new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        });
    }

//缩减成lambda表达式
    public static void main(String[] args) {
        List<String> list = Arrays.asList("A", "B", "C");
        list.forEach(s -> System.out.println(s));
    }

//还可以继续替换成方法引用
    public static void main(String[] args) {
        List<String> list = Arrays.asList("A", "B", "C");
        list.forEach(System.out::println);
    }
```

但是它本质也是使用的`foreach`循环来遍历打印，这个方法实在`Iterable`接口中定义的

```java
default void forEach(Consumer<? super T> action) {
    Objects.requireNonNull(action);
    for (T t : this) {   //foreach语法遍历每一个元素
        action.accept(t);   //调用Consumer的accept来对每一个元素进行消费
    }
}
```

:star: `Iterator`是继承自`Iterable` ，看一下它的源代码

![17133427139542886734410508459084](https://image-1305907375.cos.ap-chengdu.myqcloud.com/Flower_bound_manage_bound17133427139542886734410508459084.png)

```java
//注意这个接口是集合接口的父接口，不要跟之前的迭代器接口搞混了
public interface Iterable<T> {
    //生成当前集合的迭代器，在Collection接口中重复定义了一次
    Iterator<T> iterator();

    //Java8新增方法，因为是在顶层接口中定义的，因此所有的集合类都有这个方法
    default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
    }

    //这个方法会在多线程部分中进行介绍，暂时不做讲解
    default Spliterator<T> spliterator() {
        return Spliterators.spliteratorUnknownSize(iterator(), 0);
    }
}
```

:one: `Iterable`提供了迭代器的生成方法，实际上只要是实现了迭代器接口的类（我们自己写的都行），都可以使用`foreach`语法

```java

public class Main {
    public static void main(String[] args) {
        Test test = new Test();
//        test.forEach(System.out::println); 
        //或则
        for (String s : test){
            System.out.println(s);
        }
    }
    public static class Test implements Iterable<String>{ //写一个类实现Iterable接口

        @Override
        public Iterator<String> iterator() { //需要重写iterator方法
            return new Iterator<String>() { //创建一个迭代器对象，需要通过内部类来实现它的两个方法
                @Override
                public boolean hasNext() { //表示一直会返回true,也就是告诉程序我这个里面会一直有数据
                    return true;
                }

                @Override
                public String next() { //返回当前数据
                    return "TestIng...";
                }
            };
        }
    }
}
```

:star: `ListIterator`这个迭代器是针对`List`的强化版本，增加了很多操作，`List`是有序集合，使用他还支持两种方向的遍历，从后往前，从前往后

```java
public interface ListIterator<E> extends Iterator<E> {
    //原本就有的
    boolean hasNext();

    //原本就有的
    E next();

    //查看前面是否有已经遍历的元素
    boolean hasPrevious();

    //跟next相反，这里是倒着往回遍历
    E previous();

    //返回下一个待遍历元素的下标
    int nextIndex();

    //返回上一个已遍历元素的下标
    int previousIndex();

    //原本就有的
    void remove();

    //将上一个已遍历元素修改为新的元素
    void set(E e);

    //在遍历过程中，插入新的元素到当前待遍历元素之前
    void add(E e);
}
```

:one:测试一下

```java
    public static void main(String[] args) {
        List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));
        ListIterator<String> iterator = list.listIterator();
        iterator.next();   //此时得到A
        iterator.set("X");  //将A原本位置的上的元素设定为成新的
        System.out.println(list);
        iterator.remove(); //会删除上一个遍历
        System.out.println(list);
    }

//输出:
[X, B, C]
[B, C]
```

