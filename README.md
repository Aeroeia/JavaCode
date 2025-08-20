# Java基础

## Java概念

### Java为什么是跨平台的？

Java 能支持跨平台，主要依赖于 JVM 关系比较大。

JVM也是一个软件，不同的平台有不同的版本。我们编写的Java源码，编译后会生成一种 .class 文件，称为字节码文件。Java虚拟机就是负责将字节码文件翻译成特定平台下的机器码然后运行。也就是说，只要在不同平台上安装对应的JVM，就可以运行字节码文件，运行我们编写的Java程序。

而这个过程中，我们编写的Java程序没有做任何改变，仅仅是通过JVM这一”中间层“，就能在不同平台上运行，真正实现了”一次编译，到处运行“的目的。

JVM是一个”桥梁“，是一个”中间件“，是实现跨平台的关键，Java代码首先被编译成字节码文件，再由JVM将字节码文件翻译成机器语言，从而达到运行Java程序的目的。

==编译的结果不是生成机器码，而是生成字节码==，字节码不能直接运行，必须通过JVM翻译成机器码才能运行。**不同平台下编译生成的字节码是一样的，但是由JVM翻译成的机器码却不一样。**

所以，运行Java程序必须有JVM的支持，因为编译的结果不是机器码，必须要经过JVM的再次翻译才能执行。即使你将Java程序打包成可执行文件（例如 .exe），仍然需要JVM的支持。

跨平台的是Java程序，不是JVM。==JVM是用C/C++开发的，是编译后的机器码，不能跨平台==，不同平台下需要安装不同版本的JVM。

### JVM JDK JRE 三者关系

![image-20250813213043561](assert/image-20250813213043561.png)

- JVM是Java虚拟机，是Java程序运行的环境。它负责将Java字节码（由Java编译器生成）解释或编译成机器码，并执行程序。==JVM提供了内存管理、垃圾回收、安全性等功能==，使得Java程序具备跨平台性。
- JDK是Java开发工具包，是开发Java程序所需的工具集合。==它包含了JVM、编译器（javac）、调试器（jdb）等开发工具==，以及一系列的类库（如Java标准库和开发工具库）。JDK提供了开发、编译、调试和运行Java程序所需的全部工具和环境。
- JRE是Java运行时环境，是Java程序运行所需的最小环境。它==包含了JVM和一组Java类库==，用于支持Java程序的执行。==JRE不包含开发工具(编译器、调试器等 )==，只提供Java程序运行所需的运行环境。

### 为什么Java解释和编译都有

首先在Java经过编译之后生成字节码文件，接下来进入JVM中，就有两个步骤编译和解释。 如下图：

![image-20250813213833278](assert/image-20250813213833278.png)

**编译性**：

- Java源代码首先被编译成字节码，JIT 会把编译过的机器码保存起来,以备下次使用。

**解释性：**

- JVM中一个方法调用计数器，当累计计数大于一定值的时候，就使用JIT进行编译生成机器码文件。否则就是用解释器进行解释执行，然后字节码也是经过解释器进行解释运行的。

> JVM 内部有一个 **方法调用计数器（Invocation Counter）**。
>
> - 每当某个方法被调用一次，这个计数器就 +1。
> - 当计数值超过一个阈值（HotSpot 默认大约 **10,000 次**，可配置），JVM 就认为这个方法是“热点方法”。

```
[解释执行]
   ↓
[计数器++] ——→ [计数器 < 阈值] → 继续解释执行
   ↓ 计数器 ≥ 阈值
[JIT 编译] → [生成机器码到Code Cache]
   ↓
[以后直接执行机器码]（更快）

```

##  数据类型

![image-20250815211442893](assert/image-20250815211442893.png)

| 数据类型  | 占用大小（字节）              | 位数       | 取值范围                                                     | 默认值   | 描述                                                         |
| --------- | ----------------------------- | ---------- | ------------------------------------------------------------ | -------- | ------------------------------------------------------------ |
| `byte`    | 1                             | 8          | -128（-2^7） 到 127（2^7 - 1）                               | 0        | 是最小的整数类型，适合用于节省内存，例如在处理文件或网络流时存储小范围整数数据。 |
| `short`   | 2                             | 16         | -32768（-2^15） 到 32767（2^15 - 1）                         | 0        | 较少使用，通常用于在需要节省内存且数值范围在该区间的场景。   |
| `int`     | 4                             | 32         | -2147483648（-2^31） 到 2147483647（2^31 - 1）               | 0        | 最常用的整数类型，可满足大多数日常编程中整数计算的需求。     |
| `long`    | 8                             | 64         | -9223372036854775808（-2^63） 到 9223372036854775807（2^63 - 1） | 0L       | 用于表示非常大的整数，当 `int` 类型无法满足需求时使用，定义时数值后需加 `L` 或 `l`。 |
| `float`   | 4                             | 32         | 1.4E - 45 到 3.4028235E38                                    | 0.0f     | 单精度浮点数，用于表示小数，精度相对较低，定义时数值后需加 `F` 或 `f`。 |
| `double`  | 8                             | 64         | 4.9E - 324 到 1.7976931348623157E308                         | 0.0d     | 双精度浮点数，精度比 `float` 高，是 Java 中表示小数的默认类型。 |
| `char`    | 2                             | 16         | '\u0000'（0） 到 '\uffff'（65535）                           | '\u0000' | 用于表示单个字符，采用 Unicode 编码，可表示各种语言的字符。  |
| `boolean` | 无明确字节大小（理论上 1 位） | 无明确位数 | `true` 或 `false`                                            | `false`  | 用于逻辑判断，只有两个取值，常用于条件判断和循环控制等逻辑场景。 |

![image-20250815211817685](assert/image-20250815211817685.png)

### 为什么要有Integer

1. 一个非常重要的原因就是在Java中绝大部分方法或类都是用来处理类类型对象的，如ArrayList集合类就只能以类作为他的存储对象，而这时如果想把一个int型的数据存入list是不可能的，必须把它包装成类，也就是Integer才能被List所接受。所以Integer的存在是很必要的 

2. 在Java中，基本类型和引用类型不能直接进行转换，必须使用包装类来实现。例如，将一个int类型的值转换为String类型，必须首先将其转换为Integer类型，然后再转换为String类型。

   *注：如果使用String.valueOf()并不需要经过Integer装箱 而是char数组转换*

### 为什么要保留int

1. int是一种基本数据类型，而Integer是一种引用类型。基本数据类型是Java中最基本的数据类型，它们是预定义的，不需要实例化就可以使用。而引用类型则需要通过实例化对象来使用。这意味着，使用int来存储一个整数时，不需要任何额外的内存分配，而使用Integer时，必须为对象分配内存。在性能方面，基本数据类型的操作通常比相应的引用类型快。

2. 包装类是引用类型，对象的引用和对象本身是分开存储的，而对于基本类型数据，变量对应的内存块直接存储数据本身。

   因此，基本类型数据在读写效率方面，要比包装类高效。除此之外，在64位JVM上，在开启引用压缩的情况下，一个Integer对象占用16个字节的内存空间，而一个int类型数据只占用4字节的内存空间，前者对空间的占用是后者的4倍。

   也就是说，不管是读写效率，还是存储效率，基本类型都比包装类高效。

### 缓存

Java的Integer类内部实现了一个静态缓存池，用于存储特定范围内的整数值对应的Integer对象。

默认情况下，这个范围是-128至127。当通过Integer.valueOf(int)方法创建一个在这个范围内的整数对象时，并不会每次都生成新的对象实例，而是复用缓存中的现有对象，会直接从内存中取出，不需要新建一个对象。

##  面向对象

###  Java面向对象的三大特性包括：**封装、继承、多态**：

