# Throwable Class Hierarchy

## Learning Goals

- Discuss the  `Throwable` class hierarchy 
- Explore coding examples of `Error`, `Exception`, and `RuntimeException` subclasses

## Introduction

![throwable uml](https://curriculum-content.s3.amazonaws.com/6677/pillars/throwable_uml.png)

The `Throwable` class is the superclass of all errors and exceptions
in the Java language.  `Throwable` class instances (or subclass instances) are the only objects
that can be thrown by the JVM or by the `throw` statement, and are the only objects
that can be the argument in a `catch` clause.

`Error` and its subclasses represent a serious problem that a program can't recover
from. For example, a program might throw an `OutOfMemoryError` or a `StackOverflowError` due
to an infinite loop. There is nothing a program can do to recover from these types of errors,
so we don't bother trying to catch and handle them.

`Exception` and its subclasses (except for `RuntimeException` and its subclasses) are
called **checked** exceptions since they represent problems that must be checked for at compile-time
and handled with program logic. The `Exception` class represents a problem or abnormal condition that
may not be the result of erroneous program logic, yet still must be handled if it happens during runtime.
For example, a program might throw a `FileNotFoundException` when attempting to open a file
that does not exist.  This exception may result from an action outside the programmer's
control (i.e. someone deleted, moved, or renamed a file).  However, the program must include
logic to check for and handle the exception if it arises during program execution.

`RuntimeException` and its subclasses represent **unchecked** exceptions that may occur
any time during program execution.  Methods don't have to check for runtime exceptions,
which include  logic errors such as division by zero, array index out of bounds, or null references.
Runtime exceptions can be prevented by fixing the program logic.

Let's look at some code examples.

## `Error`

The program below throws an `OutOfMemoryError`, which is a subclass
of `Error`.

Recall that curly braces are required to create a new code block in Java.
Indentation is optional and does not create a code block, although its use
is encouraged for readability. Thus, the body of the `while` loop in the code
below contains only the statement `list.add(i);`
and not `i++;` due to missing curly braces around the two statements.
Since `i++;` is never executed, the code results in an infinite loop that keeps adding
elements to the list until the program runs out of memory.

```java
import java.util.ArrayList;
import java.util.List;

public class OutOfMemoryErrorExample {

    public static void main(String[] args) {
        int i=0;
        List<Integer> list = new ArrayList<>();
        // while loop is missing curly braces to create a code block
        while (i<10)
            list.add(i);
            i++; 	//this statement never reached due to missing curly braces
        System.out.println("done");
    }

}
```

Eventually the Java Virtual Machine runs out of memory and crashes:

```text
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
	at java.base/java.util.Arrays.copyOf(Arrays.java:3512)
	at java.base/java.util.Arrays.copyOf(Arrays.java:3481)
	at java.base/java.util.ArrayList.grow(ArrayList.java:237)
	at java.base/java.util.ArrayList.grow(ArrayList.java:244)
	at java.base/java.util.ArrayList.add(ArrayList.java:454)
	at java.base/java.util.ArrayList.add(ArrayList.java:467)
	at OutOfMemoryErrorExample.main(OutOfMemoryErrorExample.java:11)
```

## `Exception`

In a previous lesson we saw how to create a `Scanner` that reads from the input
stream referenced by `System.in`.

We can also create a `Scanner` that reads from a file by calling the `Scanner`
constructor and passing an instance of `File` as a parameter.
The signature of the `Scanner`  constructor shown below indicates it may
throw `FileNotFoundException` when a `File` object is passed to the constructor.

```java
public Scanner(File source) throws FileNotFoundException
```

The `Scanner` constructor will attempt to open the file
and an exception is thrown if the file does not exist.
`FileNotFoundException` is a subclass of `Exception`,
which means it is a checked exception that must be handled.

The following class fails to compile because it calls the `Scanner` constructor,
but does not attempt to catch the potential `FileNotFoundException` that might be thrown.

```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class FileNotFoundExceptionExample {

    public static void readFile() {
        Scanner input = new Scanner(new File("data.txt"));
        String text = input.next();
        System.out.println(text);
    }

    public static void main(String[] args) {
        readFile();
    }
}
```

We can fix this by placing the constructor call in a `try-catch` block:

```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class FileNotFoundExceptionExample {

    public static void readFile() {
        try {
            Scanner input = new Scanner(new File("data.txt"));
            String text = input.next();
            System.out.println(text);
        } catch (FileNotFoundException e) {
            System.out.println("File data.txt not found");
        }
    }

    public static void main(String[] args) {
        readFile();
    }
}
```

If the file `data.txt` is unavailable, the exception is thrown and the `catch`
block handles it by printing the message.

## `RuntimeException`

`RuntimeException` represents a bug in the program.
We don't explicitly check for the exception in the code. Imagine having to place every
division operation, method invocation, or array index operation in a `try-catch` block!
Rather, we fix the bug to prevent the exception from being thrown at runtime.

For example, the code below throws an `ArrayIndexOutOfBoundsException` when
the array index variable `i` is set to the array length of `4`. This is due to the
error in the loop range test `i<=nums.length`:

```java
public class ArrayIndexOutOfBoundsExceptionExample {

    public static void main(String[] args) {
        int [] nums = {5,2,9,6};
        for (int i=0;i<=nums.length;i++) {   //logic error
            System.out.println(nums[i]);
        }
    }

}
```

Executing the program results in the runtime exception:

```text
5
2
9
6
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 4 out of bounds for length 4
	at BoundsExceptionExample.main(BoundsExceptionExample.java:6)
```

The runtime exception can be avoided by fixing the program logic (use `<`):

```java
public class ArrayIndexOutOfBoundsExceptionExample {

    public static void main(String[] args) {
        int [] nums = {5,2,9,6};
        for (int i=0;i<nums.length;i++) {
            System.out.println(nums[i]);
        }
    }

}
```

The program now produces the correct output:

```text
5
2
9
6
```

## Conclusion

The `Throwable` class is the superclass of all errors and exceptions in Java.

The `Error`class represents a serious error from which there is no recovery.

The `Exception` class represents errors that are not the result of
erroneous logic and must be checked for and handled at compile-time.

The `RuntimeException` class represents logic errors that may result at runtime.
The errors are unchecked and can be avoided with correct program logic.

## Resources

- [Java 17 Throwable](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Throwable.html)   
- [Java 17 Error](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Error.html)   
- [Java 17 Exception](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Exception.html)     
- [Java 17 RuntimeException](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/RuntimeException.html)         
