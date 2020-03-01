[//title]:(java内部类)
[//englishTitle]:(java-inner-class)
[//category]:(java)
[//tags]:(java)
[//createTime]:(20200225)
[//lastUpdateTime]:(202002225)

## 概述
内部类是指，一个类是另一个类的成员。java中有四种内部类：

- **内部类(Nested Inner Class)**
- **静态内部类(Static Nested Class)**
- **局部类(Local Inner Class)**
- **匿名类(Anonymou Inner Classe)**

## 为什么使用内部类
使用内部类有几个好处:
- 内部类可以访问外部类包括private的所有元素(成员和方法)。  
- 内部类更具可读性和可维护性，因为它在逻辑上将相关的类/接口内聚在一个地方。  
- 代码优化:内部类引用外部类的元素时，不需要对象来引用，代码可以更简洁。  

## 内部类
下面是一个非静态内部类（nested inner class）示例：  
``` java
class Outer {
    // Simple nested inner class
    class Inner {
        public void show() {
            System.out.println("In a nested class method");
        }
    }
}

class Main {
    public static void main(String[] args) {
        Outer.Inner in = new Outer().new Inner();
        in.show();
    }
}
```

结果：  
``` text
In a nested class method
```

非静态的内部类不能有`static`成员或方法，因为内部类依赖外部类的实例化，所以它不能为自己定义任何静态方法。例如，下面的代码不能编译。  
``` java
class Outer {
    // Simple nested inner class
    class Inner {
        public static void foo() {
            System.out.println("inner class static method");
        }
    }
}
```

编译预检会报错：  
``` text
Inner classes cannot have static declarations
```


非静态内部类可以访问外部类的`static`或`non-static`成员或方法  
``` java
class Outer {
    private String alice = "alice";
    private static String bob = "bob";

    public void sayHi() {
        System.out.println("hi " + alice + " and " + bob);
    }

    class Inner {
        public void innerSayHi() {
            System.out.println("this is " + alice + " and " + bob);
            sayHi();
        }
    }
}

class Main {
    public static void main(String[] args) {
        Outer.Inner inner = new Outer().new Inner();
        inner.innerSayHi();
    }
}
```

结果：  
``` text
this is alice and bob
hi alice and bob
```

## 静态内部类
静态内部类从技术上来说不算一个内部类，它们就像是外部类的静态成员一样。 

静态内部类仅能访问外部类的`static`成员或方法：  
``` java
class Outer {
    private static Double PI = 3.1415926;

    static class Inner {
        public static void accessPi() {
            System.out.println("PI: " + PI);
        }

        public void calculateArea(Double radius) {
            System.out.println("the circle area is: " + PI * radius * radius);
        }
    }
}

class Main {
    public static void main(String[] args) {
        Outer.Inner.accessPi();
        Outer.Inner inner = new Outer.Inner();
        inner.calculateArea(3.0);
    }
```

结果：  
``` text
PI: 3.1415926
the circle area is: 28.274333400000003
```

## 静态和非静态内部类的区别
主要有以下几点：  
* 静态内部类不需要依赖外部类进行实例化，实例化的语法不一样    
``` java
// 非静态内部类实例化  
Outer.Inner inner = new Outer().new Inner();

// 静态内部类实例化  
Outer.StaticInner inner = new Outer.StaticInner();
```
- 静态内部类只能访问外部类的`static`成员，非静态内部类可以访问外部类的`static`或`non-static`成员或方法    
- 静态内部类从技术上来说不算一个内部类，它们就像是外部类的静态成员一样  
- 静态内部类是唯一一个可以在类这个层级使用`static`关键字形容的类  
 
## 局部类
在方法内可以定义一个局部类，类似于局部变量，这个类的使用范围仅限此方法内  
``` java
public class OuterClass {
    public void sayHi() {
        String alice = "alice";
        class Inner {
            public void sayHi() {
                System.out.println("hi " + alice + " from inner");
            }
        }
        Inner obj = new Inner();
        obj.sayHi();
    }

    public static void main(String args[]) {
        OuterClass outerClass = new OuterClass();
        outerClass.sayHi();
    }
}
```

结果：  
``` text
hi alice from inner
```

## 匿名内部类
匿名内部类是一个局部类，只是它没有名字。如果只想使用一次局部类，那可以考虑使用匿名内部类。匿名内部类可以在定义的时候同时初始化，使代码变得更简洁。  

以下是oracle官方示例 [HelloWorldAnonymousClasses](https://docs.oracle.com/javase/tutorial/java/javaOO/examples/HelloWorldAnonymousClasses.java) ，其中EnglishGreeting是局部类，englishGreeting为EnglishGreeting的对象，定义和声明是分开的，而frenchGreeting和spanishGreeting是匿名类的对象，定义和声明是在一起的：    
``` java
public class HelloWorldAnonymousClasses {
  
    interface HelloWorld {
        public void greet();
        public void greetSomeone(String someone);
    }
  
    public void sayHello() {
        
        class EnglishGreeting implements HelloWorld {
            String name = "world";
            public void greet() {
                greetSomeone("world");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Hello " + name);
            }
        }
      
        HelloWorld englishGreeting = new EnglishGreeting();
        
        HelloWorld frenchGreeting = new HelloWorld() {
            String name = "tout le monde";
            public void greet() {
                greetSomeone("tout le monde");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Salut " + name);
            }
        };
        
        HelloWorld spanishGreeting = new HelloWorld() {
            String name = "mundo";
            public void greet() {
                greetSomeone("mundo");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Hola, " + name);
            }
        };
        englishGreeting.greet();
        frenchGreeting.greetSomeone("Fred");
        spanishGreeting.greet();
    }

    public static void main(String... args) {
        HelloWorldAnonymousClasses myApp =
            new HelloWorldAnonymousClasses();
        myApp.sayHello();
    }            
}
```

匿名类是一个表达式。匿名类表达式的语法类似于构造函数的调用，只是在代码块中包含一个类定义。一般都是`new`一个`interface`:  
``` java
        HelloWorld frenchGreeting = new HelloWorld() {
            String name = "tout le monde";
            public void greet() {
                greetSomeone("tout le monde");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Salut " + name);
            }
        };
```

## 参考资料
geeksforgeeks - [inner class in java](https://www.geeksforgeeks.org/inner-class-java/)  
geeksforgeeks - [Static class in Java](https://www.geeksforgeeks.org/static-class-in-java/)  
javatpoint - [Java static nested class](https://www.javatpoint.com/static-nested-class)  
javatpoint - [Java Inner Classes](https://www.javatpoint.com/java-inner-class)  
tutorialsPoint - [What are method local inner classes in Java?](https://www.tutorialspoint.com/What-are-method-local-inner-classes-in-Java)
oracle - [Anonymous Classes](https://docs.oracle.com/javase/tutorial/java/javaOO/anonymousclasses.html)