- **封装**：封装是指将对象的属性（数据）和行为（方法）结合在一起，对外隐藏对象的内部细节，仅通过对象提供的接口与外界交互。封装的目的是增强安全性和简化编程，使得对象更加独立。
- **继承**：继承是一种可以使得子类自动共享父类数据结构和方法的机制。它是代码复用的重要手段，通过继承可以建立类与类之间的层次关系，使得结构更加清晰。
- **多态**：多态是指允许不同类的对象对同一消息作出响应。即同一个接口，使用不同的实例而执行不同操作。多态性可以分为编译时多态（重载）和运行时多态（重写）。它使得程序具有良好的灵活性和扩展性。

### 面向对象编程中的六大原则：

- **单一职责原则（SRP）**：一个类应该只有一个引起它变化的原因，即一个类应该==只负责一项职责==。例子：考虑一个员工类，它应该只负责管理员工信息，而不应负责其他无关工作。
- **开放封闭原则（OCP）**：软件实体应该==对扩展开放，对修改封闭==。例子：通过制定接口来实现这一原则，比如定义一个图形类，然后让不同类型的图形继承这个类，而不需要修改图形类本身。
- **里氏替换原则（LSP）**：子类对象应该能够==替换掉所有父类对象==。例子：一个正方形是一个矩形，但如果修改一个矩形的高度和宽度时，正方形的行为应该如何改变就是一个违反里氏替换原则的例子。==不能违反契约==
- **接口隔离原则（ISP）**：客户端不应该依赖那些它不需要的接口，即接口应该==小而专==。例子：通过接口抽象层来实现底层和高层模块之间的解耦，比如使用依赖注入。
- **依赖倒置原则（DIP）**：==高层模块不应该依赖低层模块==，二者都应该依赖于抽象；抽象不应该依赖于细节，细节应该依赖于抽象。例子：如果一个公司类包含部门类，应该考虑使用合成/聚合关系，而不是将公司类继承自部门类。
- **迪米特法则(Law of Demeter)**：一个对象应当对其他对象有最少的了解，只与其直接的朋友交互。

### Java抽象类和接口的区别是什么？

**两者的特点：**

- 抽象类用于描述类的共同特性和行为，==可以有成员变量、构造方法和具体方法==。适用于有明显继承关系的场景。
- 接口用于定义行为规范，可以多实现，==只能有常量和抽象方法（Java 8 以后可以有默认方法和静态方法）==。适用于定义类的能力或功能。

**两者的区别：**

- 实现方式：实现接口的关键字为implements，继承抽象类的关键字为extends。一个类可以实现多个接口，但一个类只能继承一个抽象类。所以，使用接口可以间接地实现多重继承。
- 方法方式：接口只有定义，不能有方法的实现，java 1.8中可以定义default方法体，而抽象类可以有定义与实现，方法可在抽象类中实现。
- 访问修饰符：==接口成员变量默认为public static final，必须赋初值，不能被修改==；其所有的成员方法都是==public、abstract==的。抽象类中成员变量默认default，可在子类中被重新定义，也可被重新赋值；抽象方法被abstract修饰，不能被private、static、synchronized和native等修饰，必须以分号结尾，不带花括号。
- 变量：抽象类可以包含实例变量和静态变量，而接口只能包含常量（即静态常量）。

### 接口里面可以定义哪些方法？

- **抽象方法**

抽象方法是接口的核心部分，所有实现接口的类都必须实现这些方法。抽象方法默认是 public 和 abstract，这些修饰符可以省略。

```java
public interface Animal {
    void makeSound();
}
```

- **默认方法**

默认方法是在 Java 8 中引入的，允许接口提供具体实现。实现类可以选择重写默认方法。

```java
public interface Animal {
    void makeSound();
    
    default void sleep() {
        System.out.println("Sleeping...");
    }
}
```

- **静态方法**

静态方法也是在 Java 8 中引入的，它们属于接口本身，可以通过接口名直接调用，而不需要实现类的对象。

```java
public interface Animal {
    void makeSound();
    
    static void staticMethod() {
        System.out.println("Static method in interface");
    }
}
```

- **私有方法**

私有方法是在 Java 9 中引入的，用于在接口中为默认方法或其他私有方法提供辅助功能。这些方法不能被实现类访问，只能在接口内部使用。

```java
public interface Animal {
    void makeSound();
    
    default void sleep() {
        System.out.println("Sleeping...");
        logSleep();
    }
    
    private void logSleep() {
        System.out.println("Logging sleep");
    }
}
public interface Animal {
    void makeSound();
}
```

==在接口中，不可以有构造方法,==在接口里写入构造方法时，编译器提示：Interfaces cannot have constructors，因为接口不会有自己的实例的，所以不需要有构造函数。 

#### 抽象类可以被实例化吗？

在Java中，抽象类本身不能被实例化。

这意味着不能使用`new`关键字直接创建一个抽象类的对象。抽象类的存在主要是为了被继承，它通常包含一个或多个抽象方法（由`abstract`关键字修饰且无方法体的方法），这些方法需要在子类中被实现。

==抽象类可以有构造器，这些构造器在子类实例化时会被调用==，以便进行必要的初始化工作。然而，这个过程并不是直接实例化抽象类，而是创建了子类的实例，间接地使用了抽象类的构造器。

```java
public abstract class AbstractClass {
    public AbstractClass() {
        // 构造器代码
    }
    
    public abstract void abstractMethod();
}

public class ConcreteClass extends AbstractClass {
    public ConcreteClass() {
        super(); // 调用抽象类的构造器
    }
    
    @Override
    public void abstractMethod() {
        // 实现抽象方法
    }
}

// 下面的代码可以运行
ConcreteClass obj = new ConcreteClass();
```

在这个例子中，`ConcreteClass`继承了`AbstractClass`并实现了抽象方法`abstractMethod()`。当我们创建`ConcreteClass`的实例时，`AbstractClass`的构造器被调用，但这并不意味着`AbstractClass`被实例化；实际上，我们创建的是`ConcreteClass`的一个对象。

简而言之，抽象类不能直接实例化，但通过继承抽象类并实现所有抽象方法的子类是可以被实例化的。

#### Java中的静态变量和静态方法

在Java中，静态变量和静态方法是与类本身关联的，而不是与类的实例（对象）关联。它们在内存中只存在一份，可以被类的所有实例共享。

- **共享性**：所有该类的实例共享同一个静态变量。如果一个实例修改了静态变量的值，其他实例也会看到这个更改。

- **初始化**：静态变量在类被加载时初始化，只会对其进行一次分配内存。

- **访问方式**：静态变量可以直接通过类名访问，也可以通过实例访问，但推荐使用类名。

- **无实例依赖**：静态方法可以在没有创建类实例的情况下调用。对于静态方法来说，==不能直接访问非静态的成员变量或方法，因为静态方法没有上下文的实例。==

- **访问静态成员**：静态方法可以直接调用其他静态变量和静态方法，但不能直接访问非静态成员。

- **多态性**：==静态方法不支持重写（Override），但可以被隐藏（Hide）==。

  ```java
  class Parent {
      static void hello() {
          System.out.println("Parent hello");
      }
  }
  
  class Child extends Parent {
      static void hello() {
          System.out.println("Child hello");
      }
  }
  
  public class Test {
      public static void main(String[] args) {
          Parent p = new Child();
          Parent.hello();  // 输出 Parent hello
          Child.hello();   // 输出 Child hello
          p.hello();       // 输出 Parent hello，因为 p 的类型是 Parent
      }
  }
  
  ```

#### 非静态内部类和静态内部类的区别？

- 非静态内部类==依赖于外部类的实例==，而静态内部类==不依赖于外部类的实例==。

- 非静态内部类可以==访问外部类的实例变量和方法==，而静态内部类只能访问外部类的==静态成员==。

- 非静态内部类不能定义静态成员，而静态内部类可以定义静态成员。

- 非静态内部类在外部类实例化后才能实例化，而静态内部类可以独立实例化。

