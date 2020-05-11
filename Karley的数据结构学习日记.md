# 栈

## 重点

后进先出(Last in first out,LIFO)

进行删除插入操作的成为栈顶(top)，另一端成为栈底(bottom)

## 基本运算

1. 初始化栈InitStack(S)：创建一个空栈S。
2. 判断栈空isEmpty(S)：当栈为空时返回“真”，否则返回“假”。
3. 入栈Push(S,x)：将元素压入栈顶，并更新栈顶指针
4. 出栈Pop(S)：将栈顶元素从栈中删除。若需要栈顶元素的值，可将Pop(S)定义为一个返回栈顶元素值的函数。
5. 读栈顶元素Top(S)：返回栈顶元素的值，但不修改栈顶指针。

## 存储结构

1. 顺序存储
   - 使用一组地址连续的存储单元一次存储自栈顶到栈底的数据元素
   - 同时附设指针top指示栈顶元素的位置
   - 采用顺序存储结构的栈称为顺序栈
   - 栈空间容量有限，需要预先定义栈的存储空间，所以，元素入栈时，需要判断是否栈满，若栈满，元素不能入栈
2. 链式存储
   - 以链表作为存储结构
   - 因为栈的操作在栈顶一段进行，所以不必设置头指针，联表的头指针就是栈顶指针
3. 栈的应用
   - 表达式求值、括号匹配
   - 递归过程转变为非递归过程的处理

# 队列

## 重点

先进先出(First in first out,FIFO)

在表的一端插入元素，在表的另一端删除元素

插入元素的一端称为队尾，删除元素的一端称为队头

## 基本运算

1. 初始化队列InitQueue(Q)：创建一个空的队列Q。
2. 判断队列是否为空isEmpty(Q)：当队列为空时返回“真”，否则返回“假”。
3. 入队EnQueue(Q,x)：将元素x加入到队列Q的队尾，并更新队尾指针
4. 出队DelQueue(Q)：将队头元素从队列Q中删除，并更新队头指针
5. 读队头元素FrontQue(Q)：返回队头元素的值，但不更新队头指针

## 存储结构

1. 顺序存储

   - 顺序队列
   - 一组地址连续的存储单元存放队列中的元素，设置队头指针(front)和队尾(rear)指针
   - 元素入队时只修改队尾指针，元素出队时只修改队头指针
   - 顺序队列存储空间提前设定，队尾指针有上限值，当队尾指针达到该上限时，不能只通过修改队尾指针实现新元素的入队操作。
   - 将顺序队列假象成一个环状(通过整除取余运算实现)，则可维持入队、出队操作元算的简单性，称之为循环队列。

   假设循环队列Q的容量为MAXSIZE，初始化队列为空，且Q.rear和Q.front都等于0。

   - 元素入队时，修改队列指针Q.rear=(Q.rear+1)%MAZSIZE
   - 元素出队时，修改队头指针Q.front=(Q.front+1)%MAXSIZE
   - 当队列为空或为满时，则有Q.rear==Q.front，判断队列状态：
     - 设置一个标志，区别头、尾指针的值相同时队列状态
     - 牺牲一个存储单元，约定“队列的尾指针所指位置的下一个位置时队头指针时”表示队列满，而此时头、尾指针相同时表示队列为空

2. 链式存储

   - 链队列
   - 给链队列添加一个头结点，并令头指针指向头结点
   - 头指针与尾指针的值相同，且均指向头结点时，队列为空

3. 队列应用

   - 操作系统中处理打印任务的打印队列，离散事件的计算机模拟

# 哈希

## 定义

哈希表通过计算一个以记录为关键字的自变量的函数(哈希函数)，来得到该记录的存储地址，所以在哈希表中进行查找时，需要用同一哈希函数计算得到待查记录的存储地址，然后到相应的存储单元获得有关信息再判断查找是否成功

一个简单例子理解哈希：

![image-20200219132836413](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200219132836413.png)

其实数组conpanies就是一个哈希表，典型【空间换时间】

![image-20200219135518906](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200219135518906.png)

## 哈希冲突

![image-20200219140106059](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200219140106059.png)

### 解决方案

![image-20200219141115721](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200219141115721.png)

## 哈希函数

![image-20200219142650070](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200219142650070.png)

![image-20200219142407286](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200219142407286.png)

### 生成key的哈希值

![image-20200219143828481](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200219143828481.png)

![image-20200219143841585](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200219143841585.png)

### Long和Double的哈希值

![image-20200219150132207](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200219150132207.png)

### 字符串的哈希值

![image-20200219151056088](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200219151056088.png)

- 31*i = (2^5-1)*i = i*2^5-i = (i << 5)-i

![image-20200219150912030](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200219150912030.png)

```java
public class Main{
    public static void main(String[] args){
        String string = "jack";
        int len = string.length();
        int hashCode = 0;
        for(int i=0;i < len;i++){
            char c = string.charAt(i);
            hashCode = hashCode * 31 + c;
            //hashCode = (hashCode << 5) - hashCode + c;
        }
        System.out.println(hashCode);
        System.out.println(string.hashCode());
    }
}
```

### 总结哈希算法

```java
public class Main{
	public static void main(String[] args){
        Integer a = 110;
        Float b = 10.6f;
        Long c = 156l;
        Double d = 10.9;
        String e = "rose";
        
        System.out.println(a.hashCode());
        System.out.println(b.hashCode());
        System.out.println(c.hashCode());
        System.out.println(d.hashCode());
        System.out.println(e.hashCode());
    }
}
```

