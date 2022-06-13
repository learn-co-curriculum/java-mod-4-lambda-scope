# Lambda Scope

## Learning Goals

- Understand variable scope in lambda expressions.
- Understand lambda expressions usage as parameters.

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