- 非静态内部类==可以访问外部类的私有成员==，而静态内部类不能直接访问外部类的私有成员，需要通过实例化外部类来访问。

  ```java
  class Outer {
      private int x = 10;
      private static int y = 20;
  
      static class Inner {
          void print() {
              // System.out.println(x); // ❌ 错误，不能直接访问实例成员
              Outer outer = new Outer();       // 先创建外部类实例
              System.out.println(outer.x);     // ✅ 通过外部类对象访问
  
              System.out.println(Outer.y);     // ✅ 可以直接访问静态成员
          }
      }
  }
  
  public class Test {
      public static void main(String[] args) {
          Outer.Inner inner = new Outer.Inner();
          inner.print();
      }
  }
  
  ```

#### 非静态内部类可以直接访问外部方法，编译器是怎么做到的？

非静态内部类可以直接访问外部方法是因为编译器在生成字节码时会为非静态内部类==维护一个指向外部类实例的引用==。

这个引用使得非静态内部类能够访问外部类的实例变量和方法。编译器会在生成非静态内部类的构造方法时，将外部类实例作为参数传入，并在内部类的实例化过程中建立外部类实例与内部类实例之间的联系，从而实现直接访问外部方法的功能。

## 深拷贝

==实现深拷贝三种方式是什么？==

### 1. 实现 Cloneable 接口并重写 clone() 方法

这种方法要求对象及其所有引用类型字段都实现 Cloneable 接口，并且重写 clone() 方法。在 clone() 方法中，通过递归克隆引用类型字段来实现深拷贝。

```java
class MyClass implements Cloneable {
    private String field1;
    private NestedClass nestedObject;

    @Override
    protected Object clone() throws CloneNotSupportedException {
        MyClass cloned = (MyClass) super.clone();
        cloned.nestedObject = (NestedClass) nestedObject.clone(); // 深拷贝内部的引用对象
        return cloned;
    }
}

class NestedClass implements Cloneable {
    private int nestedField;

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

### 2. 使用序列化和反序列化

通过将对象序列化为字节流，再从字节流反序列化为对象来实现深拷贝。要求对象及其所有引用类型字段都实现 Serializable 接口。

```java
import java.io.*;

class MyClass implements Serializable {
    private String field1;
    private NestedClass nestedObject;

    public MyClass deepCopy() {
        try {
            ByteArrayOutputStream bos = new ByteArrayOutputStream();
            ObjectOutputStream oos = new ObjectOutputStream(bos);
            oos.writeObject(this);
            oos.flush();
            oos.close();

            ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
            ObjectInputStream ois = new ObjectInputStream(bis);
            return (MyClass) ois.readObject();
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
            return null;
        }
    }
}

class NestedClass implements Serializable {
    private int nestedField;
}
```

### 3. 手动递归复制

针对特定对象结构，手动递归复制对象及其引用类型字段。适用于对象结构复杂度不高的情况。

```java
class MyClass {
    private String field1;
    private NestedClass nestedObject;

    public MyClass deepCopy() {
        MyClass copy = new MyClass();
        copy.setField1(this.field1);
        copy.setNestedObject(this.nestedObject.deepCopy());
        return copy;
    }
}

class NestedClass {
    private int nestedField;

    public NestedClass deepCopy() {
        NestedClass copy = new NestedClass();
        copy.setNestedField(this.nestedField);
        return copy;
    }
}
```

## 对象

### 创建对象的方式有哪些？

**使用new关键字**：通过new关键字直接调用类的构造方法来创建对象。

```java
MyClass obj = new MyClass();
```

**使用Class类的newInstance()方法**：通过反射机制，可以使用Class类的newInstance()方法创建对象。

```java
MyClass obj = (MyClass) Class.forName("com.example.MyClass").newInstance();
```

**使用Constructor类的newInstance()方法**：同样是通过反射机制，可以使用Constructor类的newInstance()方法创建对象。

```java
Constructor<MyClass> constructor = MyClass.class.getConstructor();
MyClass obj = constructor.newInstance();
```

**使用clone()方法**：如果类实现了Cloneable接口，可以使用clone()方法复制对象。

```java
MyClass obj1 = new MyClass();
MyClass obj2 = (MyClass) obj1.clone();
```

**使用反序列化**：通过将对象序列化到文件或流中，然后再进行反序列化来创建对象。

```java
// SerializedObject.java
ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("object.ser"));
out.writeObject(obj);
out.close();

