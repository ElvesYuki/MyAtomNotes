# String为final问题

String类是用final关键字修饰，这说明String不可继承。再看下面，String类的主力成员字段value是个char[ ]数组，而且是用**final**修饰的。final修饰的字段创建以后就不可改变。

再看下面这个**HashSet**用StringBuilder做元素的场景，问题就更严重了，而且更隐蔽。

```java
class Test{    public static void main(String[] args){        HashSet<StringBuilder> hs=new HashSet<StringBuilder>();        StringBuilder sb1=new StringBuilder("aaa");        StringBuilder sb2=new StringBuilder("aaabbb");        hs.add(sb1);        hs.add(sb2);    //这时候HashSet里是{"aaa","aaabbb"}         StringBuilder sb3=sb1;        sb3.append("bbb");  //这时候HashSet里是{"aaabbb","aaabbb"}        System.out.println(hs);    }}//Output://[aaabbb, aaabbb]
```

StringBuilder型变量和分别指向了堆内的字面量和。把他们都插入一个。到这一步没问题。但如果后面我把变量也指向的地址，再改变的值，因为没有不可变性的保护，直接在原先的地址上改。导致的值也变了。这时候，上就出现了两个相等的键值破坏了。所以千万不要用可变类型做

原文在CSDN：https://blog.csdn.net/u013905744/article/details/52414111

 [在java中String类为什么要设计成final？ - u013905744的专栏 - CSDN博客.html](html\在java中String类为什么要设计成final？ - u013905744的专栏 - CSDN博客.html) 