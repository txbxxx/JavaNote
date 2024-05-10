:book: 青空B站课程

:date: 2024年4月19日



### `Collections `工具类

:star: 以下是常用列表：

| 方法签名                                                     | 使用示例                                                     | 功能描述                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `void sort(List<T> list)`                                    | ```Collections.sort(myList);```                              | 根据元素的自然顺序（Comparable接口实现）对List集合进行升序排序 |
| `void sort(List<T> list, Comparator<? super T> c)`           | ```Collections.sort(myList, new MyComparator());```          | 根据自定义Comparator的规则对List集合进行排序                 |
| `void reverse(List<?> list)`                                 | ```Collections.reverse(myList);```                           | 反转List中元素的顺序                                         |
| `void shuffle(List<?> list)`                                 | ```Collections.shuffle(myList);```                           | 对List集合中的元素进行随机排序                               |
| `void swap(List<?> list, int i, int j)`                      | ```Collections.swap(myList, 0, 1);```                        | 将List中索引为i和j的两个元素位置互换                         |
| `<T> boolean addAll(Collection<? super T> c, T... elements)` | ```Collections.addAll(myList, element1, element2, element3);``` | 将一组元素添加到指定集合c中                                  |
| `<T extends Comparable<? super T>> T max(Collection<? extends T> coll)` | ```Integer maxNum = Collections.max(myIntegerList);```       | 返回集合中根据自然顺序的最大元素                             |
| `<T> T max(Collection<? extends T> coll, Comparator<? super T> comp)` | ```String maxStr = Collections.max(myStringList, new MyStringComparator());``` | 返回集合中根据Comparator指定顺序的最大元素                   |
| `<T extends Comparable<? super T>> T min(Collection<? extends T> coll)` | ```Integer minNum = Collections.min(myIntegerList);```       | 返回集合中根据自然顺序的最小元素                             |
| `<T> T min(Collection<? extends T> coll, Comparator<? super T> comp)` | ```String minStr = Collections.min(myStringList, new MyStringComparator());``` | 返回集合中根据Comparator指定顺序的最小元素                   |
| `int binarySearch(List<? extends Comparable<? super T>> list, T key)` | ```int index = Collections.binarySearch(mySortedList, myKey);``` | 在有序List中执行二分查找，返回key对应的索引或负数（表示插入点） |
| `boolean replaceAll(List<T> list, T oldVal, T newVal)`       | ```Collections.replaceAll(myList, oldValue, newValue);```    | 替换列表中所有出现的oldVal元素为newVal                       |
| `void fill(List<? super T> list, T obj)`                     | ```Collections.fill(myList, myObject);```                    | 用指定对象填充整个列表                                       |
| `void copy(List<? super T> dest, List<? extends T> src)`     | ```Collections.copy(destList, srcList);```                   | 将src列表的元素复制到dest列表中                              |
| `<T> List<T> unmodifiableList(List<? extends T> list)`       | ```List<T> unmodifiableMyList = Collections.unmodifiableList(myList);``` | 创建并返回一个不可修改的新列表视图                           |
| `<T> Set<T> unmodifiableSet(Set<? extends T> s)`             | ```Set<T> unmodifiableMySet = Collections.unmodifiableSet(mySet);``` | 创建并返回一个不可修改的新集合理视图                         |
| `<K,V> Map<K,V> unmodifiableMap(Map<? extends K,? extends V> m)` | ```Map<K,V> unmodifiableMyMap = Collections.unmodifiableMap(myMap);``` | 创建并返回一个不可修改的新映射视图                           |
| `<T> Collection<T> synchronizedCollection(Collection<T> c)`  | ```Collection<T> syncMyCollection = Collections.synchronizedCollection(myCollection);``` | 返回指定集合的线程安全包装                                   |
| `<T> List<T> synchronizedList(List<T> list)`                 | ```List<T> syncMyList = Collections.synchronizedList(myList);``` | 返回指定列表的线程安全包装                                   |
| `<T> Set<T> synchronizedSet(Set<T> s)`                       | ```Set<T> syncMySet = Collections.synchronizedSet(mySet);``` | 返回指定集合并发安全的包装                                   |
| `<K,V> Map<K,V> synchronizedMap(Map<K,V> m)`                 | ```Map<K,V> syncMyMap = Collections.synchronizedMap(myMap);``` | 返回指定映射并发安全的包装                                   |

这里的`MyComparator`和`MyStringComparator`是自定义的比较器类，实现了`Comparator`接口。在实际使用时，需要替换为具体的比较器实现。同时，请确保集合中的元素类型与Comparator支持的类型相匹配。

:star: 代码实例

```java
    public static void main(String[] args) {
        List<String> list = new ArrayList<>(Arrays.asList("A","C","B"));
        Collections.sort(list); //排序
        Collections.reverse(list); //反转
        Collections.shuffle(list); //随机排序
        Collections.fill(list,"D"); //快速填充，会把里面的说有元素都变为D

        List<Integer> integerList = new ArrayList<>(Arrays.asList(1,2,100,20));
        System.out.println(Collections.max(integerList)); //获取最大值
        System.out.println(Collections.min(integerList)); //获取最小值
        System.out.println(Collections.binarySearch(integerList,2)); //二分查找必须要实现比较器接口Comparable
        List<Integer> newlist = Collections.unmodifiableList(integerList); //将他变为只读
        System.out.println(Collections.indexOfSubList(list, Arrays.asList(2, 100))); //寻找子集合的位置
        List<Integer> emptyList = Collections.emptyList(); //创建自读的空集合
        List<Integer> singletonList = Collections.singletonList(1); //创建只有一个元素的只读集合
        System.out.println(list);
    }
```

得益于泛型的类型擦除机制，实际上最后只要是Object的实现类都可以保存到集合类中，那么就会出现这种情况：

```java
public static void main(String[] args) {
  	//使用原始类型接收一个Integer类型的ArrayList
    List list = new ArrayList<>(Arrays.asList(1,2,3,4,5));
    list.add("aaa");   //我们惊奇地发现，这玩意居然能存字符串进去
    System.out.println(list);
}
```

![image-20221004001007854](https://image.itbaima.cn/markdown/2022/10/04/FP5z3X8SEMkGYtT.png)

没错，由于泛型机制上的一些漏洞，实际上对应类型的集合类有可能会存放其他类型的值，泛型的类型检查只存在于编译阶段，只要我们绕过这个阶段，在实际运行时，并不会真的进行类型检查，要解决这种问题很简单，就是在运行时进行类型检查：

```java
public static void main(String[] args) {
    List list = new ArrayList<>(Arrays.asList(1,2,3,4,5));
    list = Collections.checkedList(list, Integer.class);   //这里的.class关键字我们会在后面反射中介绍，表示Integer这个类型
  	list.add("aaa");
    System.out.println(list);
}
```

`checkedXXX`可以将给定集合类进行包装，在运行时同样会进行类型检查，如果通过上面的漏洞插入一个本不应该是当前类型集合支持的类型，那么会直接抛出类型转换异常：

![image-20221004001409799](https://image.itbaima.cn/markdown/2022/10/04/5BHq1u9JU3bhdI6.png)