// DeserializedObject.java
ObjectInputStream in = new ObjectInputStream(new FileInputStream("object.ser"));
MyClass obj = (MyClass) in.readObject();
in.close();
```

### new出来的对象什么时候回收？

通过过关键字`new`创建的对象，由Java的垃圾回收器（Garbage Collector）负责回收。垃圾回收器的工作是在程序运行过程中自动进行的，它会周期性地检测不再被引用的对象，并将其回收释放内存。

具体来说，Java对象的回收时机是由垃圾回收器根据一些算法来决定的，主要有以下几种情况：

1. **引用计数法：**某个对象的引用计数为0时，表示该对象不再被引用，可以被回收。
2. **可达性分析算法：**从根对象（如方法区中的类静态属性、方法中的局部变量等）出发，通过对象之间的引用链进行遍历，如果存在一条引用链到达某个对象，则说明该对象是可达的，反之不可达，不可达的对象将被回收。
3. **终结器（Finalizer）：**如果对象重写了`finalize()`方法，垃圾回收器会在回收该对象之前调用`finalize()`方法，对象可以在`finalize()`方法中进行一些清理操作。然而，终结器机制的使用不被推荐，因为它的执行时间是不确定的，可能会导致不可预测的性能问题。

### 如何获取私有对象？

1. 使用公共访问器方法（getter 方法）：如果类的设计者遵循良好的编程规范，通常会为私有成员变量提供公共的访问器方法（即 `getter` 方法），通过调用这些方法可以安全地获取私有对象

2. 反射机制。反射机制允许在运行时检查和修改类、方法、字段等信息，通过反射可以绕过 `private` 访问修饰符的限制来获取私有对象

   ```java
   import java.lang.reflect.Field;
   
   class MyClass {
       private String privateField = "私有字段的值";
   }
   
   public class Main {
       public static void main(String[] args) throws NoSuchFieldException, IllegalAccessException {
           MyClass obj = new MyClass();
           // 获取 Class 对象
           Class<?> clazz = obj.getClass();
           // 获取私有字段
           Field privateField = clazz.getDeclaredField("privateField");
           // 设置可访问性
           privateField.setAccessible(true);
           // 获取私有字段的值
           String value = (String) privateField.get(obj);
           System.out.println(value); 
       }
   }
   ```

## 反射

Java 反射机制是在运行状态中，对于任意一个类，都能够知道这个类中的所有属性和方法，对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为 Java 语言的反射机制。

反射具有以下特性：

1. **运行时类信息访问**：反射机制允许程序在运行时获取类的==完整结构信息==，包括类名、包名、父类、实现的接口、构造函数、方法和字段等。
2. **动态对象创建**：可以使用反射API动态地创建对象实例，即使在编译时不知道具体的类名。这是通过Class类的==newInstance()方法==或Constructor对象的newInstance()方法实现的。
3. **动态方法调用**：可以在运行时动态地==调用对象的方法==，包括私有方法。这通过==Method类的invoke()方法==实现，允许你传入对象实例和参数值来执行方法。
4. **访问和修改字段值**：反射还允许程序在运行时访问和修改对象的字段值，即使是私有的。这是通过==Field类的get()和set()方法==完成的。

![image-20250818233815977](assets/image-20250818233815977.png)

==为什么反射机制能获取类或对象所有信息 怎么做到的==

- 编译后的字节码保留了完整类元信息。

- JVM 在运行时加载类并保存元数据。

- `java.lang.reflect` API 提供了访问这些元数据的能力。

- JVM 内部可以访问内存对象，所以可以读/写字段和调用方法。

==那为什么c++不能有这种反射机制==

### 1️⃣ 编译时 vs 运行时

- **C++** 是静态编译语言，类型信息在编译阶段就确定了，编译器会将类、方法和字段的元信息直接编译进二进制代码，但不会保留完整的字段名、方法名、类型信息等元数据。
- **Java** 是半动态语言，编译后的字节码保留了完整的类元数据，JVM 可以在运行时访问这些信息。

**结论**：C++ 编译后的程序为了效率和体积，不保留足够的元信息，运行时无法像 Java 那样“问类它有啥字段/方法”。

### 2️⃣ 内存布局不同

- C++ 对象在内存中是紧凑布局的，字段的名字和类型信息只在编译器内部使用，不存储在运行时内存中。
- Java 对象在 JVM 堆中有对象头，类的元数据在方法区/元空间中保存，因此可以通过指针找到字段、方法信息。

| 特性         | C++                  | Java                      |
| ------------ | -------------------- | ------------------------- |
| 对象内存位置 | 栈/堆/全局区，OS管理 | JVM堆，JVM管理            |
| 内存布局     | 编译期固定           | JVM运行期可查询元数据     |
| 字段名信息   | 编译期存在，运行期无 | 运行期存在于元数据中      |
| 执行方式     | 本地机器码           | 字节码 → JVM解释或JIT编译 |

## 注解

注解解析的底层实现主要依赖于 Java 的反射机制和字节码文件的存储。通过 `@Retention` 元注解可以控制注解的保留策略，当使用 `RetentionPolicy.RUNTIME` 时，可以在运行时通过反射 API 来解析注解信息。在 JVM 层面，会从字节码文件中读取注解信息，并创建注解的代理对象来获取注解的属性值。



注解本质是一个继承了Annotation的特殊接口，其具体实现类是Java运行时生成的动态代理类。

我们通过反射获取注解时，返回的是Java运行时生成的动态代理对象。通过代理对象调用自定义注解的方法，会最终调用AnnotationInvocationHandler的invoke方法。该方法会从memberValues这个Map中索引出对应的值。而memberValues的来源是Java常量池。

在 **Java 反射机制**里，`invoke` 的作用就是 **在运行时调用某个方法**，不管你在编译时是否知道方法具体是谁

## 异常

![image-20250819020856904](assets/image-20250819020856904.png)

Java的异常体系主要基于两大类：Throwable类及其子类。Throwable有两个重要的子类：Error和Exception，它们分别代表了不同类型的异常情况。

1. **Error（错误）**：表示运行时环境的错误。错误是程序无法处理的严重问题，如系统崩溃、虚拟机错误、动态链接失败等。通常，程序不应该尝试捕获这类错误。例如，OutOfMemoryError、StackOverflowError等。
2. **Exception（异常）**：表示程序本身可以处理的异常条件。异常分为两大类：
   - **非运行时异常**：这类异常在编译时期就必须被捕获或者声明抛出。它们通常是外部错误，如文件不存在（FileNotFoundException）、类未找到（ClassNotFoundException）等。非运行时异常强制程序员处理这些可能出现的问题，增强了程序的健壮性。
   - **运行时异常**：这类异常包括运行时异常（RuntimeException）和错误（Error）。运行时异常由程序错误导致，如空指针访问（NullPointerException）、数组越界（ArrayIndexOutOfBoundsException）等。运行时异常是不需要在编译时强制捕获或声明的。

**抛出异常为什么不用throws？**

如果异常是未检查异常或者在方法内部被捕获和处理了，那么就不需要使用throws。

- **Unchecked Exceptions**：未检查异常（unchecked exceptions）是继承自RuntimeException类或Error类的异常，编译器不强制要求进行异常处理。因此，对于这些异常，不需要在方法签名中使用throws来声明。示例包括NullPointerException、ArrayIndexOutOfBoundsException等。
- **捕获和处理异常**：另一种常见情况是，在方法内部捕获了可能抛出的异常，并在方法内部处理它们，而不是通过throws子句将它们传递到调用者。这种情况下，方法可以处理异常而无需在方法签名中使用throws。

**try catch中的语句运行情况**

try块中的代码将按顺序执行，如果抛出异常，将在catch块中进行匹配和处理，然后程序将继续执行catch块之后的代码。如果没有匹配的catch块，异常将被传递给上一层调用的方法。

**try{return “a”} fianlly{return “b”}这条语句返回啥**

finally块中的return语句会覆盖try块中的return返回，因此，该语句将返回"b"。

## Object

### hashcode和equals方法有什么关系？

在 Java 中，对于重写 `equals` 方法的类，通常也需要重写 `hashCode` 方法，并且需要遵循以下规定：

- **一致性**：如果两个对象使用 `equals` 方法比较结果为 `true`，那么它们的 `hashCode` 值必须相同。也就是说，如果 `obj1.equals(obj2)` 返回 `true`，那么 `obj1.hashCode()` 必须等于 `obj2.hashCode()`。
- **非一致性**：如果两个对象的 `hashCode` 值相同，它们使用 `equals` 方法比较的结果不一定为 `true`。即 `obj1.hashCode() == obj2.hashCode()` 时，`obj1.equals(obj2)` 可能为 `false`，这种情况称为哈希冲突。

`hashCode` 和 `equals` 方法是紧密相关的，重写 `equals` 方法时必须重写 `hashCode` 方法，以保证在使用哈希表等数据结构时，对象的相等性判断和存储查找操作能够正常工作。而重写 `hashCode` 方法时，需要确保相等的对象具有相同的哈希码，但相同哈希码的对象不一定相等

哈希表查找对象的流程是这样的：

1. 先调用对象的 `hashCode()` 算出哈希值，定位到某个桶（bucket）。
2. 再在这个桶里用 `equals()` 判断是否“真正相等”。

==👉 所以 `hashCode` 决定 **去哪个桶**，`equals` 决定 **桶里比对是否相等**。==

### String、StringBuffer、StringBuilder的区别和联系

**1、可变性** ：`String` 是不可变的（Immutable），一旦创建，内容无法修改，每次修改都会生成一个新的对象。`StringBuilder` 和 `StringBuffer` 是可变的（Mutable），可以直接对字符串内容进行修改而不会创建新对象。

**2、线程安全性** ：`String` 因为不可变，天然线程安全。`StringBuilder` 不是线程安全的，适用于单线程环境。`StringBuffer` 是线程安全的，其方法通过 `synchronized` 关键字实现同步，适用于多线程环境。

**3、性能** ：`String` 性能最低，尤其是在频繁修改字符串时会生成大量临时对象，增加内存开销和垃圾回收压力。`StringBuilder` 性能最高，因为它没有线程安全的开销，适合单线程下的字符串操作。`StringBuffer` 性能略低于 `StringBuilder`，因为它的线程安全机制引入了同步开销。

**4、使用场景** ：如果字符串内容固定或不常变化，优先使用 `String`。如果需要频繁修改字符串且在单线程环境下，使用 `StringBuilder`。如果需要频繁修改字符串且在多线程环境下，使用 `StringBuffer`。

对比总结如下：

| **特性**     | **String**       | **StringBuilder** | **StringBuffer** |
| ------------ | ---------------- | ----------------- | ---------------- |
| **不可变性** | 不可变           | 可变              | 可变             |
| **线程安全** | 是（因不可变）   | 否                | 是（同步方法）   |
| **性能**     | 低（频繁修改时） | 高（单线程）      | 中（多线程安全） |
| **适用场景** | 静态字符串       | 单线程动态字符串  | 多线程动态字符串 |

**StringBuilder线程不安全例子**

```java
public class StringBuilderTest {
    public static void main(String[] args) throws InterruptedException {
        StringBuilder sb = new StringBuilder();

        Runnable task = () -> {
            for (int i = 0; i < 1000; i++) {
                sb.append("a");
            }
        };

        Thread t1 = new Thread(task);
        Thread t2 = new Thread(task);

        t1.start();
        t2.start();
        t1.join();//表示：当前线程要等 t1 执行完才继续往下走。
        t2.join();//表示：当前线程要等 t2 执行完才继续往下走。

        // 预期：2000 个 'a'
        System.out.println("最终长度: " + sb.length());
    }
}

