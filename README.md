# LearningsAndNotes
Important points across all languages and technologies

Java

JVM architecture
http://youtube.com/watch?v=ZBJ0u9MaKtM
1.Class loader subsystem
	i).Load
		-loads all class files generated
		1.Bootstrap class loader - loads class files from rt.jar
		2.Extension class loader - loads class files from folder jre/lib/ext
		3.Application class loader - loads class file from CLASSPATH
	ii).Link
		1.Verify - verifies if java byte code is as per standards
		2.Prepare - Memory is allocated for static class variables (not for instance variables)
		3.Resolve - resolves the references to other classes etc
	iii).Initialise 
		- used for executing static blocks and assigning actual values into memory
	-classNotFoundException and NoClassDefException are thrown from here. Find out the differences between them
2.Runtime data areas
	i)Method Area:
		-storage area for compiled code
		-its container per class elements like, methods code, constant pool, fields, method local data, constructor code
		-XX:MaxPermSize - also called PermGen memory (by default 64MB)
		-it can throw OutOfMemoryError
		-It is called metaspace from java-8. The memory has no limit here. It can grow.
	ii)Heap
		-all the objects are created here
		-It can throw OurOfMemoryError
		- -Xms(min), Xmx(max)
		-divided into young gen and old gen
		-refer memory model below
	iii)Stack
		-it has memory allocated for currently executing method
		-each thread has its stack created
		-StackOverFlowError
		-Last In First Out
		-Xss - set the size of stack
	iV)PC Registers
		-Program Counter Register
		-All threads available here
		-it has next instruction/line of code to execute
	v)Native Method stacks
		-platform specific native libraries
3.Execution engine
	i)Interpreter
		-takes byte code and implements native methods like .dll and .so
	ii)JIT 
		-compiles the set of instruction and does not interpret if the code ie repeated
	iii)Hotspot profiler
		-grabs lot of statistics on the byte code running
	iv)Garbage collectors


JVM Memory Model:
-https://www.journaldev.com/2856/java-jvm-memory-model-memory-management-in-java

￼
Divided into above sections
-Newly created objects are located in Eden space.
-If the Eden space is full, minor GC happens and survivor objects will be moved to survivor spaces.
-At any time, one of the survivor space is empty.
-Objects that are survived after threshold number of cycles in young generation are moved to Old generation.
-Old generation garbage collection is called Major GC.

-Minor GC is fast while Major GC takes long time and sometime application may hang and times out.


Permanent generation:
-it has application metadata required by the JVM to describe classes and methods used in application.
-Perm Gen is not part of Heap

Memory Pool:
-created by JVM memory managers for creating pool of  immutable objects like Strings.
-it can be part of Heap or Perm Gen depending on JVM memory manager implementation.


Garbage Collection:
-Garbage collector is a program running in background that look into all the objects in the memory and finds out which ones are not referenced by any part of the program.
-all these unreferenced objects are deleted for new objects to be created.
-can be called programmatically as below
System.gc(); or
Runtime.getRunTime.gc();



Serialization and Deserialization:
-Anytime if we want to write an object into a file or send over a network or use in socket API, we need to serialise the object.
-Similarly, if we want to read from a file, use deserialisation.

Serialisation:
FileOutputStream for = new FileOutputStream(“EmployeeObject.ser”);
ObjectOutputStream oos = new ObjectOutputStream(fos);
oos.writeObject(new Employee(“chai”, 34);
oos.close();
Fos.close();

DeSerialisation:
FileInputStream fis = new FileInputStream(“EmployeeObject.ser”);
ObjectInputStream ois = new ObjectInputStream(fis);
Employee employee = (Employee) ois.readObject();



Collection API

1.How to select the correct collection in your application?
2.Java 8 topics -
Lamdas
Stream API
Functional Interfaces
Method References
3.internal working of Hashmap
4.hashmap vs concurrent hashmap


1.How do you choose collections?



1.What fail-fast and fail-safe?
Mainly this behaviour comes for multithreading programming and when concurrent operations are happening
fail-fast: throws an exception whenever there is concurrent operation is happening on the same object
	eg: iterator onArrayList, Vector, HashSet.
fail-safe: when the cloned object is being modified concurrently without impacting the actual object is called fail-safe
	eg: iterator on ConcurrentHashMap, copyOnWriteArrayList




Multi Threading:
1.What is Thread:
A.Thread is an independent path of execution. A small portion of a task is Thread.
	A process can have multiple threads.
Implementation:
	1.class extends a Thread. call start method. Class should have run() method which is started by start method. Thread class itself implements Runnable interface.
	2.class implements Runnable. Create an object for Thread and pass the object of class which implements Runnable interface and call the start method.
	
2.Why do we have 2 different implementations for Threads?
A.Java does not support multiple inheritance and can’t extend Thread class if one class is already extending something else
Hence Runnable interface is provided with which we can implement Runnable interface and call start method.

3.Difference between start() and run() method?
start() method will create a new Thread and executes the task in new Thread.
run() method does not create a new Thread and executes task in the current thread (ex: in the main thread only)
We cannot call start() method twice on thread object. For the second call it throws IllegalStateException.
But we can call run() method twice.

4.What is volatile in java?
-Its a keyword in java used for declaring before instance variables. Not to be used for class or methods
-Lets say if any variable needs to be shared by multiple threads, we declare that variable as Volatile.
-so that all the threads must see the latest value of volatile variable
-happens before guarantee - lets say a thread is modifying on the variable and another thread is reading from the variable. So java makes sure that modifying takes place first and visible to other threads to read from it.

5.How to stop a Thread?
Thread automatically stops once its execution is completed. 
If we want to stop it manually, call stop() method on thread. But its deprecated because of thread-safety issues.

6.How to communicate data between multiple threads?
-Use shared object so that multiple threads can access.
-make it synchronised
-use notify(), notifyAll() and wait() methods for communication

7.difference between notify() and notifyAll()?
-notify() method wakes up any one of the threads waiting for the object.
-notifyAll() method wakes up all the threads waiting for object. But only one thread gets access to utilise the object depending on the thread scheduler pattern.

8.Why wait(), notify() and notifyAll() methods in Object class instead of Thread class?
-One reason which is obvious is that Java provides lock at object level not at thread level. Every object has lock, which is acquired by thread. Now if thread needs to wait for certain lock it make sense to call wait() on that object rather than on that thread. Had wait() method declared on Thread class, it was not clear that for which lock thread was waiting. In short, since wait, notify and notifyAll operate at lock level, it make sense to define it on object class because lock belongs to object. 

9.wait and notify methods should be called from synchronised block or methods.
Otherwise they will throw IllegalMonitorStateException. It is to avoid race condition for wait and notify methods.

10.Difference between synchronised and concurrent collection?
-synchronised collections(hash table, hashset) lock whole object while concurrent collections (concurrentHashmap, CopyOnWriteArrayList)) does not lock whole block but a portion of it.
-concurrent HashMaps divides the whole map into several segments(by default 16) and locks only relevant segments which allows multiple threads to allow access to other segments.