### 自定义对象哈希值

```java
Main.java
    public static void main(String[] args){
    Person p1 = new Person(10,1.67f,"jack");
    Person p2 = new Person(10,1.67f,"jack");
    System.out.println(p1.hashCode());
    System.out.println(p2.hashCode());
    //得出哈希值不同，因为java官方根据内存生成哈希值
    Map<Object,Object> map = new HashMap<>();
    map.put(p1,"abc");
    map.put(p2,"efg");
    System.out.println(map.size());//2
}
Person.java
    public class Person{
        private int age;
        private float height;
        private String name;
        //private Car car;
        
        public Person(int age,float height,String name){
            this.age=age;
            this.height=height;
            this.name=name;
        }
        @Override
        public int hashCode(){
            int hashCode = Integer.hashCode(age);
            hashCode = hashCode * 31 + Float.hashCode(height);
            hashCode = hashCode * 31 + (name!=null ? name.hashCode() : 0);
            //hashCode = hashCode * 31 + (car!=null?car.hashCode():0);
            return hashCode;
        }
    }
```

### equals

注意：哈希值一样得出的索引一样，哈希值不一样得出的索引也可能一样

因为哈希的类型不一样，哈希值相等不等于两个对象相等

```java
Person.java
@Override
//用来比较2个对象是否相等
public boolean equals (Object obj){
    if(this == obj)//等号比较内存地址
        return true;
    //if(obj == null || obj.getClass() != getClass())
      //  return flase;
    if(obj == null || !(obj instanceof Person))	//obj是一个person，取反：obj不是一个person
        return false;
    //比较成员变量
    Person person = (Person) obj;
    return person.age == age 
        && person.height == height 
        && (person.name == null ? name == null : person.name.equals(name));
}
```

**obj.getClass() != getClass()**:判断obj的类是不是person类**(更为推荐这样类型)**

**obj instanceof Person**:判断obj对象是不是一个person对象

两者的区别在于如果传入的是person的子类对象student类，getClass()会认为student类不是一个person类，而instanceof会认为student类是父类person类

### clear

```java
HashMap.java
    @SuppressWarnings("unchecked")
    public class HashMap<K,V> implements Map <K,V>{
        private static final boolean RED = false;
        private static final boolean BLACK = true;
        private int size;
        private Node<K,V>[] table;
        private static final DEFAULT_CAPACITY=1<<4;	//16
        public HashMap(){
            table = new Node[DEFAULT_CAPACITY];	//[1<<4]	//给数组默认容量
        }
        @Override
        public int size(){
            return size;
        }
        @Override
        public boolean isEmpty(){
            return size ==0;
        }
        @Override
        public void clear(){	//清空数组
            if(size==0)	//判断数组size是否为0
                return;
            size =0;
            for(int i=0;i<table.length;i++){
                table[i] = null;
            }
        }
        @Override
        public V put(K key,V value){
            int index = index(key);
            //取出index位置的红黑树根节点
            Node<K,V> root = table[index];
            if(root == null){
                root = new Node<>(key,value,null);
                table[index]=root;
                size++;
                afterPut(root);
                return null;
            }
            return null;
            //添加新的节点在红黑树上面
            Node<K,V> parent = root;
            Node<K,V> node = root;
            int cmp = 0;
            do{
                cmp = compare(key,node.key);
                parent = node;
                if(cmp > 0){
                    node = node.right;
                }else if(cmp<0){
                    node = node.left;
                }else{	//相等
                    node.key = key;
                    V oldValue = node.value;
                    node.value = value;
                    return oldValue;
                }
            }while(node!=null);
            //看看插入到父节点哪个位置上面
            Node<K,V> newNode = new Node<>(key,value,parent);
            if(cmp>0){
                parent.right = newNode;
            }else{
                parent.left = newNode;
            }
            size++;
            //新添加节点之后的处理
            afterPut(newNode);
            return null;
        }
        @Override
        public V get(K key){
            return null;
        }
        @Override
        public boolean containsValue(V value){
            return false;
        }
        @Override
        public void traversal(Visitor<K,V> visitor){
            
        }
        private int index(K key){	//根据key生成对应索引
            if(key == null)
                reutrn 0;
            int hash = key.hashCode();
            hash = hash ^ (hash >>> 16);	//无符号右移
            return hash & (table.length-1);
        }
        private static class Node<K,V>{
            K key;
            V Value;
            boolean color=RED;
            Node<K,V> left;
            Node<K,V> right;
            Node<K,V> parent;
            public Node(K key,V value,Node<K,V> parent){
                this.key=key;
                this.value=value;
                this.parent=parent;
            }
        }
    }
```

注意：桶里面的数据如果是红黑树，放的是红黑树的根结点，红黑树的size对于桶来说无用

### compare

#### 基本处理

```java
HashMap.java
    private int cpmpare(K k1,K k2){
    int h1 =  k1 == null ? 0 : k1.hashCode();
    int h2 = k2 == null ? 0 : k2.hashCode();
    return 0;
}
```

### 打印红黑树

```java
HashMap.java
    public void print(){
    if(size == 0)
        return;
    for(int i = 0;i < table.length;i++){
     BinaryTrees.println(new BinaryTreeInfo());   
    }
}
```