```

```
最终长度: 1789
```

**String相关**

✅ 直接赋值（字面量方式）

```
String s1 = "hello";
```

- 编译器在编译时就会把 `"hello"` 放入 **字符串常量池**（方法区/元空间中专门的一块区域）。
- 执行时，JVM 先在常量池里找 `"hello"`：
  - 如果存在 → 直接返回引用。
  - 如果不存在 → 新建并放入常量池。
- 所以 **不会在堆中新建对象**，直接用常量池里的。

👉 所以这句只会有 **常量池里的一个对象**。

------

✅ 使用 `new` 关键字

```
String s2 = new String("hello");
```

执行流程是：

1. `"hello"` 这个字面量先去常量池找，如果常量池没有，则放进去。
2. `new` 关键字会 **在堆里再创建一个新的对象**，内容和常量池的 `"hello"` 一样。
3. `s2` 引用指向的是堆中的这个新对象。

👉 所以这句通常会有 **两个对象**：常量池中的 `"hello"` + 堆中的新建对象。

## Java新特性

| **特性名称**                 | **描述**                                                     | **示例或说明**                                               |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Lambda 表达式**            | 简化匿名内部类，支持函数式编程                               | `(a, b) -> a + b` 代替匿名类实现接口                         |
| **函数式接口**               | 仅含一个抽象方法的接口，可用 `@FunctionalInterface` 注解标记 | `Runnable`, `Comparator`, 或自定义接口 `@FunctionalInterface interface MyFunc { void run(); }` |
| **Stream API**               | 提供链式操作处理集合数据，支持并行处理                       | `list.stream().filter(x -> x > 0).collect(Collectors.toList())` |
| **Optional 类**              | 封装可能为 `null` 的对象，减少空指针异常                     | `Optional.ofNullable(value).orElse("default")`               |
| **方法引用**                 | 简化 Lambda 表达式，直接引用现有方法                         | `System.out::println` 等价于 `x -> System.out.println(x)`    |
| **接口的默认方法与静态方法** | 接口可定义默认实现和静态方法，增强扩展性                     | `interface A { default void print() { System.out.println("默认方法"); } }` |
| **并行数组排序**             | 使用多线程加速数组排序                                       | `Arrays.parallelSort(array)`                                 |
| **重复注解**                 | 允许同一位置多次使用相同注解                                 | `@Repeatable` 注解配合容器注解使用                           |
| **类型注解**                 | 注解可应用于更多位置（如泛型、异常等）                       | `List<@NonNull String> list`                                 |
| **CompletableFuture**        | 增强异步编程能力，支持链式调用和组合操作                     | `CompletableFuture.supplyAsync(() -> "result").thenAccept(System.out::println)` |

### Lambda 表达式了解吗？

Lambda 表达式它是一种简洁的语法，用于创建匿名函数，主要用于简化函数式接口（只有一个抽象方法的接口）的使用。其基本语法有以下两种形式：

- `(parameters) -> expression`：当 Lambda 体==只有一个表达式时==使用，表达式的结果会作为返回值。
- `(parameters) -> { statements; }`：当 Lambda 体==包含多条语句时==，需要使用大括号将语句括起来，若有返回值则需要使用 `return` 语句。

传统的匿名内部类实现方式代码较为冗长，而 Lambda 表达式可以用更简洁的语法实现相同的功能。比如，使用==匿名内部类实现 `Runnable` 接口==

### Stream流的并行API是什么？

是 ==ParallelStream==。

并行流（ParallelStream）就是将源数据==分为多个子流对象进行多线程操作，然后将处理的结果再汇总为一个流对象==，底层是使用通用的 ==fork/join 池==来实现，即将一个任务拆分成多个“小任务”并行计算，再把多个“小任务”的结果合并成总的计算结果

Stream串行流与并行流的主要区别：

![image-20250819153156263](assets/image-20250819153156263.png)

在 `ParallelStream` 里，JDK 解决并发问题 **主要靠“分而治之（任务拆分）”+“避免共享状态”**

**直接用 `parallelStream()`**

```java
List<String> list = Arrays.asList("a", "b", "c", "d", "e");

// 转换成并行流
list.parallelStream()
    .forEach(s -> System.out.println(Thread.currentThread().getName() + " -> " + s));

//输出示例
ForkJoinPool.commonPool-worker-3 -> a
main -> b
ForkJoinPool.commonPool-worker-5 -> c
ForkJoinPool.commonPool-worker-7 -> d
...

```

**普通 stream 转并行**

```java
list.stream()
    .parallel()
    .forEach(System.out::println);

```

**并行流转普通流**

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);

list.parallelStream()        // 得到并行流
    .map(i -> i * i)
    .sequential()            // 转换为普通串行流
    .forEach(System.out::println);

```

### completableFuture怎么用的？

CompletableFuture是由Java 8引入的，在Java8之前我们一般通过Future实现异步。

**Future实现异步：** ==阻塞== 

```java
import java.util.concurrent.*;

public class FutureDemo {
    public static void main(String[] args) throws Exception {
        ExecutorService executor = Executors.newSingleThreadExecutor();

        // 提交一个异步任务，返回 Future
        Future<Integer> future = executor.submit(() -> {
            System.out.println("任务开始执行...");
            Thread.sleep(2000);  // 模拟耗时
            return 42;
        });

        System.out.println("主线程继续执行其他逻辑...");

        // 阻塞等待结果（任务没完成就会阻塞在这里）
        Integer result = future.get();

        System.out.println("异步任务结果：" + result);

        executor.shutdown();
    }
}

//结果
主线程继续执行其他逻辑...
任务开始执行...
异步任务结果：42

```

**CompletableFuture：** ==非阻塞回调==

```java
import java.util.concurrent.*;

public class CompletableFutureDemo {
    public static void main(String[] args) throws Exception {
        CompletableFuture<Integer> cf = CompletableFuture.supplyAsync(() -> {
            System.out.println("任务开始执行...");
            try { Thread.sleep(2000); } catch (InterruptedException e) {}
            return 42;
        });

        // 非阻塞回调，结果出来后自动触发
        cf.thenAccept(result -> {
            System.out.println("异步任务结果：" + result);
        });

        System.out.println("主线程继续执行，不阻塞...");
        
        Thread.sleep(3000); // 等一下，防止主线程过早结束
    }
}

//输出结果
主线程继续执行，不阻塞...
任务开始执行...
异步任务结果：42

```

==但get/join方法其实也是阻塞的  CompletableFuture通过异步回调实现非阻塞==

```
[ CompletableFuture 容器 ]
        |
        |-- 状态: 未完成 --> 阻塞 get/join
        |-- 状态: 完成(值) --> get() 返回这个值
        |-- 状态: 异常 --> get() 抛异常

```

#### 回调地狱

“回调地狱”并不是说程序**卡住、阻塞**，它跟线程阻塞没关系。

它的本质是：

- **代码嵌套太深** → 逻辑层层套，每增加一个异步操作就右移一层
- **可读性差、难维护** → 异常处理、调试都变麻烦
- **容易出错** → 嵌套过多可能忘记处理某些分支或异常

**回调地狱**

```java
CompletableFuture.supplyAsync(() -> task1()).thenAccept(result1 -> {
    CompletableFuture.supplyAsync(() -> task2(result1)).thenAccept(result2 -> {
        CompletableFuture.supplyAsync(() -> task3(result2)).thenAccept(result3 -> {
            CompletableFuture.supplyAsync(() -> task4(result3)).thenAccept(result4 -> {
                System.out.println(result4);
            });
        });
    });
});

```

**链式编程**

```java
CompletableFuture.supplyAsync(() -> task1())
    .thenApply(result1 -> task2(result1))
    .thenApply(result2 -> task3(result2))
    .thenApply(result3 -> task4(result3))
    .thenAccept(finalResult -> System.out.println(finalResult));

```

### Java 21 新特性知道哪些？

**新新语言特性：**

