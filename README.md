### 接口
接口不是类，而是对类的一组需求描述。 
接口不能含有实例域，也不能在接口内实现方法。 
接口中所有的方法都是public，所以可以不用写public

因此，可以把接口看作是没有实例域的抽象类
```
//接口范例：任何实现这个接口的类都必须包含compareTo方法
public interface Comparable{
    int compareTo(Object other);
}

class Employee implements Comparable

//关键字implements用来表示实现接口
```


比如Arrays.sort()就要求类必须实现Comparable中的compareTo，类似给c语言的sort函数提供一个函数。

#### 接口的特性
接口不是类，不能new一个接口 
虽然接口不能被new一个对象出来，但是接口类型可以有变量 
接口的变量可以引用实现了接口的类对象

可以类似检查一个对象是否属于特定类一样。也可以使用instanceof检查对象是否实现了某个特定的接口
```
public interface Moveable{}

public interface Powerd extends Moveable{}
//接口之间可以这样互相继承

//接口不能包含实例域和静态方法，但是可以包含常量
public interface Powerd extends Moveable{
    double milesPerGallon();
    double SPEED_LIMIT=95;
}
//接口中的方法自动为public，接口中的域自动为public static final
```


接口可以只定义常量而不定义方法，但是这样的使用偏离了接口设计的初衷，最好不要这样使用。

每个类只能有一个超类，但是可以实现多个接口
#### 对象克隆
如果两个变量引用同一个对象，那么任意一个修改都会影响另外一个变量。 
如果希望两个变量引用两个相同的对象（即克隆），可以使用clone方法。
```
Employee original=new Employee("John Public",50000);
Employee copy=original.clone();
```
clone是Object类的一个proteced方法 
clone默认是浅拷贝（例如域还是指向同一个数据）

如果原始对象和共享对象的子对象是不可变的，那不会产生任何问题。

所以有的时候需要重新定义clone方法
```
//深拷贝
class Employee implements Cloneable{
    public Employee clone() throws CloneNotSupportedException{
        Employee cloned=(Employee)super.clone();
        cloned.hireDay=(Date)hireDay.clone();
        return cloned;
    }
}
//在调用Object的clone函数的基础上，再对可变类进行clone
```

//没错，域也可以克隆
上面那个Cloneable是JAVA的标记接口，只是为了提醒你要实现克隆方法。就像一个标记。

对于子类的克隆也要小心，因为超类的clone不一定适用于子类，要看域的类型。

clone不是解决问题的最佳方式，clone的应用也不是很普遍。

#### throw和throws
throw是主动抛出一个异常，类似python中的raise 
throws代表某函数可能会抛出某种异常

throw出现在函数体 
throws出现在函数名

调用带有throws的函数需要使用try…catch捕捉错误

#### 接口与回调
回调(callback)是一种常见的程序设计模式，在这种模式中，可以指出某个特定事件发生时应该采取的动作

有关Timer和Toolkit的API P247

### 内部类
内部类就是一个类中定义的类

为什么要使用内部类

内部类方法可以访问该类定义的所有域，包括私有域
内部类可以对同一个包中的其他类隐藏起来
定义回调函数时更方便
使用内部类访问对象状态
内部类既可以访问自身的数据域，也可以访问创建它的外围类对象的数据域。

内部类有一个隐式引用，指向了创建它的外部类对象（类似this，不用写却默认有，可以理解为outer.）

编译器会修改所有的内部类的构造器，使其拥有一个外部引用。

注意：outer并不是java关键字，使用这个词只是为了与this进行对比方便理解

内部类的特殊语法规则
```
//内部类引用外部类的非省略形式
OuterClass.this
//例如
TalkingClock.this.beep

//在外部类的作用域之外引用内部类
OuterClass.InnerClass
```
内部类是否有用、必要和安全
内部类与虚拟机无关，内部类由编译器实现 
内部类被用分割成类似TalkingClock分割成类似TalkingClockTimePrinter的常规类文件，对此虚拟机一无所知

很多人认为内部类违背了JAVA的初衷，那就是简单（比C++)，内部类的语法很复杂。

内部类的原理是有一个外部对象的指针。但是真正让内部类强大的是访问权限。你自己也可以弄一个外部类的指针。但是却无法访问内容。这就是内部类的强大之处。

#### 局部内部类
如果一个类用的很少或者只被某方法所使用，那么可以直接在那个方法里定义。

局部内部类不能用private或public修饰，它的作用域被限定在方法内部

由外部方法访问final变量
局部类还可以访问函数的参数，但是必须是final的

#### 匿名内部类
将局部内部类的使用再深入一步 
加入只创建这个类的一个对象，那就不必命名了
```
//举例
public void start(int interval,final boolean beep){
    ActionListener listener=new ActionListener(){
        public void actionPerformed(ActionEvent event){
            Date now=new Date();
            System.out.println("At the tone,the time is"+now);
            if(beep)Toolkit.getDefaultToolkit().beep();
        }
    };
    Timer t=new Timer(interval,listener);
    t.start();
}
```


注意，对匿名子类做getClass的相等测试会失败

匿名数组列表
```
function(new ArrayList<String>() {{add("Harry");add("Tony")}})
```


#### 静态内部类
有时内部类不需要引用外部类，只是想把类隐藏在别的类中。这时可以使用static 
比如避免名称冲突

代码staticInnerClass

#### 代理
代理可以在运行时创建一个实现了一组给定接口的新类 
这个功能只有在编译时无法确定需要实现哪个接口有用

