### 1. 面向对象的基本特征有哪些？并作出解释

1. 封装：封装的好处就是安全，方便。封装隐藏了对象的具体实现，当要操纵对象时，只需调用其中的方法，而不用管方法的具体实现。属性的封装就是属性私有化并提供get/set方法，这样外界只能通过get/set方法来操作属性，行为变得可控。
2. 继承：继承的好处就是代码的复用和扩展。继承可以保留父类的属性和方法，同时子类又可以扩展自己的属性和方法。
3. 多态：目的是实现代码的灵活性，多态体现在重载和重写方法，更多的时候指的是对象的多态性，即当父类的变量指向子类的对象时，那么调用子类重写的方法时，运行的是子类重写过的代码，从而实现同一个父类的变量，因为赋值的子类对象不同而体现出不同的功能。应用主要体现在多态参数和多态数组中。

### 2. Java的基本数据类型有哪些？String是基本数据类型吗？

基本数据类型有：byte,short,int,long,float,double,char,boolean

String是引用数据类型，不是基本数据类型

### 3. break、continue、return的区别？

break用于switch和循环，用于结束switch，和当前循环

continue用于循环，用于结束本次循环

return用于结束当前方法，还可以用于return 返回值;返回结果

### 4. final、finalize、finally的区别？

final是表示最终的，是一个修饰符，修饰类时表示不能被继承，修饰方法时表示不能被子类重写，修饰属性和局部变量时表示值不能被修改，是个常量。

finally是表示最终块，是异常处理的一部分，和try..catch一起使用，不管是否发生异常都要执行的代码放在finally块中。

finalize是表示最终方法，是java.lang.Object类的一个方法，在对象被垃圾回收时调用。

### 5. 请解释String、StringBuilder、StringBuffer的区别？

String是不可变的字符序列，因此字符串常量存储在常量池中，而StringBuilder和StringBuffer是可变的字符序列。

String对象是常量对象，因此一旦拼接和修改就会产生新的String对象。

SringBuffer和StringBuilder可以在原对象上进行append,insert,delete,replace等修改。

StringBuilder和StringBuffer是完全兼容的API，但是StringBuilder是线程不安全的、StringBuffer是线程安全的。

### 6. 请解释Overload与Override的区别？

Overload是方法重载，指的是在同一个类中，方法名称相同，形参列表不同的两个或者多个方法，和返回值类型无关。

Override是方法的重写，指的是子类在继承父类时，当父类的方法体不适用于子类时，子类可重写父类的方法。重写必须遵守方法名和形参列表与父类的被重写的方法相同，而返回值类型可以小于等于父类被重写的方法（如果是基本数据类型和void必须相同），权限修饰符可以大于等于父类被重写的方法，抛出的异常列表可以小于等于父类被重写的方法。

### 7. 请解释Collection 和 Collections 的区别?List、Set、Map是否继承Collection？

Collection是接口，是List和Set系列接口的父接口。是Collection系列接口的根接口。

Collections是工具类，其中提供了很多静态方法来操作各种集合。

List和Set继承Collection，Map不继承Collection。

### 8. List、Map、Set 三个接口，存取元素时，各有什么特点？

List：是有序的，可重复的，添加元素的方法是add，可以根据索引获取元素。

Set：是无序的，不可重复的，添加元素的方式是add，HashSet和LinkedHashSet的元素是依据hashCode和equals区别元素是否相等，而TreeSet是依据compareTo或compare区别元素是否相等。

Map：是存储键值对的，添加的方法是put(key,value)，可以根据key获取value。

### 9. ArrayList和LinkedList 的底层实现（存储结构、扩容机制）

1. ArrayList是实现了基于动态数组的数据结构，LinkedList基于链表的数据结构。

2. 对于随机访问get和set，ArrayList觉得优于LinkedList，因为LinkedList要移动指针。

3. 对于新增和删除操作add和remove，LinedList比较占优势，因为ArrayList要移动数据。 这一点要看实际情况的。若只对单条数据插入或删除，ArrayList的速度反而优于LinkedList。但若是批量随机的插入删除数据，LinkedList的速度大大优于ArrayList. 因为ArrayList每插入一条数据，要移动插入点及之后的所有数据。

### 10. 请解释Arraylist、Linkedlist和Vector的区别？

ArrayList：是线程不安全的动态数组，底层是数组结构，JDK1.7后初始化为空数组，在添加第一个元素时初始化为长度为10的数组，如果容量满了，按照1.5倍扩容。支持foreach和Iterator遍历。

Vector：是线程安全的动态数组，底层是数组结构，初始化为长度为10的数组，如果容量满了，按照2.0倍扩容。除了支持foreach和Iterator遍历，还支持Enumeration迭代。

LinkedList：是双向链表，底层是链表结构。当频繁在集合中插入、删除元素时，效率较高，但是查找遍历的效率较低。

### 11. Hashtable与HashMap的区别？如何解决那个线程不安全的问题？

Hashtable是线程安全的哈希表，底层结构是数组+链表。

HashMap是线程不安全的哈希表，底层结构是JDK1.7时数组+链表，JDK1.8时数组+链表/红黑树。

HashMap的线程安全问题可以使用Collections的synchronizedMap(Map<K,V> m) 方法解决。