- **Switch 语句的模式匹配**：该功能在 Java 21 中也得到了增强。它允许在`switch`的`case`标签中使用模式匹配，使操作更加灵活和类型安全，减少了样板代码和潜在错误。

  ```java
  switch (obj) {
      case String s && s.length() > 3 -> System.out.println("长字符串: " + s);
      case String s -> System.out.println("短字符串: " + s);
      case Integer i -> System.out.println("整数: " + i);
      default -> System.out.println("其它类型");
  }
  ```

  

- **数组模式**：将模式匹配扩展到数组中，使开发者能够在条件语句中更高效地解构和检查数组内容。例如，`if (arr instanceof int[] {1, 2, 3})`，可以直接判断数组`arr`是否匹配指定的模式。

  ```java
  //传统写法
  int[] arr = {1, 2, 3};
  if (arr.length == 3 && arr[0] == 1 && arr[1] == 2 && arr[2] == 3) {
      System.out.println("数组匹配 [1,2,3]");
  }
  
  ```

- **字符串模板（预览版）**：提供了一种更可读、更易维护的方式来构建复杂字符串，支持在字符串字面量中直接嵌入表达式。例如，以前可能需要使用`"hello " + name + ", welcome to the geeksforgeeks!"`这样的方式来拼接字符串，在 Java 21 中可以使用`hello {name}, welcome to the geeksforgeeks!`这种更简洁的写法

**新并发特性方面：**

- **虚拟线程**：这是 Java 21 引入的一种轻量级并发的新选择。它通过共享堆栈的方式，大大降低了内存消耗，同时提高了应用程序的吞吐量和响应速度。可以使用静态构建方法、构建器或`ExecutorService`来创建和使用虚拟线程。
- **Scoped Values（范围值）**：提供了一种在线程间共享不可变数据的新方式，避免使用传统的线程局部存储，促进了更好的封装性和线程安全，可用于在不通过方法参数传递的情况下，传递上下文信息，如用户会话或配置设置。

## 序列化

### 怎么把一个对象从一个jvm转移到另一个jvm?

- **使用序列化和反序列化**：将对象序列化为字节流，并将其==发送到另一个 JVM==，然后在另一个 JVM 中反序列化字节流恢复对象。这可以通过 Java 的 ObjectOutputStream 和 ObjectInputStream 来实现。
- **使用消息传递机制**：利用消息传递机制，比如使用消息队列（如 ==RabbitMQ、Kafka==）或者通过网络套接字进行通信，将对象从一个 JVM 发送到另一个。这需要==自定义协议来序列化对象==并在另一个 JVM 中反序列化。
- **使用远程方法调用（RPC）**：可以使用远程方法调用框架，如 gRPC，来实现对象在不同 JVM 之间的传输。远程方法调用可以让你在分布式系统中调用远程 JVM 上的对象的方法。
- **使用共享数据库或缓存**：将对象存储在==共====享数据库==（如 MySQL、PostgreSQL）或==共享缓存==（如 Redis）中，让不同的 JVM 可以访问这些共享数据。这种方法适用于需要共享数据但不需要直接传输对象的场景。

### Java序列化存在什么问题？

- ==无法跨语言==： Java 序列化目前只适用基于 Java 语言实现的框架，其它语言大部分都没有使用 Java 的序列化框架，也没有实现 Java 序列化这套协议。因此，如果是两个基于不同语言编写的应用程序相互通信，则无法实现两个应用服务之间传输对象的序列化与反序列化。

- 容易被攻击：Java 序列化是不安全的，我们知道对象是通过在 ObjectInputStream 上调用 readObject() 方法进行反序列化的，这个方法其实是一个神奇的构造器，它可以将类路径上几乎所有实现了 Serializable 接口的对象都实例化。这也就意味着，在反序列化字节流的过程中，该方法可以执行任意类型的代码，这是非常危险的。

  ```java
  // 假设有恶意类
  class Evil implements Serializable {
      private void readObject(ObjectInputStream in) throws Exception {
          Runtime.getRuntime().exec("calc.exe"); // Windows 打开计算器
      }
  }
  
  // 攻击者构造 Evil 的字节流发送给服务器
  ObjectInputStream ois = new ObjectInputStream(inputStream);
  Object obj = ois.readObject(); // 触发了恶意代码
  
  ```

  ==jvm反序列化时看到readObject都会执行==

- ==序列化后的流太大==：序列化后的二进制流大小能体现序列化的性能。序列化后的二进制数组越大，占用的存储空间就越多，存储硬件的成本就越高。如果我们是进行网络传输，则占用的带宽就更多，这时就会影响到系统的吞吐量。

**考虑用主流序列化框架，比如FastJson、Protobuf来替代Java 序列化。**

| 维度     | JDK 原生           | JSON（FastJSON）       | 二进制 RPC（Hessian/Protobuf） |
| -------- | ------------------ | ---------------------- | ------------------------------ |
| 流大小   | 包含类元数据，较大 | 字段少，简单对象可能小 | 字段顺序固定，无字段名，最紧凑 |
| 可读性   | 不可读             | 可读                   | 不可读                         |
| 解析性能 | 中等               | 较快（轻量对象）       | 很快（轻量+直接内存映射）      |
| 安全性   | 不安全             | 安全                   | 安全（不自动调用构造器/方法）  |

### 将对象转为二进制字节流具体怎么实现?

只有实现了==Serializable==或==Externalizable==接口的类的对象才能被序列化，否则抛出异常！

1️⃣ **不加 serialVersionUID（自动生成）**

- JVM 会根据类的结构（字段、方法、继承关系等）计算一个 **hash 值** 作为序列化版本号
- **特点**：
  1. 类的每次修改都有可能改变自动生成的值
  2. 序列化的数据只能和 **完全相同版本的类** 反序列化

示例

```
public class User implements Serializable {
    private String name;
    private int age;
}
```

- 自动生成 serialVersionUID，比如 `1234567890L`
- 你序列化一个对象到文件，然后修改类：

```
public class User implements Serializable {
    private String name;
    private int age;
    private String email; // 新增字段
}
```

- JVM 自动生成的 serialVersionUID 变了，比如 `987654321L`
- 尝试反序列化旧数据 → 抛出：

```
java.io.InvalidClassException: User; local class incompatible
```

> 问题：哪怕只是加了一个字段，也会导致反序列化失败

------

2️⃣ **加上 serialVersionUID（手动指定）**

```
public class User implements Serializable {
    private static final long serialVersionUID = 1L;
    private String name;
    private int age;
}
```

- 无论类怎么修改（只要不破坏字段类型和逻辑），serialVersionUID 都不变
- 反序列化旧数据时，JVM 会认为版本一致 → 可以反序列化
- 新增字段会使用 **默认值**（null、0、false 等）

示例

```
public class User implements Serializable {
    private static final long serialVersionUID = 1L;
    private String name;
    private int age;
    private String email; // 新增字段
}
```

- 旧数据依然可以反序列化
- `email` 字段值为 null

---

##### 二进制写入/读取文件

1.实现对象序列化：

- 让类实现Serializable接口：

```java
import java.io.Serializable;

public class MyClass implements Serializable {
    // class code
}
```

- 创建输出流并写入对象：

```java
import java.io.FileOutputStream;
import java.io.ObjectOutputStream;

MyClass obj = new MyClass();
try {
    FileOutputStream fileOut = new FileOutputStream("object.ser");
    ObjectOutputStream out = new ObjectOutputStream(fileOut);
    out.writeObject(obj);
    out.close();
    fileOut.close();
} catch (IOException e) {
    e.printStackTrace();
}
```

**2.实现对象反序列化：**

- 创建输入流并读取对象：

```java
import java.io.FileInputStream;
import java.io.ObjectInputStream;

MyClass newObj = null;
try {
    FileInputStream fileIn = new FileInputStream("object.ser");
    ObjectInputStream in = new ObjectInputStream(fileIn);
    newObj = (MyClass) in.readObject();
    in.close();
    fileIn.close();
} catch (IOException | ClassNotFoundException e) {
    e.printStackTrace();
}
```