11.Producer and consumer problem using Threads?
-Need to use BlockingQueue,
-https://www.java67.com/2015/12/producer-consumer-solution-using-blocking-queue-java.html

12.Dead lock and how to avoid it?
-If Thread-1 is holding object A and wanting another object B and
Thread-2 is holding object B and wanting another object A, then the program goes into deadlock. 
-providing orderly access of objects to threads will avoid deadlocks.
https://javarevisited.blogspot.com/2018/08/how-to-avoid-deadlock-in-java-threads.html#axzz6hQjvElWA
13.yield() - it pauses the current thread and give CPU to access other threads. There are chances the same thread can get allocated to use again.

14.Immutable objects in java:
-declare class as final, 
-declare all variables private and final
-properly create constructor with out any leaks
-provide no setters
Advantages of immutable classes in java
	-Thread safe by default and can be shared without synchronisation in concurrent environment
	-boosts performance because of no usage of synchronisation keyword.
	-these can be cached and reused

15.how to create a singleton object in java and what is double checked locking of singleton?
https://javarevisited.blogspot.com/2014/05/double-checked-locking-on-singleton-in-java.html#axzz6hQjvElWA 
 


Exception handling:
￼

Checked vs Unchecked exceptions
-checked are identified at compile time while unchecked are thrown at runtime.
-checked eg: IOException(File reader)
-uncheck eg:NullPointer, Arithmetic exception

Exception Handling in OSMP:
-converts all exception into OSPApplicationException or OSPSystemException based on the scenario
If there is any internal code exception throw OSPApplicationException
If there is any exception related to calling web service, throw OSPSystemException
Code snippet:

-new OSPApplicationException(“1124”, “static message related to exception”);
-OSPApplicationException extends OSPException
-OSPException extends RuntimeExeption
-here, constructor uses the error code and static message.

-OSPSystemException is also same as above but this is thrown when below exceptions occur
	-ServiceClientFaultException extends Exception - when faults are present in response
	-serviceClientMarshalException - when unable to marshal the request
	-ServiceClientIOException - thrown when unable to connect service

Custom Exception resolvers:
<bean name=“/exceptionResolver”, class = com.wf.presentation.controller.ExceptionHandler”>
<property name=“resolver”>
	<map>
		<entry key=“1234” value-ref=“creditCardCustomExceptionResolver”>
		<entry key=“1188” value-ref=“depositCustomExceptionResolver”>

	
Public class CreditCardCustomExceptionResolver extends HandlerExceptionResolver (from spring mvc)
{
@Override
	Public ModelAndView resolveException(HttpRequest r, HttpResponse rs, Object o, Exception e)
	{
		1.set model data that needs to be displayed and add to http request object
		2.set view name error_page.jsp
		3.return new ModelAndView(view);
	}
}

try with resource from Java -7
-try(BufferedReader br = new BufferedReader(new FileReader(“c:/textFile.txt”); ){
syso(“inside try”);
catch(Exception e)
{
syso();
}
	-used for auto closing resources 
	-java takes care of closing the resource once it is used
	-more readable
	-avoids memory leaks - earlier developers have to write code to close the resources explicitly in finally block. Chance that they forget.


Enums:
	-by default static and final and thread safe.

Design Patterns:
1.Creational
	i)Factory
	ii)Builder
	iii)Singleton
	iv)prototype
2.Structural
	i)Facade
3.Behavioural

Ex:
Factory










