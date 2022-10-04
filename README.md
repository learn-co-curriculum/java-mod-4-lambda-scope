# Lambda Scope

## Learning Goals

- Define variable scope in lambda expressions.
- Recognize scope errors in lambda expressions.

## Introduction

Lambda expressions allow us to work with variables other than the ones defined
in the parameters. It can create local variables within its body or reference
variables in outer scope.

## Local Variables

This works exactly the same as regular method definitions. We can define local
variables and use them however we want in the lambda body.

```java
@FunctionalInterface
interface Greeter {
    void display(String str);
}

public class Example {
    public static void main(String[] args) {
        Greeter greeter = name -> {
            String greeting = "Hi, how are you doing, ";
            System.out.println(greeting + name + "?");
        };
        greeter.display("John");
    }
}
```

```plaintext
Hi, how are you doing, John?
```

We have declared and initialized the `greeting` variable inside the lambda
expression body and then used it when calling the `System.out.println` method.

## Outer Method Variables

Let’s see what happens when the `greeting` variable is defined in the `main`
method’s scope.

```java
@FunctionalInterface
interface Greeter {
    void display(String str);
}

public class Example {
    public static void main(String[] args) {
        String greeting = "Hi, how are you doing, ";
        Greeter greeter = name -> {
            System.out.println(greeting + name + "?");
        };
        greeter.display("John");
    }
}
```

```plaintext
Hi, how are you doing, John?
```

We get the same result as the previous example. But there’s a catch when using
such variables. Let’s modify this example slightly and see what happens.

```java
@FunctionalInterface
interface Greeter {
    void display(String str);
}

public class Example {
    public static void main(String[] args) {
        String greeting = "Hi, how are you doing, ";
        Greeter greeter = name -> {
            System.out.println(greeting + name + "?");
        };
        greeting = "Hi, how's it going, ";
        greeter.display("John");
    }
}
```

```java
java: local variables referenced from a lambda expression must be final or effectively final
```

When referencing variables in outer method scopes, we have to make sure that the
variables are `final` or _effectively final_. A variable is said to be
_effectively final_ when its value isn’t changed after initialization.

## Name conflicts

The body of a lambda expression has the same scope as a nested block.
This means the lambda expression can't declare a variable with the same name as the enclosing
method.   However, a lambda expression can declare a variable with the same name as an instance or static variable,
just as a method can.


```java
public class Example {
    public static void main(String[] args) {
        String greeting = "Hi, how are you doing, ";
        Greeter greeter = name -> {
            String greeting = "Hello";  //ERROR
            System.out.println(greeting + name + "?");
        };
        greeter.display("John");
    }
}
```

The compiler will flag the lambda expression declaration of
variable `greeting` as an error, since a variable with the same name is declared in the `main` method:

`Variable 'greeting' is already defined in the scope`.


## Instance and Static Variables

Instance and static variables don’t have to be `final` or _effectively final_.

```java
@FunctionalInterface
interface Greeter {
    void display(String str);
}

public class Example {
    static String greeting = "Hi, how's it going, ";

    public static void main(String[] args) {
        greeting = "Hi, how are you doing, ";
        Greeter greeter = name -> {
            System.out.println(greeting + name + "?");
        };
        greeter.display("John");
    }
}
```

```plaintext
Hi, how are you doing, John?
```
While a lambda expression can access any static members of the enclosing class, 
instance members must be accessed through an implicit (i.e. `this`) or explicit object reference.
For example, the lambda expression below is defined within an instance method.  This means a `Person`
object is accessible through variable `this` and thus instance variable `name` is accessible. 

```java
interface Greeter {
    void display();
}

public class Person {
    private String name;
    private static int count;

    public Person(String name) { this.name = name; }

    public Greeter greeter() {
        //lambda expression within instance method
        //can access this, instance and static variable
        return () ->
        {
            count++;
            System.out.println("Hello " + name + ":" + count);
        } ;
    }

    public static void main(String[] args) {
        Person p1 = new Person("fred");
        Person p2 = new Person("sue");
        Greeter greeter = p1.greeter();
        greeter.display();     //Hello fred:1
        greeter.display();     //Hello fred:2
        greeter = p2.greeter();
        greeter.display();     //Hello sue:3
    }
}
```

The program output:
```text
Hello fred:1
Hello fred:2
Hello sue:3
```

## Summary

The body of a lambda expression follows the same scoping rules as a nested block.  
- A lambda expression can declare local variables within its method body if there are no naming conflicts with the enclosing method.
- A lambda expression can access local variables of the enclosing method if they are final or effectively final.
- A lambda expression can access static members of the enclosing class.
- A lambda expression must access instance members through an (implicit or explicit) reference to a class instance.