通过以上步骤，对象obj会被序列化并写入到文件"object.ser"中，然后通过反序列化操作，从文件中读取字节流并恢复为对象newObj。这种方式可以方便地将对象转换为字节流用于持久化存储、网络传输等操作。需要注意的是，要确保类实现了Serializable接口，并且所有成员变量都是Serializable的才能被正确序列化。

##### 自定义序列化

```java
import com.alibaba.fastjson.JSON;

import java.io.File;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

class User {
    private String name;
    private int age;

    public User() {} // 反序列化需要无参构造器

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() { return name; }
    public int getAge() { return age; }

    @Override
    public String toString() {
        return "User{name='" + name + "', age=" + age + "}";
    }
}

public class FastJsonFileDemo {
    public static void main(String[] args) throws IOException {
        // 1️⃣ 创建对象
        User user = new User("Alice", 18);

        // 2️⃣ 序列化为 JSON 字符串
        String jsonString = JSON.toJSONString(user);

        // 3️⃣ 写入文件
        Path filePath = Path.of("user.json");
        Files.writeString(filePath, jsonString); // JDK 11+ 方法

        // 或使用 FileWriter 兼容 JDK 8
        /*
        try (FileWriter writer = new FileWriter("user.json")) {
            writer.write(jsonString);
        }
        */

        System.out.println("写入文件完成: " + jsonString);

        // 4️⃣ 从文件读取 JSON
        String readJson = Files.readString(filePath);

        // 5️⃣ 反序列化回对象
        User readUser = JSON.parseObject(readJson, User.class);

        System.out.println("读取对象: " + readUser);
    }
}

```

## 设计模式

### volatile和sychronized如何实现单例模式

单例模式（Singleton Pattern）是 **一种常用的设计模式**，用于保证一个类 **在整个应用程序生命周期中只有一个实例**，并提供一个全局访问点。

```java
public class SingleTon {

    // volatile 关键字修饰变量 防止指令重排序
    private static volatile SingleTon instance = null;
    private SingleTon(){}
     
    public static  SingleTon getInstance(){
        if(instance == null){
            //同步代码块 只有在第一次获取对象的时候会执行到 ，第二次及以后访问时 instance变量均非null故不会往下执行了 直接返回啦
            synchronized(SingleTon.class){
                if(instance == null){
                    instance = new SingleTon();
                }
            }
        }
        return instance;
    }
}
```

## I/O

### BIO、NIO、AIO区别是什么？

- BIO（blocking IO）：就是传统的 java.io 包，它是基于流模型实现的，交互的方式是同步、阻塞方式，也就是说在读入输入流或者输出流时，==在读写动作完成之前，线程会一直阻塞在那里==，它们之间的调用是可靠的线性顺序。优点是代码比较简单、直观；缺点是 IO 的效率和扩展性很低，容易成为应用性能瓶颈。

  ==一个线程处理一个连接==

  ![image-20250820023135122](assets/image-20250820023135122.png)

- NIO（non-blocking IO） ：Java 1.4 引入的 java.nio 包，提供了 Channel、Selector、Buffer 等新的抽象，可以构建多路复用的、同步非阻塞 IO 程序，同时提供了更接近操作系统底层高性能的数据操作方式。

  ![image-20250820023429084](assets/image-20250820023429084.png)

  1. 线程在调用 `read()` / `write()` 时不会阻塞，==如果没有数据，直接返回 0 或空结果。==

  2. Channel + Selector + Buffer

     核心组件：

     1. **Channel**
        1. 类似 BIO 的 Socket，但可以 **非阻塞**模式。
        2. `SocketChannel`、`ServerSocketChannel`、`DatagramChannel` 等。
     2. **Selector**
        1. 就是 **事件轮询器**，一个线程可以注册多个 Channel，监听它们的 **读/写/连接/可写事件**。
        2. 当某个 Channel 有事件发生时，Selector 会通知线程。
     3. **Buffer**
        1. 用来存放读写的数据。

     ==NIO 返回 `0` 并不是数据丢了，而是“内核缓冲区此刻没新数据”。==

     ==数据始终先进入 **内核 socket 缓冲区**，等你来取。==

     ==TCP 本身保证可靠传输，不会因为你 `read` 慢一点就丢。==

- AIO（Asynchronous IO） ：是 Java 1.7 之后引入的包，是 NIO 的升级版本，提供了异步非堵塞的 IO 操作方式，所以人们叫它 AIO（Asynchronous IO），异步 IO 是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。

| 特性         | 同步 I/O                                                     | 异步 I/O                  |
| ------------ | ------------------------------------------------------------ | ------------------------- |
| 谁来搬数据   | 应用线程自己搬                                               | ==操作系统帮忙搬==        |
| 调用是否阻塞 | 调用可能阻塞（BIO）或不阻塞（NIO），但最后还是要应用线程等/搬 | 调用立即返回，OS 异步处理 |
| 应用线程角色 | 发请求 + 等待/搬运数据                                       | 只发请求，最后等通知      |
| 效率         | 多线程下容易线程爆炸                                         | 高效，线程可专注业务逻辑  |

**多线程假异步**：你雇了一个小弟（线程），让他在门口等外卖，你自己去忙别的。小弟拿到外卖再叫你。

**真正 AIO**：你点外卖时告诉骑手“送到就放冰箱”，骑手送到后直接帮你放冰箱，然后发消息通知你，**根本不需要小弟**。

### NIO是怎么实现的？

NIO是一种同步非阻塞的IO模型，所以也可以叫NON-BLOCKINGIO。同步是指线程不断轮询IO事件是否就绪，非阻塞是指线程在等待IO的时候，可以同时做其他任务。

同步的核心就==Selector==（I/O多路复用），Selector代替了线程本身==轮询IO事件==，避免了阻塞同时减少了不必要的线程消耗；非阻塞的核心就是通道和缓冲区，当IO事件就绪时，可以通过==写到缓冲区，保证IO的成功==，而无需线程阻塞式地等待。

NIO由一个专门的线程处理所有IO事件，并负责分发。事件驱动机制，事件到来的时候触发操作，不需要阻塞的监视事件。线程之间通过wait,notify通信，减少线程切换。

NIO主要有三大核心部分：==Channel(通道)，Buffer(缓冲区), Selector==。传统IO基于字节流和字符流进行操作，而NIO基于Channel和Buffer(缓冲区)进行操作，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。

Selector(选择区)用于监听多个通道的事件（比如：连接打开，数据到达）。因此，单个线程可以监听多个数据通道。

![image-20250820024043346](assets/image-20250820024043346.png)

### 你知道有哪个框架用到NIO了吗？

==Netty。==

Netty 的 I/O 模型是基于非阻塞 I/O 实现的，底层依赖的是 NIO 框架的多路复用器 Selector。采用 epoll 模式后，只需要一个线程负责 Selector 的轮询。当有数据处于就绪状态后，需要一个事件分发器（Event Dispather），它负责将读写事件分发给对应的读写事件处理器（Event Handler）。事件分发器有两种设计模式：Reactor 和 Proactor，Reactor 采用同步 I/O， Proactor 采用异步 I/O。

![image-20250820024544934](assets/image-20250820024544934.png)

Reactor 实现相对简单，适合处理耗时短的场景，对于耗时长的 I/O 操作容易造成阻塞。Proactor 性能更高，但是实现逻辑非常复杂，适合图片或视频流分析服务器，目前主流的事件驱动模型还是依赖 select 或 epoll 来实现。

## Native方法解释一下

在Java中，native方法是一种特殊类型的方法，它允许Java代码调用外部的本地代码，即用==C、C++或其他语言编写的代码==。native关键字是Java语言中的一种声明，用于标记一个方法的实现将在外部定义。

在Java类中，native方法看起来与其他方法相似，只是其方法体由native关键字代替，没有实际的实现代码。例如：

```java
public class NativeExample {
    public native void nativeMethod();
}
```

