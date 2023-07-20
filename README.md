# WPF Interview Question Reference **Basic**


## Table of contents

1) [value type and reference type] (#value type and reference type)

2) [Boxing and unboxing] (#packing and unboxing)

3) [string and string operations] (#string and string operations)

4) [Classes and Interfaces] (#Classes and Interfaces)

5) [Constants, fields, properties, properties and delegates] (#Constants, fields, properties, properties and delegates)

6) [GC and memory management] (#GC and memory management)

7) [Multi-thread programming and thread synchronization](#Multi-thread programming and thread synchronization)

8) [Thread Synchronization](#Thread Synchronization)

9) [WPF] (#WPF)
--------------------------------

## <a id="value type and reference type">value type and reference type</a>

### **1. What is the difference between value type and reference type? **

Value types include simple types, structure types, and enumeration types, and reference types include custom classes, arrays, interfaces, and delegates.

1. Assignment method: When assigning a value type variable to another value type variable, the contained value will be copied. This is different from the assignment of reference type variables. The assignment of reference type variables only copies the reference of the object (that is, the memory address, similar to the pointer in `C++`), and does not copy the object itself.

2. Inheritance: It is impossible to derive new types from value types, and all value types are implicitly derived from `System.ValueType`. But like reference types, structs can also implement interfaces.

3. `null`: Unlike reference types, value types cannot contain `null` values. However, the nullable type feature allows `null` to be assigned to value types.

4. Each value type has an implicit default constructor to initialize the default value of the type. The value type will default to 0 initially, and the reference type will default to `null`.

5. Value types are stored on the stack, and reference types are stored on the managed heap.

### **2. What is the difference between a structure and a class? **

The structure is a value type, and the class is a reference type. The main difference is as in question 1.
Other differences:
- Structs do not support non-destructive constructors, do not support destructors, and cannot be modified with `protected`;
- Structures are often used for data storage, and class `class` is mostly used for behavior;
- `class` needs to use the `new` keyword to instantiate the object, and `struct` does not need to use the `new` keyword;
- `class` can be an abstract class, `struct` does not support abstraction;

### **3. Is the delegate a reference type or a value type? What about enum, int[] and string? **

Enumerations are value types and others are reference types.

### **4. What is the difference between heap and stack? **

thread stack:
-Stack for short Stack managed heap: Heap for short Most value types are allocated on the stack, and reference types are all allocated on the heap;
- The stack is managed by the operating system, and the variables on the stack are released after their scope is completed. The efficiency is high, but the space is limited. The heap is controlled by the GC of the CLR;
- The stack is based on threads, and each thread has its own thread stack with an initial size of 1M. The heap is process-based, a process allocates a heap, and the size of the heap is dynamically controlled by the GC according to the running conditions;

### **5. Is it possible for "struct" objects to be allocated on the heap? What happens, and is there anything to be aware of? **

The structure is a value type, and there are two situations where it will be allocated on the pair: as a field or attribute of the class, the structure will be allocated on the heap together with the class; it will be stored in the heap after boxing, and the boxing of the value type should be avoided as much as possible. Both unboxing and boxing of value types have performance loss, which will be focused on in the next article;

### **6. Understand that parameters are passed by value? and pass by reference? **

Pass by value: For the value type, the copy of its value is passed, while the reference type passes the memory address of the reference variable, and they still point to the same object. Pass by reference: The memory address of the parameter is passed through the keywords out and ref, and the effect of the value type and the reference type is the same.

### **7. What is the difference and similarity between out and ref? **

Both out and ref instruct the compiler to pass the address of the parameter, which is the same in behavior; their use mechanism is slightly different, ref requires the parameter to be explicitly initialized before use, and out must be initialized inside the method; out and ref cannot be repeated Overload means that you cannot define overloads such as Method(ref int a) and Method(out int a). From the perspective of compilation, the essence of the two is the same, but there is a difference in use;

### **8. How many ways are there to determine value type and reference type? **

Simply put, those inherited from System.ValueType are value types, and vice versa are reference types.

### **9. Which predefined value types does C# support? What predefined reference types does C# support? **

Value types: integer, float, character, bool, and decimal Reference types: Object, String


### **10. Tell me about the life cycle of value types and reference types? **

Value types are deallocated when their scope ends. Reference types are reclaimed by GC garbage collection cycles. This answer may be too simple, and a more detailed answer will be mentioned in a later article.


### **11. If the reference type is defined in the structure, how is the object stored in memory? For example, is the class User object in the following structure stored on the stack or the heap? **

```C#
public struct MyStruct
{
public int Index;
public User User;
}
```


MyStruct is stored in the stack, the instance of its field User is stored in the heap, and the MyStruct.User field stores the memory address pointing to the User object.

 
## <a id="Boxing and Unboxing">Boxing and Unboxing</a>

### **1. What is unboxing and boxing? **

Boxing is the conversion of a value type to a reference type, and unboxing is the conversion of a reference type (boxed object) to a value type.

### **2. What is a box? **

It is a reference type object.

### **3. Where is the box? **

on the managed heap.

### **4. What is the performance impact of boxing and unboxing? **

Both boxing and unboxing involve memory allocation and object creation, which have a large performance impact.

### **5. How to avoid stealth boxing? **

In coding, use generics and display boxing more often.

### **6. The basic structure of the box? **

As mentioned above, the box is a reference type object, so its structure mainly includes two parts: the value type field value; the standard configuration of the reference type, and the extra space for the reference object: TypeHandle and synchronization index block, about these two concepts in Later articles in this series will delve deeper.

### **7. The process of boxing? **

1. Apply for memory in the heap, the memory size is the size of the value type, plus additional fixed space (standard configuration of the reference type: TypeHandle and synchronization index block); 2. Copy the field value (x=1023) of the value type In the newly allocated memory; 3. Return the address of the new reference object (to the reference variable object o)

### **8. The process of unpacking? **

1. Check whether the instance object (object o) is valid, if it is null, whether its boxed type is consistent with the unboxed type (int), if the detection is invalid, throw an exception; The address of the value type field value in the box object (object o); 3. Field copy, copy the value type field value in the box object (object o) to the stack, which means creating a new value type variable to store the unboxing after the value;

 
## <a id="string and string operations">string and string operations</a>

### **1. Is the string a reference type or a value type? **

reference type.

### **2. What is the best way to add strings, and what are the reasons? **

For a small number of string connections, use String.Concat, and for a large number of strings, use StringBuilder, because StringBuilder has better performance, and if it is string, it will create a large number of string objects.

### **3. When using StringBuilder, what issues should be paid attention to? **

When a small number of strings are used, try not to use them. StringBuilder itself has a certain performance overhead; when using StringBuilder for a large number of string connections, you should set an appropriate capacity;

 

## <a id="Class and Interface">Class and Interface</a>

### **1. Do all types inherit System.Object? **

Basically yes, all value types and reference types inherit from System.Object, and interface is a special type that does not inherit from System.Object.

### **2. Explain the difference between virtual, sealed, override and abstract**

- virtual declares the keyword of the virtual method, indicating that the method can be overridden

- sealed indicates that the class cannot be inherited

- override overrides the method of the base class

- abstract is a keyword for declaring abstract classes and abstract methods. Abstract methods do not provide implementation and are implemented by subclasses. Abstract classes cannot be instantiated.

### **3. What are the similarities and differences between interfaces and classes? **

difference:

1. Interfaces cannot be instantiated directly.

2. The interface only contains the declaration of the method or property, not the implementation of the method.

3. Interfaces can be multi-inherited, but classes can only be inherited single-inherited.

4. The class has the concept of partial class, and the definition can be split between different source files, but the interface does not. (This place is indeed wrong, the interface can also be divided, thank you @xclin163 for your correction)

5. The meaning of the expression is different. The interface mainly defines a specification, and the method is called uniformly, that is, the specification class, the constraint class, and the class is the realization and collection of method functions

Same point:

1. Interfaces, classes, and structures can all inherit from multiple interfaces.

2. An interface is similar to an abstract base class: any non-abstract type that inherits an interface must implement all the members of the interface.

3. Both interfaces and classes can contain events, indexers, methods, and properties.

### **4. What is the difference between an abstract class and an interface? **

1. Inheritance: Interfaces support multiple inheritance; abstract classes cannot implement multiple inheritance.

2. The concept of expression: interface is used for specification, more emphasis on contract, abstract class is used for commonality, emphasizing father and son. An abstract class is a high degree of aggregation of a class of things, so for a subclass inheriting an abstract class, for an abstract class, it belongs to the relationship of "Is A"; while an interface defines a behavior specification and emphasizes the relationship of "Can Do", Therefore, for a subclass that implements an interface, compared to the interface, it is "behavior needs to be completed according to the interface".

3. Method implementation: For the methods in the abstract class, the implementation part can be given or not; while the interface methods (abstract rules) cannot give the implementation part, and the methods in the interface cannot add modifiers.


4. Subclass rewriting: Inherited classes have different implementations of the methods involved in the two. The inherited class does not need to rewrite the abstract method defined by the abstract class, that is to say, the method of the abstract class can be extended; but for the method or attribute defined by the interface class, it must be rewritten in the inherited class. The corresponding methods and properties are implemented.

5. The impact of the new method: In the abstract class, if a method is added, it does not need to be used for any processing in the inherited class; for the interface, the inherited class needs to be modified to provide a newly defined method.

6. Interfaces can act on value types (enumerations can implement interfaces) and reference types; abstract classes can only act on reference types.

7. Interfaces cannot contain fields and implemented methods. Interfaces only contain signatures of methods, properties, indexers, and events; abstract classes can define fields, properties, and methods that contain implementations.

### **5. What is the difference between overloading and overriding? **

Overloading: Method overloading occurs when a class contains two methods with the same name but different signatures (same method name, different parameter list). Use method overloading to provide methods that accomplish the same semantically but perform different functions.

Override: Used in class inheritance, by overriding subclass methods, the implementation of parent class virtual methods can be changed.

Main difference:

1. Method coverage is the relationship between subclasses and parent classes, which is a vertical relationship; method overloading is the relationship between methods in the same class, which is a horizontal relationship. 2. Coverage can only be related by one method, or only by a pair of methods; method overloading is the relationship between multiple methods. 3. Overriding requires the same parameter list; overloading requires a different parameter list. 4. In the coverage relationship, the method body to call is determined according to the type of the object; in the overload relationship, the method body is selected according to the actual parameter list and the formal parameter list when calling.

### **6. What are the similarities and differences between new and override in inheritance? **

Look at the code below, there is a base class A, B1 and B2 both inherit from A, and use different methods to change the behavior of the parent class method Print(). What does the test code output? Why?

```c#
        static void Main(string[] args)
        {
            B1 b1 = new B1(); B2 b2 = new B2();
            b1.Print(); b2.Print();      //output B1、B2

            A ab1 = new B1(); A ab2 = new B2();
            ab1.Print(); ab2.Print();   //output B1、A
            Console.ReadLine();
        }

        public class A
        {
            public virtual void Print() { Console.WriteLine("A"); }
        }
        public class B1 : A
        {
            public override void Print() { Console.WriteLine("B1"); }
        }
        public class B2 : A
        {
            public new void Print() { Console.WriteLine("B2"); }
        }
```

​       



 

### **7. Where is the static field defined in the class stored in memory? Why do you say it won't be recycled by GC? **

Objects of different types are stored on the loading heap of memory, because the loading heap is not managed by GC, and its life cycle follows the AppDomain and will not be recycled by GC.

 

## <a id="Constants, Fields, Properties, Properties and Delegates">Constants, Fields, Properties, Properties and Delegates</a>

### **1. What is the difference between const and readonly? **

The const keyword is used to declare compile-time constants, and readonly is used to declare runtime constants. Both can identify a constant, the main differences are as follows: 
1. The initialization positions are different. const must be assigned at the same time as the declaration; readonly can be assigned at the declaration or in the constructor. 
2. The modification objects are different. const can modify the fields of the class, as well as local variables; readonly can only modify the fields of the class. 
3. const is a compile-time constant, the value is determined at compile time, and the value is inlined into the code at compile time; readonly is a runtime constant, and the value is determined at runtime. 
4. const is static by default; and if readonly is set to static, the statement needs to be displayed. 5. The supported types are different. const can only modify primitive types or other reference types whose value is null; readonly can be any type.

```C#
 public class Program
    {
        public readonly int PORT;
        static void Main(string[] args)
        {
            B a = new B();
            Console.WriteLine(a.PORT);
            Console.WriteLine(B.PORT2);
            Console.WriteLine(a.PORT3.ccc);
            Console.ReadLine();
        }
    }

    class B
    {
        //readonly can be assigned at the declaration or in the constructor
        public readonly int PORT;
        //const must be assigned at the same time of declaration
        public const int PORT2 = 10;

        //Error public const A PORT3 = new A() const can only modify primitive types or other reference types whose value is null

        //readonly can be any type
        public readonly A PORT3 = new A();
        public B()
        {

            PORT = 11;
        }
    }
    class A
    {
        public string ccc = "aaaa";
    }

 
```

### **2. What are the similarities and differences between fields and attributes? **

- Attributes provide more powerful and flexible functions to manipulate fields

- Due to object-oriented encapsulation, fields are generally not designed as Public

- Attributes allow writing code in set and get

- Attributes allow to control the accessibility of set and get, thus providing read-only or read-write functions (logically only write is meaningless)

- Attributes can use override and new

### **3. What is the difference between static members and non-static members? **

- Static variables are declared with the static modifier, and static members are loaded when the class is added (as mentioned in the previous article, static fields are stored on the Load Heap along with the type object) and accessed through the class.

- Variables declared without the static modifier are called non-static variables, which are created when the object is instantiated and accessed through the object.

- The same static variable of all instances of a class has the same value, and the same non-static variable of different instances of the same class can have different values.

- Non-static members, such as non-static variables and non-static functions, cannot be used in the implementation of static functions.

### **4. What are the features? how to use? **

Features and attributes are two completely different concepts, but they are similar in name. The Attribute feature is a piece of configuration information associated with a target object. It is essentially a class that provides associated additional information for the target element. This additional information is stored in the metadata in the dll, which itself has no meaning. The runtime obtains additional information in a reflective manner.

### **5. What is a delegate in C#? Is an event a kind of delegation? **

What is a commission? In simple terms, delegates are similar to function pointers in C or C++, allowing methods to be passed as parameters.

- The delegates in C# all inherit from the System.Delegate type;

- The declaration of the delegate type is similar to the method signature, with return value and parameters;

- A delegate is a reference type that can encapsulate a named (or anonymous) method, and the method is passed as a pointer, but the delegate is object-oriented and type-safe;

Events can be understood as a special kind of delegation, and events are implemented internally based on delegation.
 

## <a id="GC and memory management">GC and memory management</a>

### **1. Briefly describe the life cycle of a reference object? **

- new creates an object and allocates memory

- object initialization

- Object manipulation, usage

- Resource cleanup (unmanaged resources)

- GC garbage collection

### **2. What is the main process of GC garbage collection? **

① Marking: Assume that all objects are garbage, traverse each reference object on the heap according to the application root root, generate a reachable object graph, and mark the objects (reachable objects) that are still in use (actually, in object synchronization A flag is turned on in the index block).

② Clearing: Clearing is performed on all unreachable objects, memory is directly reclaimed for ordinary objects, and objects that implement finalizers (objects that implement destructors) need to be recovered separately. After clearing, the memory will become discontinuous, which is the work of step 3.

③ Compression: Transfer the remaining objects to a continuous memory, because the addresses of these objects have changed, and the addresses of the Root and pointers need to be modified to the new addresses after the move.

### **6. Under what circumstances does the GC recycle? **

- When the memory overflows (when the 0 generation object is full)

- When Windows reports insufficient memory, the CLR will force garbage collection

- CLR unloads AppDomian, GC recycles all

- call GC.Collect

- Other situations, such as the host refuses to allocate memory, the physical memory is insufficient, and the storage threshold of the short-term survival generation is exceeded

**7. How does the using() syntax ensure that object resources are released? Will resources still be released if an exception occurs inside? **3

using() is just a grammatical form, and its essence is still a try...finally structure, which can ensure that Dispose will always be executed.

### **8. Explain the destructor in C#? Why are destructors not recommended in some programming advice? **

The destructor in C# is actually the finalizer Finalize, because it looks like the destructor in C++.

Some programming suggestions do not recommend the use of destructors. The main reasons are: first, the performance of Finalize itself is not good; second, many people do not understand the principle of Finalize, and may abuse it, resulting in memory leaks, so don’t use it at all.

### **9. Difference between Finalize() and Dispose()? **

Both Finalize() and Dispose() are ways to release unmanaged resources in .NET. The main difference between them is the executor and execution time:

- finalize is called by the garbage collector; dispose is called by the object.

- finalize does not need to worry about unmanaged resources not being released because finalize is not called, and dispose must be called manually.

- finalize cannot guarantee the immediate release of unmanaged resources. The time when Finalizer is executed is an indeterminate time after the object is no longer referenced; and unmanaged resources are released as soon as dispose is called.

- Only class types can override finalize, but structures cannot; both classes and structures can implement IDispose.

Another important difference is that the finalizer will cause the object to be resurrected once, that is to say, it will be recycled twice by the GC before the recycling work is finally completed. This is the main reason why some people do not recommend developers to use finalizers.


### **10. When are the Dispose and Finalize methods called? **

- Release unmanaged resources as soon as Dispose is called;

- Finalize cannot guarantee immediate release of unmanaged resources, and the time when Finalizer is executed is an indeterminate time after the object is no longer referenced;

### **11. Is there a memory leak in the managed heap in .NET?**

Yes, probably. for example:

- Improper use of static fields, resulting in a large amount of data that cannot be released by GC;

- Dispose() was not executed correctly, and unmanaged resources were not released;

- Improper use of the finalizer Finalize(), resulting in failure to release resources normally;

- Other incorrect references, resulting in a large number of managed objects that cannot be released by GC;

### **12. What are the common ways to create new objects on the managed heap? **

- new an object;

- String assignment, such as string s1="abc";

- value type boxing;

## <a id="Multi-thread programming and thread synchronization">Multi-thread programming and thread synchronization</a>

### **1. Describe the difference between a thread and a process? **

- An application instance is a process, a process contains one or more threads, and a thread is a part of a process;

- Processes are independent of each other, they have their own private memory space and resources, and threads in a process can share all resources of the process to which they belong;

### **2. Why does lock need to lock a parameter, can it lock a value type? What are the requirements for this parameter? **

The lock object of lock is required to be a reference type. It can lock the value type, but the value type will be boxed, and the object after each boxing will be different, which will cause the lock to be invalid.

For the lock lock, the locked object parameter is the key. The synchronization index block pointer of this parameter will point to a real lock (synchronization block), and this lock (synchronization block) will be reused.

### **3. What is the relationship and difference between multithreading and asynchrony? **

Multithreading is one of the main ways to achieve asynchrony, and asynchrony is not equal to multithreading. There are many ways to achieve asynchrony, such as using hardware characteristics, using processes or fibers, and so on. There are a lot of asynchronous programming support in .NET. For example, there are Begin** and End** methods in many places, which are a kind of asynchronous programming support. Some of them use multi-threading internally, and some use the characteristics of hardware to realize asynchronous programming.

### **4. What are the advantages of thread pool? What are the shortcomings? **

Advantages: Reduce the overhead of thread creation and destruction, and can reuse threads; thereby reducing the performance loss of thread context switching; during GC recycling, fewer threads are more conducive to GC recycling efficiency.

Disadvantages: The thread pool cannot have more precise control over a thread, such as understanding its running status, etc.; it cannot set the priority of the thread; tasks (methods) added to the thread pool cannot have return values; it is not suitable for thread pools for long-running tasks.
### **5. What is the difference between Mutex and lock? Which one is generally better to use as a lock? **

Mutex is a kernel-mode-based mutual exclusion lock that supports recursive calls of locks, while Lock is a hybrid lock. It is generally recommended to use Lock better because the performance of lock is better.

### **6. The difference between Thread and Task**

The Thread class is mainly used to implement the creation and execution of threads.

The Task class represents a single operation performed asynchronously.

#### 1. Task is based on Thread, which is a relatively high-level encapsulation. Task still needs Thread to execute in the end

#### 2. Task uses background thread to execute by default, and Thread uses foreground thread by default
```C#
static void Main(string[] args)
{
    Thread thread = new Thread(obj => { Thread.Sleep(3000); });
    thread.Start();
}
```
With the above code, the main program ends after 3 seconds.


```C#
static void Main(string[] args)
{
    Task<int> task = new Task<int>(() => 
    {
        Thread,Sleep(3000);
        return 1;
    });
    task.Start();
}

```

And this code will end in an instant.

#### 3. Task can have a return value, but Thread has no return value

Although Thread can handle the return value through the parameters of the Start method, it is very inconvenient.
```C#
public void Start (object parameter);
static void Main(string[] args)
{  
    Task task = new Task(LongRunningTask);
    task.Start();
    Console.WriteLine(task.Result);
}
private static int LongRunningTask()
{
    Thread.Sleep(3000);
    return 1;
}

```

#### 4. Task can perform follow-up operations, but Thread cannot perform follow-up operations

```C#
static void Main(string[] args)
{
    Task task = new Task(LongRunningTask);
    task.Start();
    Task childTask = task.ContinueWith(SquareOfNumber);
    Console.WriteLine("Sqaure of number is :"+ childTask.Result);
    Console.WriteLine("The number is :" + task.Result);
}
private static int LongRunningTask()
{
    Thread.Sleep(3000);
    return 2;
}
private static int SquareOfNumber(Task obj)
{
    return obj.Result * obj.Result;
}

```

#### 5. Task can cancel task execution, but Thread cannot

```C#
static void Main(string[] args)
{
    using (var cts = new CancellationTokenSource())
    {
        Task task = new Task(() => { LongRunningTask(cts.Token); });
        task.Start();
        Console.WriteLine("Operation Performing...");
        if(Console.ReadKey().Key == ConsoleKey.C)
        {
            Console.WriteLine("Cancelling..");
            cts.Cancel();
        }                
        Console.Read();
    }
}
private static void LongRunningTask(CancellationToken token)
{
    for (int i = 0; i < 10000000; i++)
    {
        if(token.IsCancellationRequested)
        {
            break;
        }
        else
        {                  
            Console.WriteLine(i);
        }               
    }          
}

```



#### 6. Exception Propagation

Thread can't get exception on parent method, but Task can.

## **Communication between threads**

C# threads communicate with each other mainly using two classes: AutoResetEvent and ManualResetEvent.

### **一、AutoResetEvent**

AutoResetEvent allows threads to communicate with each other by signaling, threads wait for the signal by calling WaitOne on AutoResetEvent. If the AutoResetEvent is non-terminal, the thread is blocked and waits for the thread currently controlling the resource to notify it that the resource is available by calling Set.

When you eat fast food, you will line up to pay, the cashier will send a payment notification, and the customers will pay in turn

The AutoResetEvent class can only notify one waiting thread at a time, and the state of the AutoResetEvent object will be set to false immediately after the notification once, that is, if two customers are waiting for the cashier to notify, the set method of the AutoResetEvent object can only notify the first customer

### **二、ManualResetEvent**

In AutoResetEvent, if two threads are to be notified, the Set method has to be executed twice, and the example of a fast food restaurant is also used as an example. However, if one day customer 1 wins the lottery and wants to invite 10 colleagues in the department to eat, that is to say, as long as Set once, all waiting threads will cancel the wait, which is equivalent to the cashier only receiving money once, and 10 people can go to eat through the cash register. After the method sends a notification, if you want to block again, you need to manually modify it, that is, call the Reset method

### **Summarize**
The main difference between AutoResetEvent and ManualResetEvent is: AutoResetEvent can only notify one waiting thread at a time, and it will automatically close after notification; while ManualResetEvent can notify many waiting threads at a time, but to close it, you need to call the Reset method to close it manually

## <a id="Thread Synchronization">Thread Synchronization</a>

### **Several methods of thread synchronization:**

The main difference between AutoResetEvent and ManualResetEvent is: AutoResetEvent can only notify one waiting thread at a time, and it will automatically close after notification; while ManualResetEvent can notify many waiting threads at a time, but to close it, you need to call the Reset method to close it manually

## <a id="Thread Synchronization">Thread Synchronization</a>

### **LINE### **1, Blocking**

When a thread calls Sleep, Join, EndInvoke, the thread is in a blocked state (Sleep blocks the calling thread, Join, EndInvoke blocks another thread), and will immediately exit from the cpu. (Threads in blocked state do not consume cpu)

### **2, lock (lock)**

Locking makes multiple threads only one thread can call this method at the same time, and other threads are blocked.

#### **Selection of synchronization object: **Several methods of process synchronization:**

- use

**Reference type**, when the value type is locked, it will be boxed and a new object will be generated.

- use

**private** modification, it is easy to cause deadlock when using public. **(When using lock(this), lock(typeof(instance)), the class should also be private)**.

- string cannot be used as a lock object.

- cannot be used in lock

await keyword

#### ** Do locks have to be statically typed? **

If the method being locked is static, then the lock must be of static type. In this way, the method is locked globally, and no matter how many instances of the class there are, they must be queued for execution.

If the locked method is not static, then you cannot use a static type lock, because the locked method belongs to the instance, as long as the instance calls the locked method without causing damage, and there is no need for locks between different instances. This lock only locks the method of this instance, not the methods of all instances.*

```C#
class ThreadSafe
{
 private static object _locker = new object();
 
  void Go()
  {
    lock (_locker)
    {
      ......//Shared data operations (Static Method), use static locks to ensure that all instances are queued for execution
    }
  }
​
private object _locker2=new object();
  void GoTo()
  {
    lock(_locker2)
    //The operation of shared data, non-static method, uses non-static lock to ensure that method callers of the same instance are queued for execution
  }
}
```

#### **Synchronization object can double as its lock object**

like:

```C#
class ThreadSafe
{
 private List <string> _list = new List <string>();
  void Test()
  {
    lock (_list)
    {
      _list.Add("Item 1");
    }
  }
}
```

### **3、Monitors**

lock is actually a concise way of writing Monitors.

```C#
lock (x)
{
    DoSomething();
}
```



Both are actually the same.

```C#
System.Object obj = (System.Object)x;  
System.Threading.Monitor.Enter(obj);  
try  
{  
    DoSomething();  
}  
finally  
{  
    System.Threading.Monitor.Exit(obj);  
} 
```



### **4. Mutex**

A mutex is a mutually exclusive synchronization object, and only one thread can acquire it at a time. Synchronization of threads at the process level can be achieved.

```C#
class Program
    {
      //Instantiate a mutex
        public static Mutex mutex = new Mutex();

        static void Main(string[] args)
        {
            for (int i = 0; i < 3; i++)
            {
              //Call the method protected by the mutex in a different thread
                Thread test = new Thread(MutexMethod);
                test. Start();
            }
            Console. Read();
        }

        public static void MutexMethod()
        {
           Console.WriteLine("{0} requests to acquire a mutex", Thread.CurrentThread.Name);
           mut. WaitOne();
           Console.WriteLine("{0} has acquired the mutex", Thread.CurrentThread.Name);
           Thread. Sleep(1000);
           Console.WriteLine("{0} ready to release mutex", Thread.CurrentThread.Name);
            // Release the mutex
           mut. ReleaseMutex();
           Console.WriteLine("{0} has released the mutex", Thread.CurrentThread.Name);
        }
    }
```



#### **Mutex locks can achieve thread synchronization between different processes**

Use a mutex to implement a feature where only one application can be started at a time.

```c#
    public static class SingleInstance
    {
        private static Mutex m;
​
        public static bool IsSingleInstance()
        {
            // Do you need to create an application
            Boolean isCreateNew = false;
            try
            {
               m = new Mutex(initiallyOwned: true, name: "SingleInstanceMutex", createdNew: out isCreateNew);
            }
            catch (Exception ex)
            {
               
            }
            return isCreateNew;
        }
    }
```



#### Mutex constructor with three parameters

1. initiallyOwned: If initiallyOwned is true, the initial state of the mutex is acquired by the instantiated thread, otherwise the instantiated thread is in an unacquired state.

1. name: The name of the mutex. There is only one mutex named name in the operating system. If a thread obtains the mutex of this name, other threads cannot obtain the mutex and must wait for the thread to release it.

1. createNew: Returns false if a mutex with the specified name already exists, otherwise returns true.

### **5, signal and handle**

Lock and mutex can achieve thread synchronization to ensure that only one thread executes at a time. But the communication between threads cannot be realized. If the threads need to communicate with each other, use AutoResetEvent, ManualResetEvent, and communicate with each other through signals. They both have two states, a terminal state and a non-terminal state. A thread can block only when it is in a non-terminating state.
#### **AutoResetEvent****：**

The AutoResetEvent constructor can pass in a parameter of bool type, false means that the initial state of the AutoResetEvent object is set to non-terminated. If it is true to identify the termination state, then the WaitOne method will no longer block the thread. But because this class will automatically change the termination status to non-termination, subsequent calls to the WaitOne method will be blocked.

The WaitOne method blocks the thread calling this method if the state of the AutoResetEvent object is not terminated. You can specify the time, if no signal is obtained, return false

The set method releases the blocked thread. But only one blocked thread can be released at a time.

```C#
class ThreadSafe
{
    static AutoResetEvent autoEvent;

    static void Main()
    {
        / / Make AutoResetEvent in a non-terminating state
        autoEvent = new AutoResetEvent(false);

        Console.WriteLine("Main thread running...");
        Thread t = new Thread(DoWork);
        t.Start();

        Console.WriteLine("The main thread sleeps for 1 second...");
        Thread. Sleep(1000);

        Console.WriteLine("The main thread releases the signal...");
        autoEvent. Set();
    }

     static void DoWork()
    {
        Console.WriteLine("The t thread runs the DoWork method, blocking itself to wait for the signal of the main thread...");
        autoEvent. WaitOne();
        Console.WriteLine("t thread DoWork method gets the main thread signal, continue to execute...");
    }

}

// output
// main thread running...
//The main thread sleeps for 1 second...
// The t thread runs the DoWork method, blocking itself waiting for the main thread signal...
//Main thread release signal...
// The t thread DoWork method gets the signal of the main thread and continues to execute...
```



#### **ManualResetEvent**

The usage of ManualResetEvent and AutoResetEvent is similar.

After AutoResetEvent calls the Set method, it will automatically change the signal from release (termination) to blocking (non-termination), and only one thread will get the release signal at a time. The ManualResetEvent will not automatically change the signal from release (termination) to blocking (non-termination) after calling the Set method, but keeps releasing the signal, so that multiple blocked threads are running at a time, and the Reset method can only be called manually , Change the signal from release (termination) to blocking (non-termination), and then the thread that calls the Wait.One method will be blocked again.

```C#
public class ThreadSafe
{
    //Create a non-terminating state ManualResetEvent
    private static ManualResetEvent mre = new ManualResetEvent(false);

    static void Main()
    {
        for(int i = 0; i <= 2; i++)
        {
            Thread t = new Thread(ThreadProc);
            t.Name = "Thread_" + i;
            t.Start();
        }

        Thread.Sleep(500);
        Console.WriteLine("\nThe method of the new thread has been started and blocked, call Set to release the blocked thread");

        mre.Set();

        Thread.Sleep(500);
        Console.WriteLine("\nWhen the ManualResetEvent is in the terminated state, the multi-thread called by the Wait.One method will not be blocked.");

        for(int i = 3; i <= 4; i++)
        {
            Thread t = new Thread(ThreadProc);
            t.Name = "Thread_" + i;
            t.Start();
        }

        Thread.Sleep(500);
        Console.WriteLine("\nCall the Reset method, the ManualResetEvent is in a non-blocking state, at this time the thread calling the Wait.One method is blocked           again");

        mre.Reset();

        Thread t5 = new Thread(ThreadProc);
        t5.Name = "Thread_5";
        t5.Start();

        Thread.Sleep(500);
        Console.WriteLine("\nCall the Set method to release the blocked thread");

        mre.Set();
    }


    private static void ThreadProc()
    {
        string name = Thread.CurrentThread.Name;

        Console.WriteLine(name + " run and call WaitOne()");

        mre. WaitOne();

        Console.WriteLine(name + "end");
    }
}

//Thread_2 runs and calls WaitOne()
//Thread_1 runs and calls WaitOne()
//Thread_0 runs and calls WaitOne()

//The method of the new thread has been started and blocked, call Set to release the blocked thread

//Thread_2 ends
//Thread_1 ends
//Thread_0 ends

//When the ManualResetEvent is in the terminated state, the multi-thread called by the Wait.One method will not be blocked.

//Thread_3 runs and calls WaitOne()
//Thread_4 runs and calls WaitOne()

//Thread_4 ends
//Thread_3 ends

///Call the Reset method, the ManualResetEvent is in a non-blocking state, and the thread calling the Wait.One method is blocked again

//Thread_5 runs and calls WaitOne()
//Call the Set method to release the blocked thread
//Thread_5 ends
```


### **6、Interlocked**

If a variable is modified by multiple threads, read. Interlocked can be used.

The addition and deletion of a data cannot be guaranteed to be atomic on the computer, because the operation of the data is also divided into steps:

1. Load the value from the instance variable into the register.

1. Increase or decrease the value.

1. Store the value in an instance variable.

Interlocked provides atomic operations on variables shared by multiple threads. Interlocked provides methods that require atomic operations:

- public static int Add (ref int location1, int value); Adds two parameters, and assigns the result to the first parameter.

- public static int Increment (ref int location); auto-increment.

- public static int CompareExchange (ref int location1, int value, int compareand);

location1 is compared with comparand and replaced by value. value If the first parameter is equal to the third parameter, then value is assigned to the first parameter. compareand Compare with the first parameter.
------

### **7、ReaderWriterLock**
If you want to ensure that a resource or data is up to date before being accessed. Then you can use ReaderWriterLock. This lock ensures that only it can access these resources when assigning or updating resources, and no other threads can access them. That is, an exclusive lock. However, when reading these data with a lock, an exclusive lock cannot be achieved.

The lock allows only one thread to execute at a time. And ReaderWriterLock allows multiple threads to perform **read operations** at the same time, or only one thread with an exclusive lock to perform **write operations**.

```C#
    class Program
    {
       // create an object
        public static ReaderWriterLock readerwritelock = new ReaderWriterLock();
        static void Main(string[] args)
        {
            //Create a thread to read data
            Thread t1 = new Thread(Write);
           // t1. Start(1);
            Thread t2 = new Thread(Write);
            //t2.Start(2);
            // Create 10 threads to read data
            for (int i = 3; i < 6; i++)
            {
                Thread t = new Thread(Read);
              //  t.Start(i);
            }

            Console.Read();

        }

        // write method
        public static void Write(object i)
        {
            // Acquire the write lock, with a timeout of 20 milliseconds.
            Console.WriteLine("Thread: " + i + "ready to write...");
            readerwritelock.AcquireWriterLock(Timeout.Infinite);
            Console.WriteLine("Thread: " + i + "write operation" + DateTime.Now);
            // Release the write lock
            Console.WriteLine("Thread: " + i + "End of writing...");
            Thread.Sleep(1000);
            readerwritelock.ReleaseWriterLock();

        }

       // read method
        public static void Read(object i)
        {
            Console.WriteLine("Thread: " + i + "ready to read...");

            // Acquire the read lock, with a timeout of 20 milliseconds
            readerwritelock.AcquireReaderLock(Timeout.Infinite);
            Console.WriteLine("Thread: " + i + "read operation" + DateTime.Now);
            // Release the read lock
            Console.WriteLine("Thread: " + i + "read end...");
            Thread.Sleep(1000);

            readerwritelock.ReleaseReaderLock();

        }
    }

// Shield the writer and reader methods respectively. You can see more clearly that the writer is blocked. The reader is not blocked.

//shield reader method
// thread: 1 ready to write...
//Thread: 1 write operation 2017/7/5 17:50:01
//Thread: 1 end of writing...
// thread: 2 ready to write...
//Thread: 2 write operations 2017/7/5 17:50:02
//Thread: 2 end of writing...

//shield writer method
//Threads: 3 ready to read...
//Threads: 5 ready to read...
//Threads: 4 ready to read...
//Thread: 5 read operations 2017/7/5 17:50:54
//Thread: 5 read end...
//Thread: 3 read operations 2017/7/5 17:50:54
//Thread: 3 read end...
//Thread: 4 read operations 2017/7/5 17:50:54
//Thread: 4 end of reading...
```



## <a id="WPF">WPF</a>

### **1.WPF consists of two parts? **

WPF consists of two main parts: the engine and the programming framework.

1 engine. The WPF engine provides a single runtime library for applications based on forms, graphics, video, audio and documents. Importantly, WPF's vector-based rendering engine gives applications the flexibility to take advantage of high-DPI monitors, supporting hardware acceleration of graphics.

2 frames. The WPF framework provides numerous solutions for media user interface design and documentation. WPF is designed with extensibility in mind, so that developers can create their own controls entirely on the basis of the WPF engine, or create their own controls by reclassifying existing WPF controls.
### **2.什么是WPF？**

The full name of WPF in English is Windows Presentation Foundation, and is the foundation of Windows presentation layer. It is a graphics platform originally launched by Microsoft on the .NET Framework3.5 platform.

### **3. Similarities and differences between Silverlight and WPF? **

They operate differently:

#### 1 silverlight is based on the browser plug-in and runs in the browser.

#### 2 WPF can write web programs or desktop applications, and can be directly compiled into exe files that run independently.

The implementation functions are different:

#### 1 WPF supports binding triggers directly in XAML to trigger animations, while silverlight can only be done through managed code or javascript.

#### 2 WPF directly supports 3D effects and 3D lens transformation, but silverlight does not.

They also have similarities:

Silverlight formerly known as WPF/E is WPF Everywhere is a subset of WPF. So they are very similar in many syntax implementations.

**4. How to understand the WPF architecture? **

WPF uses a multi-layer architecture, which is similar to a three-tier structure. The top layer is a managed code API. This layer is used to provide higher-level services for developers to write WPF applications, based on C# managed code. The work of converting .NET code to DirectX is implemented by the middle layer milcore.dll.

The middle layer milcore is implemented with unmanaged code, because it needs to be tightly integrated with DirectX and is sensitive to performance, that is, it consumes more resources and has a greater impact on performance.

### **5. What is the function and implementation syntax of Binding in WPF? **

A typical Binding has four important components: Binding target object (binding target object) target object property (target property) Binding data source (binding source) Path (used to specify the value to be obtained from the data source, which is the property name we usually write).

### **6. What is the XML extension XAML? What are the advantages? **

1 XAML is the English abbreviation of eXtensible Application Markup Language, which corresponds to the Chinese name of Extensible Application Markup Language. It is a new descriptive language created by Microsoft Corporation for building application user interfaces.

2 XAML is a derivative of the XML language, its syntax is completely consistent with the XML language, and its function is to design and implement the UI of the program.

3 A great advantage of XAML is that because WPF supports WEB development, the conversion between WEB development and desktop development is very simple, and the modification is very short. At the same time, the UI and logic are completely separated, so the logic code hardly needs to be changed.

### **7. Explain what a dependency property is and how is it different from the previous properties? Why would it be used in WPF? **

1 Windows Presentation Foundation (WPF) provides a set of services that can be used to extend the functionality of common language runtime (CLR) properties, often collectively referred to as the WPF property system. Properties supported by the WPF property system are called dependency properties.

2 It differs from previous attributes in that

(1) A dependency property is a specific type of property. This property is special in that its property value is tracked and influenced by a dedicated property system in the Windows Runtime.

(2) The purpose of dependency properties is to provide a systematic way to calculate the value of properties based on other inputs (other properties, events, and states that occur inside the application when it is running).

(3) A dependency attribute represents or supports a specific feature of the programming model used to define a Windows Runtime application, which uses XAML to write the UI and uses C#, Microsoft Visual Basic, or Visual C++ Component Extensions (C++/CX) to write the code.

General attributes are not so complicated

3 There are many advantages of using it in WPF

(1) The storage of attributes is optimized, which directly reduces unnecessary memory usage.

(2) There are attribute change notifications, restrictions, verifications, etc.

(3) It can store multiple values, and cooperate with Expression and Animation to create a more flexible usage method.

### **8. WPF中什么是样式？**

First of all, understand that styles in WPF are an important type of resources.

At the same time, a style is also a collection of attribute values ​​that can be applied to an appropriate element, or a set of attributes can be applied to multiple elements.

Styles in WPF can set any dependency property.

Styles in WPF also support triggers, which trigger a set of activities through property changes, including changing the style of a control.

Elements in WPF can only use one style.

Styles have the property of inheritance, and styles can inherit styles.

### **9. What is a template in WPF? **

Templates in WPF are used to define or redefine the control structure, or the appearance of objects.

There are two types of templates in WPF, one is the control template (ControlTemplate) and the other is the data template (DataTemplate), both of which are derived from the FrameworkTemplate abstract class.

There are a total of three templates ControlTemplate, ItemsPanelTemplate, DataTemplate.

1 The main purpose of ControlTemplate is to change the appearance of the control. It has two important attributes: VisualTree (visual tree) content attribute and Triggers trigger. You don't need to think too much about triggers, and triggers are optional. VisualTree is to present the controls we draw. Triggers can make some changes to the elements on our visual tree.

2 ItemsPanelTemplate is a special space template, which is mainly used to indicate how a multi-item control displays multiple items of data it contains. Can also be said to specify the layout of the panel used for the item. Multipurpose target for multiple content controls. Most of them are Panel attributes or attributes at the end of Panel.

3 DataTemplate is mainly used for data presentation. Also known as a template for displaying bound data objects.

[WPF](https://so.csdn.net/so/search?q=WPF&spm=1001.2101.3001.7020) has control templates and data templates. Control templates allow us to customize the appearance of controls, while data templates define how data is displayed, that is, the visual structure of data objects. But there is a problem to consider here, how is the data displayed? Although the data template defines the visual structure of the data, we clearly know that only the control is visible, and the data is generally carried by the control. Here, another object, ContentPresenter, is needed.

### **10. Basic usage of Binding**

There are three types in WPF: Binding, PriorityBinding, and MultiBinding. The base classes of these three Bindings are BindingBase, and BindingBase inherits from MarkupExtension.

Common methods of using Binding are:

1 For controls inherited from FrameworkElement. SetBinding(DependencyProperty dp, String path), SetBinding(DependencyProperty dp, BindingBase binding), where SetBinding in FrameworkElement is only valid for DependencyProperty.

2 The other is BindingOperations.SetBinding(currentFolder,TextBlock.TextProperty,binding);

The prototype of BindingOperations.SetBinding is

public static BindingExpressionBase SetBinding(DependencyObject target, DependencyProperty dp, BindingBase binding)

3 Clear Binding:

BindingOperations.ClearBinding(currentFolder,TextBlock.TextProperty);//Delete the TextBlock.TextProperty binding on currentFolder

BindingOperations.ClearAllBindings(currentFolder);//Delete all bindings on currentFolder.

Binding can also be released by directly assigning a value to Dependency Property, but it is only valid for one-way binding.

### **11. Explain the functions and relationships of these classes: Visual, UIElement, FrameworkElement, Control. **

The relationship between them: From the perspective of the System.Windows.Controls namespace, the sequential inheritance relationship is:

Visual inherits UIElement, UIElement inherits FrameworkElement, and FrameworkElement inherits Control.

1 The main role of Visual is to provide 2D rendering support for WPF, mainly including output display, coordinate transformation, area clipping, etc.

2 The main role of UIElement is to build the base class for WPF elements and basic presentation features. For example, it defines many features related to input and focus, such as keyboard events, mouse, and some APIs related to WPF event model.

3 The main function of FrameworkElement is to add some functions to the defined WPF elements. For example, layout definitions, logical trees, object lifecycle events, support for data binding and dynamic resource references, support for styles and animations.

The primary role of 4 Control is to provide the basis for custom application controls. Because it is the base class for creating custom application controls, its role is to rewrite the properties, methods, events, etc. provided by the Control class to add custom logic to custom controls. The Window class that builds the pages of a WPF application is also derived from it。

### **12. Visual Tree VS Logical Tree?**

1 The logical tree is a subset of the visual tree, that is, the visual tree is basically an extension of the logical tree.

2 WPF solves the problem of dependency property inheritance and resources through the logical tree, and uses the visual tree to handle rendering, event routing, resource positioning and other issues.

3 The logical tree can be thought of as what XAML sees, while the visual tree contains the structure inside the XAML elements.

4 The logic tree can be searched through the LogicalTreeHelper auxiliary class, and the visual tree can be searched through the VisualTreeHelper auxiliary class. It should be noted that the search for the ContentElement element cannot be directly searched through the VisualTreeHelper. The ContentElement element does not inherit Visual, and the use of the ContentElement element requires a ContentElement carrier FrameworkContentElement.

Six property change notifications (INotifyPropertyChanged and ObservableCollection)

1 INotifyPropertyChanged Notifies the client of a property value change.

2 The ObservableCollection class, which is a built-in implementation of data collections that implement the INotifyCollectionChanged interface. Represents a dynamic data collection that provides notifications when items are added, removed, or the entire list is refreshed

### **13. Explain ResourceDictionary? **

Provides a hashtable/dictionary implementation of WPF resources used by the Contain element and other elements of a WPF application.

Conducive to resource sharing in the project.

### **14. What are the three methods/strategies for routing events (bubbling direct tunneling)? **

The routing event in WPF is passed along the VisualTree, which is used to call the handlers on the various listeners on the element tree of the application.

(1) Bubbling, this event processing method is to flow from the source element to the upper layer until it reaches the root node, which is the top node, which is generally the outermost control.

(2) Directly, this processing method is processed on the source, and is mainly used for processing on the source element. It is usually reflected in the setter and trigger. I personally think that the visual state of VisualState may also be direct event processing, and it is also a state change of dependent properties. There is a certain overlap with Trigger, but VisualState indirectly realizes the change of dependency properties through blunt animation.

(3) Tunnel, also known as Preview event, is the opposite of bubbling event processing. Event handlers are invoked at the root of the element tree, and down to the source element position.

Tunneling events and bubbling events generally appear in pairs. The same event, when executed, is first a tunneling event and then a bubbling event.

### **15. Explain Routed Events and Commands? **

Event and Command are the basis of program internal communication. Routed Events can initiate multiple controls and communicate with users in an orderly manner.

Commands are the core framework provided by the .NET Framework to activate and deactivate high-level tasks.

The animation derived from this is a further step of events. Allows you to use the Event architecture in a friendly and interactive way to use multiple controls.

### **16.Template**

There are three types of WPF templates:

ControlTemplate,

DataTemplate,

ItemsPanelTemplate,

They all inherit from the FrameworkTemplate abstract class. In this abstract class, there is a VisualTree variable of type FrameworkElementFactory, through which you can set or get the root node of the template, which contains the appearance element tree you want.

(1) Control template. Control templates can apply custom templates to all controls of a particular type, or to an instance of a control. It is the ControlTemplate that determines the appearance of the control, which determines what the control "looks like", so the control template is represented by the ControlTemplate class. Control templates are actually defined in resource collections or resource dictionaries. For example, see: [Creating rounded text boxes and rounded buttons by designing ControlTemplate](https://www.cnblogs.com/zhouhb/p/3284780.html)(http://www.cnblogs.com/zhouhb/p/3284780.html). (2) Data template. In WPF, it is the DataTemplate that determines the appearance of the data, that is, the DataTemplate is the representation of the data content. What a piece of data looks like, whether it is simple text or intuitive graphics, is determined by the DataTemplate. For examples, see: [DataTemplate Application] (https://www.cnblogs.com/zhouhb/p/3284827.html) (http://www.cnblogs.com/zhouhb/p/3284827.html).

(3) ItemsPanelTemplate template. ItemsPanelTemplate Specifies the panel used for the layout of items. As in the following example, by setting the ItemsPanelTemplate of the ListBox, each item is displayed in order from left to right.

#### **1, ControlTemplate**

ControlTemplate: The control template mainly has two important properties: VisualTree content property and Triggers trigger. The so-called VisualTree (visual tree) is to present the controls we draw. Triggers can make some changes to the elements on our visual tree. Typically used for single-content controls.

#### **2、ItemsPanelTemplate**

The explanation of ItemsPanelTemplate in MSDN is: ItemsPanelTemplate specifies the panel for the layout of the item. [GroupStyle](http://msdn.microsoft.com/en-us/library/system.windows.controls.groupstyle.aspx) has a [Panel](http://msdn.microsoft.com/en-us/library/system.windows.controls.groupstyle.panel.aspx) property of type ItemsPanelTemplate. The [ItemsControl](http://msdn.microsoft.com/en-us/library/system.windows.controls.itemscontrol.aspx) type has an [ItemsPanel](http://msdn.microsoft.com/en-us/library/system.windows.controls.itemscontrol.itemspanel.aspx) property of type ItemsPanelTemplate .

Let's talk about ItemTemplate first. It is generally used in templates with multiple content controls. Such as ListBox.

#### **3, DataTemplate and HierarchicalDataTemplate**

DataTemplate is a template that displays bound data objects.

Data Template in WPF (DataTemplate) In WPF, we can customize the display method for our own data, that is to say, although a certain data data is certain, we can make it display in various ways

HierarchicalDataTemplate inherits from DataTemplate, which is specially bound to some data objects of TreeViewItem or MenuItem.

### **17.Triggers**

Triggers in WPF are a kind of encapsulation of WPF events. There are many ways to trigger WPF, which can be summarized as follows:

- Control trigger (FrameworkElement.Triggers collection)
- Style triggers (Styles.Triggers collection)
- Control template trigger (ControlTemplate.Triggers collection)
- Data template trigger (DataTemplate.Triggers collection)

Trigger type

System.Windows.DataTrigger data trigger

System.Windows.EventTrigger event trigger

System.Windows.MultiDataTrigger multi-data trigger

System.Windows.MultiTrigger multi-attribute trigger

System.Windows.Trigger property trigger

#### **1. Property trigger Trigger**

As the name implies, attribute triggers are caused by changes in attributes, so attribute triggers have several key attributes:

Property: Gets or sets the property to compare the returned value with [Value](https://msdn.microsoft.com/en-us/library/system.windows.trigger.value.aspx) The property of the trigger. The comparison is a reference equality check. (only dependency properties are useful)

Value: Gets or sets the value to compare with this element's attribute value. comparison is a reference equality check

Setter: Get a set of [Setter](https://msdn.microsoft.com/zh-cn/library/system.windows.setter.aspx) objects, describing the attribute values ​​to be applied when the specified conditions are met.

Setter: The property is a collection of SetterBaseCollection.

#### **Setter class:**

Property: The property to get or set and its Value to apply. **(Only useful for dependency properties)**

Value: Gets or sets the value Setter to apply to the specified property.

The following code specifies the use of attribute triggers, because attribute triggers are generally used in style definitions.

​ ![0](https://note.youdao.com/yws/res/d/WEBRESOURCEa1826240762bf6d88f0d0963734953cd)

```xaml
<Button Content="Trigger" x:Name="btn1" Click="Button_Click">
<Button. Style>
<Style>
<Setter Property="Button. Width" Value="80"></Setter>
<Setter Property="Button. Height" Value="100"></Setter>
<Style. Triggers>
<Trigger Property="Button. IsMouseOver" Value="true">
<Trigger.Setters>
<Setter Property="Button. Width" Value="200"></Setter>
</Trigger.Setters>
</Trigger>
</Style. Triggers>
</Style>
</Button. Style>
</Button>
```



#### **2. Multi-attribute trigger MultiTrigger**

A multi-attribute trigger is a trigger that is triggered when one or more styles change. It has an important attribute Condition, which is used for statistical conditions. MultiTrigger.Conditions is a collection of Conditions. A simple case can illustrate:
```xaml
 <Style>
      <Setter Property="Button.Width" Value="80"></Setter>
      <Setter Property="Button.Height" Value="100"></Setter>
      <Style.Triggers>
              <MultiTrigger>
               <MultiTrigger.Conditions>
                        <Condition Property="Control.IsFocused" Value="true"></Condition>
                        <Condition Property="Control.IsMouseOver" Value="true"></Condition>
                   </MultiTrigger.Conditions>
               <Setter Property="Button.Width" Value="300"></Setter>
              </MultiTrigger>
       </Style.Triggers>
 </Style>
```


#### **3. Event trigger EventTrigger**

The event trigger is the information triggered by the operation of the control. In the EventTrigger property, several key properties are:

RoutedEvent: Gets or sets the RoutedEvent that will fire this trigger.

Actions: Gets a collection of actions to apply when the event occurs.

It is mainly used to specify routing events and Actions, so what are Actions? It is defined as follows:
```C#
public TriggerActionCollection Actions { get; } 
```



t is a collection of TriggerAction

**System.Windows.Interactivity.TriggerAction**

​ [Microsoft.Expression.Interactivity.Core.CallMethodAction](https://msdn.microsoft.com/zh-cn/library/microsoft.expression.interactivity.core.callmethodaction(v=expression.40).aspx)

​ [Microsoft.Expression.Interactivity.Media.PlaySoundAction](https://msdn.microsoft.com/zh-cn/library/microsoft.expression.interactivity.media.playsoundaction(v=expression.40).aspx)

​ [Microsoft.Expression.Interactivity.Media.StoryboardAction](https://msdn.microsoft.com/zh-cn/library/microsoft.expression.interactivity.media.storyboardaction(v=expression.40).aspx)

​ [System.Windows.Interactivity.InvokeCommandAction](https://msdn.microsoft.com/zh-cn/library/system.windows.interactivity.invokecommandaction(v=expression.40).aspx)

Therefore, event triggers can only call CallMethodAction, PlaySoundAction, StoryboardAction, and InvokeCommandAction. Therefore, in the general application and animation of event triggers, we take StoryboardAction as an example. We know that StoryboardAction is a package of Storyboard, which mainly encapsulates the action BeginStoryboard. This action is equivalent to calling the BeginAnimation method. Our case is as follows:

```xaml
   <Button Content="Trigger" x:Name="btn1" Click="Button_Click">
            <Button. Style>
                <Style>
                    <Setter Property="Button. Width" Value="80"></Setter>
                    <Setter Property="Button. Height" Value="80"></Setter>
                    <Style. Triggers>
                        <Trigger Property="Button. IsMouseOver" Value="true">
                            <Trigger.Setters>
                                <Setter Property="Button. Width" Value="300"></Setter>
                            </Trigger.Setters>
                        </Trigger>
                    </Style. Triggers>
                </Style>
            </Button. Style>
        </Button>
```

#### **4. Data Trigger DataTrigger**

Data triggers are used to trigger data changes. DataTrigger also has two important properties

Binding: Gets or sets the binding that generates the property value of the data object.

Value: Gets or sets the value to compare with this data object's property value.

The case code is as follows:

```xaml
<Style>
     <Style. Triggers>
        <DataTrigger Binding="{Binding RelativeSource={RelativeSource Self},Path=Text}" Value="8">
            <Setter Property="Button. Foreground" Value="Sienna"></Setter>
            <Setter Property="Button. FontWeight" Value="Bold"></Setter>
            <Setter Property="Button. FontSize" Value="30"></Setter>
         </DataTrigger>
     </Style. Triggers>
</Style>
```

#### 5. MultiDataTrigger MultiDataTrigger

The multi-data trigger is an extension of the data trigger, which needs to be executed only when multiple data changes are satisfied at the same time.

We have made a simple classification of 5 types of triggers in 4 triggering environments. Although some triggers can be used in some triggering methods, it does not mean that they are necessarily effective. This is distinguished according to the situation during use. The classification is as follows:

| Trigger method | Available triggers | Description |
| ------------ | ------------------------------------------------------------ | ------------------------ |
| Control triggering | EventTrigger must be used | Used to adjust the interface, realize animation, etc. |
| Style trigger | TriggerEventTriggerMultiTriggerDataTriggerMultiDataTrigger | All triggers available |
| Control template trigger | TriggerEventTriggerMultiTriggerDataTriggerMultiDataTrigger | All triggers available |
| Data Template Trigger | TriggerEventTriggerMultiTriggerDataTriggerMultiDataTrigger | All Triggers Available |