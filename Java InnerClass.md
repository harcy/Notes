# Java内部类的认识
Java内部类，顾名思义就是在一个class的内部再定义一个class。内部类可以有访问修饰符，内部类与外部类可以相互调用，内部类与外部类的引用关系，内部类允许访问外部类的成员，包括私有成员。内部类可以分成一下以下几种：

- 成员内部类(static & non-static)
  
  static: 使用static修饰内部类，即静态成员内部类，被称为静态嵌套类
  ```java
     class Outer{
       static class Inner{
           //成员变量和方法
       }
     }
     
     class Main{
       public static void main(String[] args)
       //实例化static 静态内部类
       Outer.Inner i = new Outer.Inner();
       //i.func(),调用即静态内部类的变量或方法
     }
  ```
  
  non-static:即成员内部类，类看成是外部类的一个成员，可以访问外部类的所有成员变量和方法，即使是private修饰的；但是外部类需要访问成员内部类时，则需要使用成员内部类的实例，即必须先创建成员内部类的对象。注意：成员内部类不能含有static的变量和方法(因为成员内部类必须先创建了外部类对象，才能创建它自己)
  ```java
    Class Outer{
      private String cupSize = "F";
      Class Inner{
        private String cupSize = "E";
        public void showSize(){
          String cupSize = "D";
          System.out.println("局部变量：" + cupSize); 
          System.out.println("内部类变量：" + this.cupSize);
          System.out.println("外部类变量：" + Outter.this.cupSize);
        }
      }
    }
    
    Class Main(){
      public static void main(String[] args){
      //如何实例化内部类
      Outer.Inner i = new Outer().new Inner();
      //i.func()调用内部类的变量和方法
      }
    }
  ```
- 局部内部类
  局部内部类被定义在外部类的方法中，必须在同一方法中实例化该内部类，然后才可以使用该局部内部类。注意：如果需要往外部类的方法中传入参数，那么外部类方法的形参必须是final
  ```java
    Class Outer{
      private String cupSize = "A";
      public void showSize(final String adj){ //final 必须
        Class Inner{
          public void printSize(){
            System.out.println(cupSize +" , How" + adj);
          }
        }
        Inner i = new Inner();
        i.printSize();
      }
    }
    
    Class Main(){
      public static void main(String[] args){
        Outer o = new Outer();
        o.showSize("small");
      }
    }
  ```
- 匿名内部类
  匿名内部类也就是没有名字的内部类，可以被声明在代码块或方法的内部，并以一个带有分号的花括号结尾，因为没有名字所有没有构造函数，只能被实例化一次，并且不能是static的;简化代码是其一个重要的作用
  
  ```java
  Class Main{
    //Runnable接口的匿名内部类
    public static void main(String[] args) {
      Runnable r = new Runnable(){
        public void run(){
          for(int i=0;i<10;i++){
            System.out.println("Hello, "+ i);
          }
        }
      };
      Thread t = new Thread(r);
      t.start();
    }
  }
  ```
  