要实现native方法，你需要完成以下步骤：

1. **生成JNI头文件**：使用javah工具从你的Java类生成C/C++的头文件，这个头文件包含了所有native方法的原型。
2. **编写本地代码**：使用C/C++编写本地方法的实现，并确保方法签名与生成的头文件中的原型匹配。
3. **编译本地代码**：将C/C++代码编译成动态链接库（DLL，在Windows上），共享库（SO，在Linux上）
4. **加载本地库**：在Java程序中，使用System.loadLibrary()方法来加载你编译好的本地库，这样JVM就能找到并调用native方法的实现了。

# Java集合

## 概念

### 数组与集合区别，用过哪些？

数组和集合的区别：

- 数组是固定长度的数据结构，一旦创建长度就无法改变，而集合是==动态长度==的数据结构，可以根据需要动态增加或减少元素。
- 数组可以包含基本数据类型和对象，而集合==只能包含对象==。
- 数组可以直接访问元素，而集合需要==通过迭代器==或其他==方法==访问元素。

我用过的一些 Java 集合类：

1. **ArrayList：** 动态数组，实现了List接口，支持动态增长。
2. **LinkedList：** 双向链表，也实现了List接口，支持快速的插入和删除操作。
3. **HashMap：** 基于哈希表的Map实现，存储键值对，通过键快速查找值。
4. **HashSet：** 基于HashMap实现的Set集合，用于存储唯一元素。
5. **TreeMap：** 基于红黑树实现的有序Map集合，可以按照键的顺序进行排序。
6. **LinkedHashMap：** 基于哈希表和双向链表实现的Map集合，保持插入顺序或访问顺序。
7. **PriorityQueue：** 优先队列，可以按照比较器或元素的自然顺序进行排序。

### 说说Java中的集合？

![image-20250820131943027](assets/image-20250820131943027.png)

List是有序的Collection，使用此接口能够精确的控制每个元素的插入位置，用户能根据索引访问List中元素。常用的实现List的类有==LinkedList，ArrayList，Vector，Stack==。

- ArrayList是容量可变的==非线程安全==列表，其底层使用数组实现。当几何扩容时，会创建更大的数组，并把原数组复制到新数组。ArrayList支持对元素的快速随机访问，但插入与删除速度很慢。
- LinkedList本质是一个双向链表，与ArrayList相比，，其插入和删除速度更快，但随机访问速度更慢。

Set不允许存在重复的元素，与List不同，set中的元素是无序的。常用的实现有HashSet，LinkedHashSet和TreeSet。

- HashSet通过HashMap实现，HashMap的Key即HashSet存储的元素，所有Key都是用相同的Value，一个名为PRESENT的Object类型常量。使用Key保证元素唯一性，但不保证有序性。由于HashSet是HashMap实现的，因此线程不安全。
- LinkedHashSet继承自HashSet，通过LinkedHashMap实现，使用双向链表维护元素插入顺序。
- TreeSet通过TreeMap实现的，添加元素到集合时按照比较规则将其插入合适的位置，保证插入后的集合仍然有序。

Map 是一个键值对集合，存储键、值和之间的映射。Key 无序，唯一；value 不要求有序，允许重复。Map 没有继承于 Collection 接口，从 Map 集合中检索元素时，只要给出键对象，就会返回对应的值对象。主要实现有TreeMap、HashMap、HashTable、LinkedHashMap、ConcurrentHashMap

- HashMap：JDK1.8 之前 HashMap 由数组+链表组成的，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”解决冲突），JDK1.8 以后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）时，将链表转化为红黑树，以减少搜索时间
- LinkedHashMap：LinkedHashMap 继承自 HashMap，所以它的底层仍然是基于拉链式散列结构即由数组和链表或红黑树组成。另外，LinkedHashMap 在上面结构的基础上，增加了一条双向链表，使得上面的结构可以保持键值对的插入顺序。同时通过对链表进行相应的操作，实现了访问顺序相关逻辑。
- HashTable：数组+链表组成的，数组是 HashTable 的主体，链表则是主要为了解决哈希冲突而存在的
- TreeMap：红黑树（自平衡的排序二叉树）
- ConcurrentHashMap：Node数组+链表+红黑树实现，线程安全的（jdk1.8以前Segment锁，1.8以后volatile + CAS 或者 synchronized）

### ArrayList并发问题

#### 1. 为什么不存在size+2而元素只新增1

------

**先看 `add` 的关键逻辑：**

```java
elementData[size] = e;  // 写数组
size++;                 // 自增
```

注意：`size++` **不是原子操作**，它实际上分三步：

1. 读出 `size`
2. `+1`
3. 写回

------

两个线程同时 `add` 的过程（假设初始 `size=0`）

**线程 A**

- 读到 `size = 0`
- 准备写 `elementData[0]`
- （此时被切换）

**线程 B**

- 也读到 `size = 0`
- 写 `elementData[0] = e2`
- 执行 `size++`：读到 0，加 1，写回 `size = 1`

**线程 A 继续**

- 写 `elementData[0] = e1`（覆盖掉 e2）
- 执行 `size++`：**注意它之前也读到 0**，所以结果还是写回 `size = 1`

------

**最终结果**

- 数组 `elementData[0] = e1`
- `size = 1`
- **现象**：两个元素试图插入，但最后只保留了 1 个，`size` 也是 1。

------

**为什么不会出现 “新增一个元素但 size=2”**

关键在于：

- 两个线程在读 `size` 时都读到 **相同的旧值 (0)**
- 后面 `size++` 的写回结果会互相覆盖
- 结果只会是 **最后一次写入的那个值（1）**，不会累加成 2

换句话说：

- 并发时可能 “多加了元素但 size 只加了一次” ✅
- 但不会 “少加元素却 size 多加了” ❌

------

类比

就像两个人在纸上同时写 “1”，结果还是 **1**，不会 magically 变成 “2”。

------

#### 2. 那size+1但是新增两个元素情况是怎么样的呢

------

**回顾 `add` 代码**

```java
elementData[size] = e; // 先写数组
size++;                // 再自增
```

关键点：

1. `elementData[size] = e` 是基于当前读到的 **旧 size 值**
2. `size++` 是非原子的（读 -> 加 1 -> 写）

------

**场景：`size = 0`，两个线程并发调用 `add`**

**线程 A**

- 读到 `size = 0`
- 写 `elementData[0] = e1`
- 执行 `size++`（假设还没写回）

**线程 B（同时运行）**

- **注意此时线程 B 可能已经看到 A 的写入了**
- 线程 B 也读到 `size = 0`（因为自增还没写回）
- 写 `elementData[0] = e2`（覆盖掉 e1）
- 执行 `size++`，写回 `size = 1`

**线程 A 再继续**

- 把自己的 `size++` 写回（同样是 1）

------

**最终结果**

- `elementData[0] = e2`（e1 被覆盖）
- `size = 1`
- **现象：有两个线程调用了 `add`，但最终只新增了一个元素，size 也是 1**

------

**那 size=1 但数组里有两个不同元素的情况呢？**

有的！发生在 **不同索引写入但 size 丢失** 的时候。

**假设初始 `size = 0`，线程 A 和 B 几乎同时执行：**

- A：读到 `size=0` → 写 `elementData[0] = e1` → 准备 `size=1`
- B：**稍晚一点**，这次可能读到了 **A 已经写回的 size=1**
   → 写 `elementData[1] = e2` → 准备 `size=2`

但因为竞争，假设最后写回的是 **A 的 size=1**（覆盖了 B 的 `2`）。

------

**最终结果：**

- `elementData[0] = e1`
- `elementData[1] = e2`
- `size = 1`

👉 这就是 “数组里有两个元素，但 size 只加了一次”的情况。

------

✅ 总结一下 ArrayList 并发下的可能错乱：

1. **两个线程写同一位置** → 只留下一个元素，`size=1`
2. **两个线程写不同位置** → 数组里 2 个元素，但 `size=1`（size 回退丢失更新）

------

