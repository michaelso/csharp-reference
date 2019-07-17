## [Manage Program Flow (25-30%)](https://www.microsoft.com/#syllabus-1)

Fundamentals of multi-process programming

Features and libraries that let you work with process

How process are created, managed, communicate

Features for doing error management

Fundamental elements that control program flow

# Implement multithreading and asynchronous processing

# Use the Task Parallel library, including theParallel.For method, PLINQ, Tasks;

Task:

an abstraction of a unit of work to be performed

  Work: described by c# program code by method or lambda expression

 May perform concurrently with other tasks

Task parallel Library (TPL):

 Range of resources to perform task

  **Task.Parallel** class – 3 methods – System.Threading.Tasks

Parallel.Invoke([Action delegates (delegates in lambda usually)])

Action: encapsulation of method w/o para or return result

e.g. ( ()=\&gt;Task1(), ()=\&gt;Task2() )

can start a large no. of task at once

no control over the order which start first or to which processor

Return: when all of the tasks are completed

Parallel.ForEach(_IEnumerable collection_, _Action)_

 Tasks not perform in the same order

Parallel.For(start\_counter, length\_of\_array, delegate in lambda pass in a variable provides the counter value)

Managing Parallel.For, Parallel.Foreach

Pass in &quot;ParallelLoopState&quot; into the lambda function

Call Stop() method

 NO GUARANTEE!!! Because no in order!

Maybe lower index (the condition) not performed

Call Break() method

Lower index are guaranteed to be completed

**Parallel LINQ**

 AsParallel()

See if a parallel version would speed up

**May not go parallel** if program is not sure have performance gain or not!

**Informing parallel:**

AsParallel().WithDegreeOfParallelism(#\_of\_parallel).WithExecutionMode(ParallelExecutionMode.ForceParallelism)

Forced parallel!

Go Parallel, result maybe in different order!

AsOrdered()

Preserve the result order – might slow down -query might not execute in order

AsSequential()

Execute the query in order

**Iterate query elements**

ForAll ( item\_identifier =\&gt; method )

Different with &quot;foreach&quot;!

Parallel + start before query is completed!

**Exception**

Still the usual try catch, but have AggregatedException

**Task**

**Create a Task,** complex way **:**

 Task task\_identifier = new Task( () =\&gt; method\_identifier());

 task\_identifier.Start();

Simple way:

Task task\_identifier = Task.Run( () =\&gt; method\_identifier() );

Both need wait to finish:

 task\_identifier.Wait();

**Return a value from a task**

Generic style to define type: Task **\&lt;return\_value\_type\&gt;** task\_identifer = Task.Run( lambda expression of the work)

Get result by: Task. **Result**

Task.Run use TaskFactory.StartNew to create and start the task!

Task class expose _Factory property_ that refers to the default task scheduler

**Wait**

Task.Waitall( [array\_of\_tasks] );

Since task create and run is ASYNC, i.e. return immediately, so we have to &quot;wait&quot; explicitly and let the wait block.

**Note:** The value loop counter in &quot;for loop&quot; use for the task creation must be copied to a local variable in order to be used in the lambda expression correctly!

**Wait( task ), WaitAll( [array\_of\_tasks] ), WaitAny( [array\_of\_tasks] )**

&quot;Any&quot; will return after the 1

# st
 task has finished

**Exception**

Still the usual try catch, but have AggregatedException

# continuation tasks

continuation task can be started after an existing task (the antecedent task) finishes

can choose to use the result from the antecedent task in the new task, if any

use to create &quot;pipeline&quot; of operations

**create a continuation task**

Call task object&#39;s ContinueWith(), pass in next task as method / lambda expression

Antecedent\_Task\_Identifier.**ContinueWith(action\_delegate, options)**

**action\_delegate** : **(previous\_task) =\&gt; new\_task() then new\_task can use previous\_task.Result**

continuation options: TaskContinuationOptions

.OnlyOnRanToCompletion (for previous task complete successfully)

.OnlyOnFaulted (for exceptions)

NotOnFaulted

OnlyOnCanceled

NotOnCanceled

NotOnRanToCompletion

**Child task**

Task creating more tasks inside it

**Detached** child tasks / detached nested tasks: Child tasks execute independently of the parent. This is **default** mode! **TaskCreationOptions.DenyChildAttach**

**Attached** child tasks: parent will not complete until all attached child tasks have completed.

**Task.Factory.StartNew(lambda expression, state object, TaskCreationOptions.AttachedToParent);**

**Note: Task.Run** has TaskCreationOptions. **DenyChildAttach** option

# Threads and ThreadPool;

Thread:

Lower level than tasks – object represent a process running within OS

Thread vs Task:

Thread: foreground, optional background

OS run foreground process till completion i.e. if contains infinite loop will run forever, or until it throws uncaught exception or OS kill it

Task: background

Can be terminated before completes if all foreground threads have completed

Priority: thread has i.e. request greater portion of computation power, task can&#39;t

Deliver Result: Thread cannot deliver result, must communicate via shared variables

Continuation: thread has no continuation. Use Thread.Join() to &quot;wait till complete&quot;

Aggregate Exception: thread cant aggregate, must be caught and dealt with in that thread

Create a Thread

Namespace: System.Threading

Thread thread\_identifier = new Thread(pass\_delegate\_by\_method\_identifier);

thread\_identifer.Start();

Note: past has to use ThreadStart to wrap the method as the delegate

Can start using lambda expression

new Thread( () =\&gt; { … } );

Pass data into a thread

Classic way:

ParameterizedThreadStart ps = new ParameterizedThreadStart(method\_identifier);

Thread thread\_identifier = new Thread(ps);

Thread.Start(_DATA\_to\_be\_pass\_in);_

Lambda way:

new Thread( (data\_identifier) =\&gt; { … }; );

thread.Start(data\_value)

Both ways:

_Data will be passed in as &quot;boxed&quot; object_

_Data value / the real pass of data, is at thread.Start()_

Aborting a thread

 Thread object expose an Abort() method

i.e. thread\_identifier.Abort();

but this is a BAD way!

Better to use a shared flag variable

i.e. While loop the condition, till external change it

Thread Synchronization using Join

Join() sync two threads, caller of join() wait/held till the thread being called to complete

Thread data storage and ThreadLocal

[ThreadStatic] attribute to &quot;localize&quot; the variable for each thread, but only work for static variables (class variable)

ThreadLocal\&lt;T\&gt; will generate a separate object for each thread, &quot;localized&quot;, work for instance variable (object variable) as well

Thread execution context

Thread instance expose a range of context information via property. Some of the context property are read only, some can be write and read.

**Thread**. **CurrentThread** static property can be used to discover itself

With a range of instance property (non-static, define for each object)

.Name

.Priority

.CurrentCulture

.ExecutionContext

.IsBackground

.IsThreadPoolThread

Thread pools

Store a collection of reusable thread objects, when a thread object completes, the object is returned to the pool for use by another process.

**ThreadPool.QueseUserWorkItem** (lambda expression) to allocates a thread to run the supplied item of work

Restricts number of active threads and maintains a queue of threads, too much threads will overwhelm a device

Don&#39;t use ThreadPool if:

Large no. of thread maybe idle for long time, i.e. IO bound

Cannot manage priority of threads

Cannot obtain foreground priority

Most importantly, Local state variables are not cleared

# Tasks and the User Interface; unblock the UI

Universal Application (Windows Store) Windows Presentation Foundation (WPF) or a Winform app use a **single thread** of execution **dealing with user interface**

If UI event handler is running in response to a particular event, its not possible for other part of the UI to execute.

i.e. a time consuming computation in the UI event handler will &quot;lock up&quot; the interface and make it unresponsive

Delegate the &quot;heavy work&quot; or computation to a Task!

However, its more complicated than normal Task run and get result as it will need a dispatcher property with RunAsync()

# use async and await keywords

Task is useful way to get things done as it let program partition and dispatch items of work, especially useful as it let the program to do something else while its working on a task already. It is like creating a branch and let it run in parallel to the existing one.

Downside: hard to manage!

Code for start the task, start the task

Ways to know the task has finished and maybe get the result

Catch the exception generated and passed back

Async and await keywords let programmers write asynchronous code elements easier

**Async** flag a method as &quot;asynchronous&quot;, the method must contain one or more actions that are &quot; **awaited**&quot;

Action can be &quot;awaited&quot; if it returns a Task or Task\&lt;t\&gt;

Compiler &quot;translate&quot; the &quot;await&quot; into &quot;callback/continueWith/continuation&quot; like structure, &quot;async&quot; tells the compile the &quot;await&quot; in the method is keyword, not identifier

Exception and async/await

Try/catch wrap the await statement

Can only catch exception if the async method return a result

Void return can not catch exception

For method just performance action, a statue value should be returned so that exceptions can be caught and dealt with

Keyword &quot;await&quot; – **syntactic sugar of ContinueWith()**

Awaiting parallel tasks

Task.WhenAll([array\_of\_tasks])

Create &quot; **a task**&quot; that &quot; **completes**&quot; when a given **lists of tasks have be completed**

Return order of the collection may not match the order of submitted

No aggregation of any exceptions thrown

Task.WhenAny( [tasks\_array] )

Return when 1

# st
 task in the list of tasks has finished

# manage data by using concurrent collections

Thread safe:

Code elements that work correctly when used from multiple processes (task) at the same time

NOT thread safe: List, Queue, Dictionary, many Standard .net collections

All Below are thread safe means to add and remove item to a data store:

_Blocking_ **Collection** \&lt;T\&gt;, basic, can as wrapper

_Concurrent_ **Queue** \&lt;T\&gt;, first in first out

_Concurrent_ **Stack** \&lt;T\&gt;, first in last out

_Concurrent_ **Bag** \&lt;T\&gt; unordered list

_Concurrent_ **Dictionary** \&lt;TKey, TValue\&gt;

Avoid: Task doing both produce and consume

Best: Task only either produce or consume

BlockingCollection

Take() action will block a task if no more items to be taken

Add() action will block a task if the collection is full

CompleteAdding() prevent more items to be added, hence

IsCompleted property will be True when the collection is empty and CompleteAdding() has been called

Exception:

IsCompleted is False, CompleteAdding(), Take()

Use TryAdd(), TryTake() which return Bool to avoid exception

Can act as wrapper for other concurrent collection classes

ConcurrentQueue – first in first out

Enqueue() add item – guaranteed to work, infinite length

TryDequeue() remove item

TryPeek() inspect what is at the start of queue, but TryDequeue() might not get that item as the item might be remove by other task already

foreach can be used

ConcurrentStack – first in last out

Push() add item

TryPop() remove

PushRange() add a number of items

TryPopRange() pop a number of items

TryPeek() same as Queue one

ConcurrentBag - unordered

Add()

TryTake()

TryPeek()

ConcurrentDictionary

**Atomic**!!! All actions cannot be interrupted by other tasks

TryAdd(key, value) – if item (key) exists already, return false

TryUpdate(key, new\_value, old\_value\_to\_be\_overwritten) – succeed only if the old value is correct

**AddOrUpdate** (key, value, lambda expression (key, value) =\&gt; { update } ) – if key not exist, create new, else, do the update using lambda expression



# **Manage multithreading**

# Side effect of running parallel is things becomes unpredictable in the order of execution as its hand over to the OS/framework and things become &quot;asynchronous&quot;. When doing &quot;Asynchronous&quot;, ensure the uncertainty about the timings of thread activities does not affect the result you want. There are design and practice to deal with the unpredictability.

# Key point is to use producer consumer pattern.

# Synchronize resources;

Race condition

For a statement to work on a shared variable between more tasks (non atomic!)

Steps: 1. Read 2. Update 3. Store (similar to extract, transform, load (ETL) but within a single computer program process)

A task can run till step 2, then being stopped, another task takes over control and complete all 3 steps, then go back to the previous task and store the value. However, the data in the shared variable will be incorrect!

Concurrent collections implemented a way to avoid the race condition!

# implement locking;

Locks

Ensure the action is Atomic

Atomic: perform to completion, cannot be interrupted

&quot;key&quot; like. The task has to get the key or wait for the key to return to run the statements inside the &quot;lock&quot; area. The statement will involve working on shared resource/variable

lock (shared\_lock)

{

 //inside here, operations are atomic

 Shared\_resource = ……

}

Side effect:

Might break the &quot;parallel&quot; execution, due to the tasks running in parallel need to access the shared resource too much, e.g. each of the task iteration

Solution:

Break down the task into more &quot;independent&quot; work.

Only update/summarize at the end

Reduced the access frequency need for shared resource

Code as short as possible in lock section!

Avoid blocking task in lock area

Lock: lock(obj){body} is syntactic sugar of

bool lockWasTaken = false;

var temp = obj;

try { Monitor.Enter(temp, ref lockWasTaken); { body } }

finally { if (lockWasTaken) Monitor.Exit(temp); }

the body of a lock should do as little as possible

as hard to handle exception and rollback

and avoid thread abortion

Ref: [https://blogs.msdn.microsoft.com/ericlippert/2009/03/06/locks-and-exceptions-do-not-mix/](https://blogs.msdn.microsoft.com/ericlippert/2009/03/06/locks-and-exceptions-do-not-mix/)

Monitors

Provide Similar actions to a lock, in different way, Enter(), Exit()

Monitor.Enter(shared\_lock)

// atomic code

Shared\_Resource = ……

Monitor.Exit()

If atomic code throws exception, must release in finally by calling Exit()

Monitor.Enter(shared\_lock);

Try

{

// code that might throw an exception

}

Finally

{

 Monitor.Exit(shared\_lock);

}

Monitor provide more than Lock

Monitor.TryEnter(lock\_object) can let the check whether or not it will be block waiting to enter the locked area

Lock doesn&#39;t have similar offer

TryEnter() can choose to &quot;wait&quot; for some time to see if it can get the &quot;key&quot; before give up and do something else

Deadlock

TaskS are waiting of each other to perform an action in the locked area

Example: Block adding item when the collection is empty,

Block removing item when the collection is full

Lock/key inside or waiting for another lock/key

Never &quot;nest&quot; lock inside another

Lock object

Any object manage by reference can be used as a locking object

Explicitly create an object() for lock

Avoid using &quot;this&quot;, other data object, especially string which the word might be re-use and due to the string nature, re-used word will share same reference, this might lead to problem when using as lock

Interlocked operations

Interlocked class provide a set of thread-set operation can be performed on a variable, including

Increment

Decrement

Exchange / swap

Add

Example

Interlocked.Add(ref shared\_resource, local\_resource);

Volatile variables

Make a variable not to optimize by compiler for order of execution

And fetch the copy in memory instead of CPU cache

Might need to Use it due to compiler optimization might create error in parallel code

# Cancel a long-running task;

Cancellation Token

CancellationTokenSource class

IsCancellationRequested property

The while loop in the task monitor the property,

if the property is True, the while loop in the task will break

Has time for the task to tidy up before leave

Cancel()

Set the IsCancellationRequsted to trueinde

Use outside the task, for killing task

Raise exception when cancel

cancellationToken.ThrowIfCancellationRequested();

# implement thread-safe methods to handle race conditions

Thread safety and member variable

Make use update method thread-safe by using Interlocking operation to update the member variable

At the same time, the design not impacting the performance a lot

Thread safety and method parameters

Pass by value, ok, its unique to method

Pass by reference, need handlings!!!

avoid passing object, use struct instead

atomic action that copies the object/data passed by reference to local variable of the method

# **Implement program flow**

# Iterate across collection and array items;

While(terminating condition) {…}– run till terminating condition is true, can be infinite

Do{……} while(condition) {…} – check condition after run once

For ( Initialization(); Test\_ terminating\_condition(); update() ) {…} – limited, not infinite

For( int local\_variable = 0; local\_variable \&lt; 10; local\_variable++)

Foreach – iterate collection, cannot modify value type, can modify reference type

Can iterate any object implement the IEnumerable interface via

GetIterator()

Lazy loading happen if its iterating the queries object, only fetch  the actual data when need

Item being iterate should not be changed, or will throw exception

Break

Above loop construct can be ended early by using break statement

But from design point of view, avoid using it if possible

Continue

Jump to testing the terminating condition, and skipping statements below it inside the loop

# program decisions by using switch statements, if/then, and operators;

selection statements, 2 types: if and switch

if( **logical\_expression** ) {…}

logical expression evaluates to true or false

execute the statements inside if the expression is evaluated to true

else {…} – optional, execute the statements inside if, bind to the nearest if construct

**logical expression** evaluates to true or false

Relational operators – compare two values

\&lt; less than

\&gt; greater than

\&lt;= less than or equal to

\&gt;= greater than or equal to

Use between variables of

numeric

string

evaluate alphabetically

equality operators – compare equality

== equal

!- not equal

Use between numeric and string as well

CANNOT use to test floating point

Subtract one from other, see if the absolute value is less than a given tolerance value

Logical operators

&amp;&amp; and

|| or

^ exclusive or

Short circuit

1

# st
 operand of &amp;&amp; is false, no need evaluate 2
# nd
 and can return false

1

# st
 operand of || is true, no need evaluate 2
# nd
 and can return true

Switch( **some\_value** ) – replace a long sequence of if-then-else constructions

Contain cases, each case statement is constructed with &quot;match expression&quot;, the expression evaluates to match **some\_value**

Case **match\_expression** : – if the value match, run the statements under it till &quot;break&quot; statement

Expect &quot;Mutually exclusive&quot; values for cases, meaning only match one case, not something like 2 and 1+1

MUST end the case with break, return or throw except, not &quot;fall through&quot; from the end of one case clause into another

Default: - if none matched, run statements under this one till break

&quot;luxury&quot; to have because can still live without it using nested if-then-else

Match expression support 2 type of patterns

&quot;Traditional&quot; – constant pattern

Can use: character, string, enumerated values

e.g. Case 1, Case &quot;small&quot;

Can group multiple related cases together, as it runs statements under it till break

Type pattern

Check the passed in object for type

Similar to the &quot;is&quot; operator, if return true, statements following case statement will be executed

If &quot;object&quot; is passed in, case null is needed to detect null

case statement works with &quot;when&quot; clause

cases no longer require Mutually exclusive values in cases

can match and run multiple cases!

type pattern with conditional expression which return a bool

case type\_pattern when conditional\_expression

ref: [https://love2dev.com/blog/csharp-switch/](https://love2dev.com/blog/csharp-switch/)

https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/switch#-pattern-matching-with-the-switch-statement

# evaluate expressions

expressions: comprise of operators and operands

operator specify action to be performed,

operand are literal values or variables

operator works on

one operand: unary/monadic ++ --

two operands: binary + - \* /

three operands: ternary - condition? True : false

operator use in different context give different result

addition operator use on numeric, add them, use on string, concatenate them. Incorrect context will cause compilation error

operator has priority / precedence that decide when it will perform expression evaluation

parenthesis can override the precedence, element inside parenthesis evaluate first

operators also have associability, left to right or right to left

# **Create and implement events and callbacks  **

Programs used to have a simple flow, from beginning to end in one way, starting with data input, do some processing, produce result.

Now programs aka modern apps are more complex, they **made up of many individual components working together** , each having own&#39;s input, process, result like simple programs; In order to cooperate and work as one single program or app, components **pass messages** from one to another.

Component, like mini program, can be created and tested individually before being integrated into the solution.

For components pass messages **using events** in C#.

# Create event handlers;

Event handlers

Before async await, event is generated to notify the async action has completed

Today events are more frequently use for inter-process communication

Object provide service to other by exposing public method, let others to initiate call, let other objects &quot; **pull**&quot; it

Event is a thing that happens

Event is the reverse, object notify like pushing, other object that something has happened. An object &quot;publish&quot; events which other objects can &quot;subscribe&quot;. One can do the **push** , the other can choose to receive the push.

Components of a solution that communicate using events are described as lossely coupled.

Delegates and events

Delegate contains a reference to a particular method. E.g. a phone number (reference) of yours (method)

An object can subscribe to an event by providing a delegate (method reference) to the delegate field/container of the event publisher

Event publisher which provides a container to store all the delegates being passed to it

When something happens, Event publisher will notifie the subscribers by **calling the delegates** stored inside the delegate field/container, which in turn call the methods of objects

This **indirect calling via delegate** , is for the sake decoupling the caller and the target method

Delegate type is defined by the event publisher to let other objects know what **kind of delegate** , specifically the parameter type and return type of a method, is expected from the subscriber.

Action delegate – one of many kinds of pre-defined delegate type

**Simplest** action delegate contains Reference to a method that does not return a result and does not accept any parameters

Usually check if there are any subscribers before calling

public Action delegate\_container\_identifer\_property { get; set }

other object &quot;subscribe&quot; by provider delegate:

oject\_instance.delegate\_container\_identifer\_property += own\_delegate\_identifer

when something happened, call delegate\_container\_identifer\_property**();**

# subscribe to and unsubscribe from events;

Event subscribers

+= operator:

subscriber bind to a publisher, &quot;overloaded&quot; to apply

not guarantee to be called in the same order as added in

All delegates are called using one thread during &quot;event publishing&quot;

Pitfall: if a delegate is blocking or takes long time, the entire publication is blocked

i.e. a badly written or malicious subscriber can block the publication of event

addressed by: use individual task to run each of the event subscribers

use GetInvokcationList() in Delegate object to get a list of all subscribers

use null conditional operator to see if the delegate container is empty

delegate\_field**?.Invoke();**

**.?** means access this member of the class if the reference is not null

**.** HasValue() return a bool, true for not null

delegate\_field != null, will be converted to .HasValue() by compiler

ref: https://stackoverflow.com/questions/676078/which-is-preferred-nullablet-hasvalue-or-nullablet-null

**Invoke()** to invoke methods in the delegates

-= operator: unsubscribe from the publisher/ unbind the method

If same subscriber or method is added to the delegate in publisher more than once, then it will be called according to the number of times being added

# What is event, and how to use built-in delegate types to create events;

Using &quot;events&quot;

Using public property is for containing delegate is bad idea!

Other can call the delegate

Other can override the delegate, removing subscribers that&#39;s not supposed to remove, by using &quot;=/assign&quot; operator

Using &quot;event&quot; construction/keyword to make the delegate container as an &quot;event&quot; which is a special type to handle this situation.

The construct become a data field (which is private usage) instead of property (which expect other to use)

**Event type** delegate vs Property type delegate:

External code can **only subscribe (+=) or unsubscribe (-=)** instead of able to do assign (=)

no more get/set behaviors as in property type

The delegate can **only be called within the class** where its declared instead

public **event** Action delegates\_container\_identifer = delegate {};

no need to check if the delegate &quot;has value&quot;/&quot;is null&quot; or not before calling

Note: Delegates form an event! Without delegates in the delegate container, or at least expect delegates to subscribe to it, its hardly an &quot;event&quot;.  

Note: The wording &quot;delegate&quot; is confusing, its used in different contexts, event subscriber and event publisher/host

The method reference which the object passed in to the event it wants to subscribe is called &quot;delegate&quot; for the object. i.e. passing the delegate

The container of containing the delegates being passed in, is also called &quot;delegate&quot; (can be declared as &quot;event&quot; contains the method references), for the object or class that &quot;host&quot; or publish the event. i.e. calling the delegate

EventHandler – a type of delegate

A proper delegate container use by broadcaster should use &quot; **EventHandler**&quot; class type instead of the class type of delegates use e.g. Action/Func delegate type

public _event_ **EventHandler** delegates\_container\_identifer = delegate { } ;

.net framework use EventHandler to manage events

EventHandler not only can be used to call the delegates, it also can **deliver data to delegates!**

EventHandler: pass two arguments to subscribers

Reference to the object raising the event

Reference to an EventArgs object which provide information about the event

Can be set to empty by EventArgs.Empty

The method reference provides by the subscriber must has the method signature corresponding to the EventHandler requirement

EventArgs: deliver event information

Create own type to contain the info by inherent EventArgs class

class someEventArgs: EvertArgs

{

 public _type_ SomeProperty { get; set;}

 public someEventArgs (_type someProperty)_

_{_

_       _ SomeProperty = someProperty;

}

}

Example of use:

public _event_ **EventHandler\&lt;someEventArgs\&gt;** delegates\_container\_identifer = delegate { } ;

void someDelegateBySubsriber( object source, someEventArgs args)

Note: EventArgs is passed by reference, subscriber might modify it and other subscriber will see the modification. This can be a source of unwanted side effects

Exception in event subscribers

If one of the subscribers has uncaught exception, this will lead to the end of event broadcasting, and no other subscribers will be notified. Due to the notifying action is handle by one thread, similar to the situation of being blocked

To resolve, don&#39;t call them in &quot;batch&quot;/&quot;in one shot&quot;:

Make the call individually and aggregate the exceptions being thrown

Use GetInvocationList() to get a list of delegate, then

Use foreach iterator to call each by using DynamicInvoke(), while

Use a try catch pattern in the DynamicInvoke(), then

Throw the exceptions aggregated (if any) at the end

# create delegates;

delegate type is declared using &quot;delegate&quot; keyword

using delegate for **event is only one of the use cases**

delegate can be use as &quot;plug-in&quot; method, replace hard-wiring,

delegate type has **multicast capability** , i.e. contain and call multiple method references

add or remove the method references by += or -=

example of declaring delegate type:

_delegate_ int IntOperation(int a, int b);

_delegate_ returnType method\_identifer(paraType1 para1, paraType2 para2);

**Note:** the method reference adding to the delegate **MUST follow the same method signature except method identifier** (types and orders of parameters, but not the name) **and return type** , defined by the delegate type.

Similar to a type defined list or array, you can only put in things (reference/value) into the list / array

Delegate: abstract class, delegate: keyword for compiler

Delegate treats &quot;behavior/action&quot; (method) as item of &quot;data&quot; (method reference; meaning can pass in and change instead of hardwired like the usual object method)

No hardwire == flexibility == not easy to use

Delegate type define the method delegate instance can call

Declare delegate type:

delegate int IntOperation(int a, int b);

declare delegate instance:

IntOperation add = (a, b) =\&gt; a + b;

# lambda expressions;

pure way of expressing **method without identifier** :

something goes in, something happens, something comes out

exactly like object method but without the identifier

sometime what it does can be guessed from the delegate type

e.g. from the return type, required parameters

delegate int IntOperation(int a, int b);

=\&gt; is called lambda operator – pronounced as &quot;go into&quot;

e.g. IntOperation add = (a, b) =\&gt; a + b;

&quot;a and b go into a plus b&quot;

on the left of =\&gt;

items (a, b) are mapped onto the method parameter defined by the delegate.

on the right of =\&gt;

Things can be block of multiple statements like a method body or in expression format

lambda: name comes from lambda calculus, a branch of mathematics that concerns &quot;functional abstraction&quot;

Closure:

Compiler extends the life of a variable if its used in lambda expression

Example:

delegate int _IntOperation_(int a, int b);

_IntOperation_ **add** = (a, b) =\&gt; a + b; //add is the delegate identifier

Console.WriteLine( **add** (2,2); // use add like as method identifier

Lambda + built in delegate type (Func, Action)

Func: generic delegate type that takes parameters, and return result

Func\&lt;int, int, int\&gt; add = (a, b) =\&gt; a + b;

Action: generic delegate type that takes parameters, but does not return result

static Action\&lt;string\&gt; logMessage = (message) =\&gt; Console.WriteLine(message);

Predicate: generic delegate type takes paramaters and return Boolean, True or False

Predicate\&lt;int\&gt; isEven = (i) =\&gt; i % 2 == 0;



# anonymous methods

piece of functional code without a name.

just like lambda expression but

must explicitly declare the input type on the let side of go into operator (=\&gt;)

(int x) =\&gt; instead of (x) =\&gt;

Must use statement block instead of expression

=\&gt; { **return** x \* x} instead of =\&gt; x \* x



# **Implement exception handling**

User error should not consider as exception, as it should be expected and deal with in normal running of solution.

Use exception to manage error that makes the program not meaningful to continue the current execution if the error occurs.

Write file but storage is full

Fetch a webpage but network is down

# Handle exception types, including SQL exceptions, network exceptions, communication exceptions, network timeout exceptions;

Indicate error condition by

**throwing an exception object** , that may be

being caught by an **exception handler**

exception handler - mitigate the effect of the exception

action such as:

alerting the user

creating a log entry

releasing resources

shut down the application in managed way

if exception is not caught by an exception handler

caught by .NET environment

cause the thread/task to terminate

fail silently with no message to users, confusing

if in foreground thread, will kill the app

exception can be nested, if thrown

.net runtime search up the call stack to find &quot;closest&quot; exception handler

Time consuming process – ONLY invoke in exceptional circumstances (not meaningful to continue)

Exception type

Parent type: Exception class

Each exception type describes particular error condition, many of those types

IOException – Input/Output operation

SqlException – invalid SQL query

List of SqlError items describe the error that occurred

For SQL error generates in LINQ query:

Will not throw during execution of query expression

But, produced within the code that is **iterating** through the result return by express

Reason: LINQ only begins evaluating the query when the results are requested

CommunicationsException – Windows Communication Framework

TimeOutExceptions – network operation takes too long to compete

Specifically handle particular exception type by

Matching catch clause

As Method signature doesn&#39;t indicate will exception be generated, so

Check the documentation!

# use try-catch statements;

try: code block subject to error handling or clean up

must be followed by catch block

catch: code block for handling exception if error occurs

access to &quot;exception&quot; object, which has the properties:

Message

StackTrace – position which the error occurred

HelpLink – further info about the exception

TargetSite – name of method that causes the exception

Source – name of the app or assembly (if app name isn&#39;t set) that causes error

# use base class of an exception;

order of the catch element is important!

put the most abstract exception type last in catch sequence

i.e. put catch (Exception ex) at last

more specific exception types first

or else, will end up getting the more &quot;general&quot; one

# implement try-catch-finally blocks;

finally block is

optional to have

guaranteed to run no matter having exception of not

use to release any resource the code may be using

finally block won&#39;t be executed only if:

preceding code, try block or catch block, enter infinite loop

Environment.FailFast() in try block explicitly request to skip finally block

# throw exceptions;

throw new Exception (&quot;exception message&quot;);

# rethrow an exception;

DO NOT hide/swallow errors, pass it up!

Low level handler might not recognize the exception

Alert higher level handler!

Rethrow:

catch (Exception ex)

throw;

// with NO parameter!

Bad practice:

catch (Exception ex)

throw ex;

BAD! Original stacktrace will be replace by the current one!

Pass as inner exception:

catch (Exception ex)

throw new Exception(&quot;error message&quot; **, ex** );

# create custom exceptions;

name of custom exception class should end with &quot;Exception&quot;

e.g. Custom **Exception**

**conditional clause** in catch block

keyword: **when**

**e.g.** catch (CalcException ce) **when** (ce.Error == Calc.Exception.CalcErrorCodes.DivideByZero)

# handle inner exceptions;

must be planned!

i.e. handler must be expecting inner exception

# handle aggregate exception

list of inner exceptions is called &quot;aggregate exceptions&quot;

occur when more than one thing can fail as operation is performed

or when series of actions need to be brought together

e.g.

catch (AggregateException ag)

{

foreach(Exception e in ag.InnerExceptions)

{

Console.WriteLine(e.Message);

}

}

Handle all uncaught exceptions subscribing to &quot;global&quot; exception handling events:

UI related:

UWP

Application.DispatcherUnhandledException

Windows Forms applications

Application.ThreadException

Non-UI:

on any unhandled exception on any thread

AppDomain.CurrentDomain.UnhandledException

useful as a backstop for logging and reporting bugs

can handle the exception, override to prevent app shutdown

Ref:

Book: c# in nutshell, p570

[https://docs.microsoft.com/en-us/dotnet/api/system.appdomain.unhandledexception?view=netframework-4.8](https://docs.microsoft.com/en-us/dotnet/api/system.appdomain.unhandledexception?view=netframework-4.8)

[https://docs.microsoft.com/en-us/dotnet/api/system.windows.application.dispatcherunhandledexception?view=netframework-4.8](https://docs.microsoft.com/en-us/dotnet/api/system.windows.application.dispatcherunhandledexception?view=netframework-4.8)



Exceptions as part of managed error handling

Check the method doc! Handle the exception might be throw, and propagate upward

Make sure not accidentally hiding the exception event

Own method should throw exception when not meaningful to continue

Deal with exception being thrown if still want the program to run at that point

Consider how to manage error during design

Very hard to add error management to an app once created

Throwing an exception, rather than returning an error code or a null value, is a way of ensuring that the program will fail in a properly noticeable way and maximizes the chances of the mistake being detected.

## Create and use types

Use OOP technique to make development of large scale solution easier

Break down program into components (break down by interface and interact via events) and can be worked on by many developers

Create custom data type to hold info that **match the problem requirements**

Might have behaviors allowing them to operate as software components that have integrity and provide service for other objects

How to generate object behaviors and mange life cycle of objects

Discover powerful features of string type

# Creating types

**implement behaviors in a problem domain**

Range of &quot;built-in&quot; types aka primitive types e.g. int, float

Technically, can write all programs with built-in types

However, create own data types make software development much easier and less error

Value and reference types

1

# st
 question when creating a type:

Value type or reference type

**Value** type:

Result of assignment: **value is**** copied** to new variable object

Value is as &quot;literal&quot; value, not underlying of something else

Two independent copies of values, changes in one won&#39;t affect other

**Reference** type:

Result of assignment: a **reference is copied** to new variable object refers to **an underlying object**

The two are &quot;linked&quot; to the same underlying object, so if one changes things, any other objects link to the same underling can see the changes

value and reference types provide by .net

Why struct is good?

DateTime struct – hold the values about date and time in a single value type

Alternative is to break the struct down into a collection of variables each contains one type of data, e.g. one for day one for month, one for year

this is more tedious and error probing

**Struct groups the related data together** to form a new type is more simple

Just need a single assignment to copy all the data to another

Bitmap class – reference type from System.Drawing library

Type design

Value type – Small, independent, like co-ordinates

Reference type – bigger data, share among object, like sound effect

Value types – enumerated types and structures

Reference types – class

Immutability in types

DateTime struct – immutable type

Once the instance is created, cannot change the elements

To edit, it will create a new struct containing updated vales

Modifying immutable value each time will create a new object each time

String – immutable type

Creating value type

Structures – struct

Methods

Data values

Properties

Constructor

Class vs structure

Data members must be all initialize in constructor

NO parameter-less constructor

Unless all elements are set to default values

Numeric set to zero, strings set to null

NO inherent or extend from parent structure

In stack, except created in closure

Enumerated types – enum

Specify a range of values that a given type can have e.g. state

Base on int type, unless specified otherwise

Start from 0, unless specified otherwise

Benefit:

Predefined range reduce error

Text substitute number to increase readability

Enum AlienState:

byte { //specific the type instead of default int type

Sleep = 1, //set number rather than start from 0

Attack =2

}

Creating reference types

Basis of reference type is C# class

Declare similar to structure but manipulate differently

&quot;new&quot; keyword create a new _instance of a class_

Explicitly set each element in the array to refer to an instance

Memory Allocation

Value type – store on stack

Stack: area of memory that allocate and removed as programs enter and leave blocks

Value type variable created during execution of a block

Store on a local stack frame

Discard when the block completes

Extremely efficient way of memory management

Reference type – store on heap

Heap: managed for an entire app

Heap is Required!

Reference may be passed between method calls as parameters

may still needed after the method exit

objects managed by reference can only be removed when

garbage collection determines there is no more reference to them

Generic types

Generic – reusability with &quot;template&quot; that contains &quot;placeholder&quot; type

Avoid boxing/unboxing, casting. Meaning avoid errors

Example:

public class Stack **\&lt;T\&gt;** {

int position;

**T** [] data = new T[100];

public void Push ( **T** obj) =\&gt; data[position++] = obj;

public **T** Pop() =\&gt; data[--position];

}

Constraints – programmer limiting what the type **T** can be

Keyword: where

where T: class //must be reference type

where T: struct //must be value type

where T: new() //must have public, parameterless, constructor. **If more than one constraints, specify it last**

where T: \&lt;base class\&gt; //must be base class type or derive from base

where T: \&lt;interface name\&gt; //must be or have implement the specified interface. Can be multiples

where T: unmanaged //must NOT be reference type or contain member which are reference types

Constructors

Control the process of object creation

structure (value type), class (reference type), both use constructor

same name as object

without return type

perform validation of their parameters

if fail, throw exception to prevent creating invalid object

can be given access to modifiers

if only private, can not instantiated

unless contains public factory method

can be overloaded – multiple versions of a constructor

different signatures

avoid repetition by making one constructor call another constructor **within** its own class

using &quot;this&quot; keyword

useful when having overload constructors with optional parameters

public someConstructor( **x, y** ): **this(x, y, 3){**

**}**

Or use optional parameters

Static constructors

Call only the very first time calling the constructor

static someConstructor()

{

// only can one time

}

Good for load up resources required, initialize the static members

Static variables

Keyword: static

Always present – can use it without creating any instances

Member of a type not created for each instance of a type

Useful for:

Set max number of objects, constructor can check it

Outside of class, must Refer to static via class name

e.g. **ClassIdentifier.** StaticVariableIdentifer = someValue;

DO NOT mean cannot change the value in variable

To make it cant change: const / readonly

static methods

Can be called without the need for an instance of the object containing the methods

Libraries of useful function are often provided as static member

Methods

Member of a class

Has **signature** and **body**

Signature

Defines the type and number of parameters

that the method will accept

body

block of code that is performed when the method is called

any method with type other than _void_

must end with return statement that

return a value of the type of the method

naming

&quot;verb-noun&quot; manner, action followed by the thing that the action act on

e.g. DisplayMenu, SaveCustomer

be descriptive

parameter or arguments?

Method signature: parameters

Calling the method: arguments

Classes

Template or plans that are required to create an instance of that class

Blueprint of an object

Declare a class (blueprint) does NOT create object

Create object using &quot;new&quot; keyword followed by class name

Program code of the class load into memory

for 1

# st
 time calling

static members are initialized

static constructor is called

call the constructor of the class

a class contains:

data variables – maintain state and manage storage of info

properties – manage access to data

methods – provide behaviors to be used by other classes

Extension methods

A class that provide methods for other classes to call

Adding behaviors WITHOUT needing to extend the class itself

e.g. methodIdentifier ( **this TARGET\_TYPE** variableIdentifer){ … }

public static int LineCount( **this String**  str) { … }

include the &quot;namespace&quot; containing the extension methods to use it on your class

compiler searches the included namespaces for a matching method for that type

then generate a call of that method

never part of the object

no access to private member of the method class

Can Never replace an existing method in a class

i.e. if String type already contained a method called &quot;LineCount&quot;,

extension method named &quot;LineCount&quot; woud not be called!

Add behaviors to existing classes and use them as if they were part of that class

e.g. LINQ query operation are added using extension methods



extension method is similar to &quot;decorator pattern&quot; aka wrapper pattern

but its less flexible as its can&#39;t be used in runtime to alter the behavior

ref: [https://stackoverflow.com/questions/4888116/the-decorator-pattern-extension-methods-in-c-sharp](https://stackoverflow.com/questions/4888116/the-decorator-pattern-extension-methods-in-c-sharp)



Optional and named parameters

Named parameters:

Use name instead or order when passing arguments

Remove confusion when another programmer read the calling arguments

x = ReadValue(1, 100, &quot;Enter your age: &quot;);

x = ReadValue(low:1, high:100, prompt: &quot;Enter your age: &quot;);

Optional parameters:

Give default value for the parameters at Method signatures

Without this, calling may look messier

MUST provide after all of the required ones

Indexed properties

Similar to accessing array using index

Example:

public int **this [int i]**

{

get { return array[i]; }

set { array[i] = value; }

}

Can use **other types** in indexed property!

e.g. public int **this [string name]**

This is how Dictionary collection works!

Index type is string!

Create overloaded and overridden methods

Overloading:

Providing a method with the same name, but

Different signature in a given type

Useful for

Providing several ways of performing a particular behavior

Depending on the circumstances in which the behavior is used

e.g. different constructors using different initial arguments

DataTime has large number of overloaded constructors

Overriding:

Overriding of methods takes place when class hierarchies are used

Child class is derived from a parent or base class

Methods in a base class is overridden

By a method in a child

When a method with exact same method signature as in the parent class

Only **virtual** method in parent class can be overridden

Reason why need to override:

Class at top of the hierarchy are more abstract

Class toward the bottom are more specific

e.g. Base class: Document ; child class: Invoice

example: Document contains all general behaviors common to all documents

e.g. method that get the create time

Print behavior of Invoice will have to be different from Document

Overriding allow us to create a print method in Invoice overrides that in Document

Using base:

Use the parent/base method

Keyword: base

public **override** void **DoPrint** ()

{

**base****.DoPrint**()

}

Allow behavior of different items to be customized for each particular item

Re-use code in parent object

# Consume types

Everything is an object of particular type and compiler ensure that interactions between types are always meaningful

How to use differently typed objects

Process performed in conversion between value and reference type

How to use casting to inform the compiler of our intensions

How statically typed world of C# can be made to interact with environments that have a dynamically typed; component object model (COM) interfaces

Different data type object is used for storing different type of data

e.g. string for text, int, float for numeric, custom built objects or data struct using primitive type to store related data

A special case is &quot;nullable&quot; data type, the type end with &quot;?&quot; e.g. int?

Useful for working with database which store null

e.g. the column supposes to store integer, but has null instead

Box or unbox to convert between value types

Boxing:

Treat value types as reference types

object o = 9;

Unboxing:

Convert a reference type into a value type

Int oVal = (int) o;

Things happened behind the scene

Take the value 9 and place it on the evaluation stack

Box instruction that creates an object of type Int32 on the heap

Set the value of this object to 99

Places a reference to this object on the evaluation stack stored in storage location 0 which is where variable o is stored

Unbox instruction that unbox a reference to an Int32 object

Places the value that result from unboxing to evaluation stack

Box and unbox instructions are always given a destination or source type to use

All built-in C# type has a matching C# type called &quot;interface type&quot;

e.g. int -\&gt; int32 is the interface type

good news for programmer is

boxing and unboxing process happen automatically

bad news

slows down the program

MOST IMPORTANT: **AVOID THE NEED OF BOXING AND UNBOXING**

Needing these processes is a symptom of poor design

Should be clear in design

which data items are value types and which are reference type and

work with them correctly

Cast types

C# disallow implicit conversion between types that result in the loss of data

Called narrowing

Value transfers into a type which offers a narrower range of values

e.g. integer doesn&#39;t handle the fractional part of a value

C# can do narrowing with explicit conversion

Using cast

Example:

float x = 9.9f;

int i = (int) x;

Widening conversion does not require a cast

Casting is also used when

converting references to objects that maybe

part of the class hierarchies or

expose interfaces

Convert types

Built in type: .NET runtime provides conversion methods to perform casting

System.Convert class

Set of static methods that can be used for type conversion between built-in types

e.g. int myAge = Convert.ToInt32(&quot;21&quot;);

throw exception if the string provided can&#39;t convert to integer

usually work with string

ref: https://docs.microsoft.com/en-us/dotnet/api/system.convert.toint32?view=netframework-4.8

own type: write own conversion operators for own data class

e.g. convert mile to kilometer, mile to integer form from double

implicit conversion

public static **implicit operator** Kilometer(Miles t)

{

return new Kilometers(t.Distance \* 1.6);

}

explicit conversion

public static explicit operator int (Miles t)

{

return (int)(t.Distance + 0.5);

}



Handle dynamic types

Strong type language

When compile, compiler ensures that all actions that performed are valid

in the context of the types that have been defined

e.g. if a class has no method with a particular name,

compiler will refuse to generate a call to that method

make sure the programs are valid at the time that are executed

Problem of strong type

Work with non-strong types or do not have their origins in C# code

e.g. Common Object Model (COM)

Document Object Model (DOM)

Objects generated by C# reflection

Interworking with dynamic languages such as Javascript

Force compiler to work with object which strong type info generated from compiled C# is not available

Keyword: dynamic

Identify items for which C# compiler should suspend static type checking

Compiler generates code that works with the items as described

Without doing static checking to make sure that they are valid

Program using dynamic objects might not work if the &quot;description&quot; is wrong

No checking in complier time, run time error during runtime

dynamic type variables

similar behavior to those Python or Javascript variables

type of variable will change according to what is stored

if program behave incorrect, throw exception

flexibility of dynamic type is for

easy interacting with other language

libraries written using the Component Object Model (COM)

NOT for C# programmers not declaring types

Use ExpandoObject:

Dynamically add properties to an object

e.g. dynamic person = new ExpandoObject ();

can created nested data structures

can be queried using LINQ

can expose the IDictionary interface for

content query and remove

useful when creating data structure from markup language

e,g, reading a JSON or XML



Ensure interoperability with unmanaged code that accesses COM APIs

Component Object Model (COM)

Mechanism that allow software components to interact

How to express an interface for other objects to connect

Many resources are exposed via COM

COM objects runs as unmanaged code

Direct access to the underlying system

.Net app usually run inside a managed environment

limiting level of access to the underlying system

possible to run in unmanaged

.Net app to interact with a COM object, steps:

Convert para for COM into an appropriate format

Switch to unmanaged execution for the COM behavior

Invoke the COM behavior

Switch back to managed execution upon completion of the COM behavior

Convert any results of the COM request into the correct types of .NET objects

Performed by Primary Interop Assembly

Supplied along with the COM object

Results returned by PIA can be managed as dynamic objects

Type of the values can be inferred rather than specified directly

Example of PIA:

Microsoft.Office.Interop.Excel

No casting needed as elements are exposed as dynamic types

Conversion is performed automatically based on the inferred type of assignment destination

var excelApp = new Microsoft.Office.Interop.Excel.Application();

Embedding Type Information from assemblies

To embed PIA in app:

Set True to Embed Interop Types option of the assembly reference

Remove need for any interop assemblies on the machine running the app

# Enforce encapsulation

Recap:

Object

hold data values by the use of member variables

provide services for other objects by the use of member methods

Use constructor to

setup content

Ensure the content are valid from the point of creation

Value vs reference

directly store the value – stack, lifecycle end in closure except lambda

indirectly store and refer to the location of underlying which contains the value – heap, managed by GC will no reference referring to the underlying object

dynamic types

allow C# (strong type) program it interact with external languages and library

Encapsulation – Enclose in a capsule

How to use encapsulation to improve the safety of programs

How to enclose your software objects in capsules

Active and passive system in &quot;safety&quot; control

Active – prevent accident or bad things from happening

Passive – if shit happened, react to it

Encapsulation – active system – reduce the possibility of program errors occur

Exception handling – passive system – take control in the events of errors occurring

Encapsulation – let you hide the elements that actually implement the behaviors of an object

Only expose the behaviors that it provided by a well-defined interface

Protect against accidental damage

Prevent direct changing of internal elements of an object made by Programmers without proper understanding of the effect of the change

Protect against malicious attacks on an app

Code is written with the intent to corrupt or damage the content of an object



C# provides features to implement encapsulation in created objects

Access modifiers

Mark the object member (data / method) of a type with

access modifiers that control access to them

Basic modifiers: public and private

Public – can be accessed by code that is outside that type

Private – only accessed by code running inside the type

Enforce encapsulation by using properties

Property – a way that a programmer can encapsulate data

Manage access to individual values in a class via using accessor methods

Look like field on the outside, but have logic inside

Can&#39;t tell from the outside

public access modifier

e.g. class Customer

{

public string Name;

}

doesn&#39;t provide any control

e.g. can&#39;t control anyone setting the Name to empty string that is not supposed to happen

need to &quot;enforce business rules&quot; on the app

or other rules to enforce

e.g. restricting the characters can be used in name

set limit for min and max length of name

need to manage access and stop invalid data

 &quot;set&quot; &amp; &quot;get&quot; behavior in property

set is used when a program set a value in property

set

            {

                if (value == &quot;&quot;)

                    throw new Exception(&quot;Invalid customer name&quot;);

                \_nameValue = value;

            }

get is used when a program get a value from property

get

            {

                return \_nameValue;

            }

set behavior

keyword: value – represent the value being assigned to the property

can implement validation logic

e.g. throw exception on empty string – make sure user is aware

ignore invalid name

default string when invalid is provided

powerful and natural to use

transparent for used to use i.e. might not even be aware that code is running when doing simple assignment

can provide &quot;read only&quot; properties by creating properties with only get behaviors

useful for exposing multi-view of data in an object

can provide &quot;write only&quot; properties by only providing set behaviors

different access modifiers for the get and set behaviors

e.g. public get for anyone to read but private set for only code running inside the class can assign values. To the property

backing value: private value holding the value being passed in by the property

e.g. \_nameValue in the above

Auto-implemented properties – **syntactic sugar**

Just implement a class member as property, don&#39;t need control

C# compiler automatically creates the backing values, without access to it

e.g. public int Age{ get; set;}



Enforce encapsulation by using accessors

General rule: within a type, data as private, method as public (access data via methods)

Help &quot;enforce business rules&quot;

Secure code example: class member private with public methods to access it

e.g. bank account balance variable in account class should be private, other cannot assign a new value at will

must go thought methods like PayInFund(), which can include logging for each transaction for audit

secure code and practice above help create secure workflow

Default access modifiers – without specifying, private is used

If wants public, must explicitly add it

Even default is private, its better to specify it

Protected access

Child class can access

Not recommended for highly important data as a malicious programmer can gain access by extending the parent class

Use for Limit access to helper method that have no meaningful use outside of the class hierarchy

Internal access

Assessible within the assembly

Output of a C# project: executable program .exe or library of class .dll

Useful when having a large number of cooperating classes that are being used to provide a particular **library component**

Share the member **within the library** while **not being visible to programs that use the library**

Readonly access

Can only be set at declaration or within the constructor of the class

Access modifiers and classes

Private, public, protect, internal access modifiers can also be applied to classes that are declared nested inside other classes

e.g. class abc

{

protect class def

{

//sth

}

{

Enforce encapsulation by using **explicit interface implementation**

Explicit interface implementation

Make methods implementing an interface **only visible** when the object is accessed via an interface reference

e.g. class Report: **IPrintable**

{

String **IPrintable**.GetTitle () {…}

// instead of just GetTitle () {…} without interface identifier

}

Preceding the method name, add the interface name!

i.e. methods being explicitly implemented

Encapsulate/hide member methods or properties that is highly specialized and distracting for normal use case.

To access those &quot;hidden&quot; methods, users has to **cast the object to that interface** in order to access them

Reduce the chances of the interface methods being used in an incorrect context

Resolving **duplicate method signatures** by using explicit implementation

i.e. class implements multiple interfaces; two interfaces might contain method with the same name

e.g. interface **IPrintable** { string GetTitle() };

interface **IDisplay** { string GetTitle() };

class Report : **IPrintable** , **IDisplay**

{

string **IPrintable**.GetTitle() { … }

string **IDisplay**.GetTitle() { … }

}

# Create and implement a class hierarchy

Create component objects that can be managed in terms of their abilities

How to reduce the amount of code that you will need to write for an app using class hierarchies to reuse code

Interfaces that are used by the .Net framework to manage objects and consider how these can be usefully implemented by created objects

Design and implement an interface

**Interface turns objects into components**

Interface is use by components that are interchangeable (from supplier perspective)

e.g. power supply interface defines how an electrical device can be connected to power

Interface specification – describe exactly how the two components interact

e.g. Which signal are inputs, which signals are outputs, etc.

power voltage, plug shape

Component-lize is possible because people create standard interface to describe exactly how different pieces should fit together

e.g. PCI-E slot on mainboard, and graphic card using the PCI-E interface

Without interfaces and components, things are &quot;hard wired&quot; together

Software components and interfaces

Interface specific how a software component can be used by other software components

Start design and build an app by designing interface, instead of class

Describe what each component will do

Functions can be encapsulated inside the component

C# interface – a set of method signatures

Class contains an implementation of all of the methods described in the interface is known as

Implementing that interface

Interfaces regard objects **in terms of &quot;abilities&quot;,** rather than what type of object is it

e.g. hire someone to paint your house is dealing with that person in term of his ability to paint, and cast as the person as &quot;painter&quot;, not who he really is

Benefits from decoupling

the additional &quot;abilities&quot; are access via interface without knowing the specific of the objects

create interface testing objects to test if the other object has implemented the interface correctly

Design an interface

Create an interface to describe the behavior of a class that will be implemented

i.e. express these behaviors in the form of an interface that sets out the method that a class should implement

at the interface level, no detail about how to implement the methods (tasks), but just identifying what to implement

Interface place in source files just like class

Interface name start with letter I is a convention

Reference to Interfaces

Deal with objects &quot;in terms of abilities&quot; instead of what the object is

e.g. a person who has the book writing ability

i.e. book writing interface, can be regard as a &quot;writer&quot;

from the point of view of a publisher

who has to deal with a large number of interchangeable writers

for easier management

instead of regarding each as individual.

i.e. manage and operate in terms of &quot;more standardized&quot; interface across objects is easier than knowing every single object and deal with it specially case by case

users can create reference variables of the interface to be used and assign the objects in order to only use or focus on the specific abilities on an object in C#

Rather than deal with the particular type that they are

Can be done in terms of &quot;casting&quot;

or assign the object reference to interface reference

Interface is like a qualification without validation

There is not interface instance/object

But there are object implementing the interface

e.g. no physical thing as &quot;writer&quot; but that are people who has the ability or role as &quot;writer&quot;

Inherit from a base class

One of the fundamental principles of software development

Every piece of code in an app only write once

Avoid copying from one place to another by using method calls

Reason is, when fix a bug, fix in one place instead of having to search through the entire program. Search and fix might miss some places

Class hierarchy

a way to **reuse code** , write one, other can reuse

used when an app has to manipulate items that are part of a particular group

inheritance – use this to save writing duplicating codes

e.g. BankAccount class has the abilities implemented using three methods, payIn(), withDraw(), checkBalance()

A new BabyAccount class requires same abilities,

except limiting the withDraw to less than a threshold amount

The new class can &quot;inherit&quot; the BankAccount class,

also implement the IAccount interface,

so other part of the system can still work with it by regarding it&#39;s a &quot;Account&quot; without new friction

public class BabyAccount : BankAccount, IAccount { … }

the above BankAccount class is called a base/parent class of BabyAccount

The is and as operators

is and as operators are for class hierarchies and interface to use

is operator determine if a given object can be made to refer to that class type or interface:

is a particular class hierarchy

or implements a specific interface

return true if reference variable x can e.g. if ( x is IAccount)

as operator takes a reference and a type

return a reference of the given type

or return null if the reference cannot be made to refer to the object

safer than direct casting

cast fail, throw exception

as operator return null instead

Overriding methods

Override **extends** the base method! Child class can have more specialized abilities without copy and paste code

Keyword: override

Use this version of the method in preference to the one in the base class

keyword: virtual

Mark the method that might be overridden with

Note:

Even the child class object is being casted as parent, will still use the override method, because the virtual keyword in parent will tell the compiler to look for override method!

C # compiler need to know if a method is going to be overridden because the way of calling it is slightly different from &quot;normal&quot;

C# language doesn&#39;t allow overriding of explicit implementations of interface method

i.e. method with interface name in front can&#39;t be overridden or declare as virtual

with the ability to override a method

can create more general class and customize it to make them more specific

plan and manage class relationship at the design stage

gather more info from customers, see which part need to be changed during the life of the project

some always the same – checkBalance(), PayIn()

some might need to change – withDraw()

using the base method

reduce writing duplicated code in new overridden method

reduce the need for exposing private data member in parent

fix bug once in parent class, then auto propagate to children

base – reference to the thing which has been overridden

new override method – add some customized need, then use the core function provides by the parent by using base method

replacing method in base classes

keyword: new

replace a method provided by base class

using &quot;new&quot; replacement method is not the same as override, it **hides the base method**

no more &quot;base&quot; method can be used

**avoid using it** , use instead of virtual + override combo

&quot;new&quot; keyword is to surpass compiler warning, because a method with the same method name (even different signature) in child class, usually happen by accident

**Unlike override** , if a child class object being casted to parent class, **the object will call the parent method instead**! This can be undesirable!

stopping overriding

some methods should never be override, class should never be extended

e.g. GetBalance() in BankAccount ; if being replaced by children with returning higher balance is undesired

keyword: sealed

use in parent class to tell children that they can&#39;t override this method

however, this is **not** working on &quot;new&quot; which replace/hide base method

more useful use for &quot;sealed&quot; keyword is used on class, mark the class cannot be extended

e.g. public **sealed** class BabyAccount : CustomerAccount,IAccount {…}

constructors and class hierarchies

child MUST declare a constructor which call the parent constructor

never automatic!

Create a child class object involve creating a parent/base class object

Base class constructor always executes first, this ensure base is initialized before specialized child initialized

Child constructor can call parent constructor using &quot;base&quot; keyword

e.g. public BabyAccount(int initialBalance) : base(initialBalance) {…}

similar to &quot;this&quot; keyword which is calling constructor within the type while &quot;base&quot; call the parent&#39;s constructor

child constructor can do specialized initialization after calling the parent one

Abstract methods and classes

Class with any abstract method must be abstract class as well

abstract class cannot be instantized

abstract method must be overridden in child class

and must NOT contain any implementation

use for telling the child class must implement the abstract method itself

abstract class is a like a template for creating more specialized class

Abstract classes and interfaces

Abstract class can contain fully implemented methods with abstract methods

Interface can only contain method signatures

Abstract class can reduce duplicating codes over using interface

In C#, a class can only inherit from one abstract class, but can implement multiple interface

Reference in class hierarchies

Upcasting, implicit: child object using parent class reference

e.g. BankAccount acc = babyAccountObjectRef;

Downcasting, explicit: may not work and not recommended!

Because the object might not have the specialized methods

using &quot;interfaces&quot; to manage &quot;references to objects&quot; instead of using &quot;class types&quot;

Create and implement classes based on the ICompareable, IEnumerable, IDisposable, and IUnknown interfaces

Learn about some .Net interface implementation

IComparable

Determine the ordering of objects when they are sorted

CompareTo() compares this object with another

Method to be implemented: int CompareTo(object obj)

Return less than 0

Indicate this object should be placed before the one being compared with

Return is 0

Indicate this object should be place at the same position as the one being compared with

Return greater than 0

Indicate this object should be placed after the one being compared with

 &quot;object&quot; passed into the CompareTo() must be converted to the relevant type/interface by assigning the object reference to a type/interface variable

e.g. IAccount account = obj as IAccount;

return this.balance.CompareTo(account.GetBalance());

note: &quot;this.balance&quot; is a built-in integer type object with CompareTo() method to compare with another integer type object

with the above, the object can be compared with other object with the same type or same interface!

Benefit: can use .Net built-in method Sort() to sort the object in List() collection type!

Typed IComparable

Create a CompareTo() that only accepts parameters of a specific type

More secured, because there is checking in compile time, rather than run time to reduce error

No casting needed

e.g. public class BankAccount : IAccount, IComparable **\&lt;BankAccount\&gt;** { … }

note: it is good to force any other class implementing the Interface to implement CompareTo() method by update the interface to inherit IComparable\&lt;\&gt; interface

e.g. public interface IAccount  **: IComparable\&lt;IAccount\&gt;**

IEnumerable

Implement the interface to make the object &quot;consumable&quot;

To make it &quot;consumable&quot;, a &quot;cursor&quot;(movable pointer) pointing to current object in the data set that contains multiple objects has to be provided

Therefore, Enumerable must give an Enumerator

An enumerator is a read-only, forward-only cursor over a sequence of values

IEnumerable interface:

public interface IEnumerable // aka &quot;consumerable&quot;

{

      IEnumerator GetEnumerator(); // return a &quot;Consumer&quot;

}

IEnumerator interface:

public interface IEnumerator // aka &quot;cursor&quot;

{

// move &quot;cursor&quot; to next object, return true if success, first call to MoveNext should move to the first (and not the second) item in the list

      bool MoveNext();

      object Current { get; } // return a reference to the object which &quot;cursor&quot; is pointing at

      void Reset(); // set the &quot;cursor&quot; to default value which is -1, or can throw &quot;not support&quot; exception

}

Two ways to &quot;consume&quot; the object

1. Directly manipulate the enumerator (cursor): - old fashion, rarely do this

string s = &quot;Hello&quot;;

IEnumerator cursor = s.GetEnumerator();     // Because string implements IEnumerable, we can call GetEnumerator():

while (cursor.MoveNext())

{

char c = (char) cursor.Current;

Console.Write (c + &quot;.&quot;);

}

// Output: H.e.l.l.o.

1. Using &quot;foreach&quot;: - the modern &quot;consumer&quot; - syntactic sugar of the above!

string s = &quot;Hello&quot;;      // The String class implements IEnumerable

foreach (char c in s)

{

      Console.Write (c + &quot;.&quot;);

}

Making an object enumerable (&quot;consumable&quot;)

i.e. : return an enumerator

Three ways to create an Enumerator:

1. Expose the collection enumerator

calling GetEnumerator() on the inner collection

1. Create an &quot;iterator&quot; (producer) using yield – to be discussed in next section
2. Implement enumerator interface inside – old fashion, avoid!

Declare an &quot;inner&quot; Enumerator class inside the data object class

Implement behaviors explicitly for consuming &quot;own data&quot;

public class DataObject : IEnumerable

{

 int[] data = { 1, 2, 3 };

 public IEnumerator GetEnumerator();

{

  return new Enumerator (this);

}

// Declare an &quot;inner&quot; Enumerator class inside the data object class

class Enumerator : IEnumerator

{

   DataObject collection;

   int currentIndex = -1;

        public Enumerator (MyIntList collection)

        {

          this.collection = collection;

        }

        public object Current

        {

get {

            if (currentIndex == -1)

              throw new InvalidOperationException (&quot;Enumeration not started!&quot;);

            if (currentIndex == collection.data.Length)

              throw new InvalidOperationException (&quot;Past end of list!&quot;);

return collection.data [currentIndex]; }

}

        public bool MoveNext()

        {

          if (currentIndex \&gt;= collection.data.Length - 1) return false;

          return ++currentIndex \&lt; collection.data.Length;

        }

        public void Reset() { currentIndex = -1; } }

}



Using yield – a very complicated syntactic sugar that generate a &quot;state machine&quot; shown above!

Implementation reference: https://csharpindepth.com/Articles/IteratorBlockImplementation

Keyword: yield

Compiler generate all the &quot;Current&quot; and &quot;MoveNext&quot; behaviors

Record the state of the iterator method

Resume at the statement following &quot;yield&quot; in next call

Return the value and control back the caller

Example:

class EnumeratorThing : IEnumerable\&lt;int\&gt;

{

    private int limit;

    public EnumeratorThing(int limit)

    {

        this.limit = limit;

    }

    public IEnumerator\&lt;int\&gt; GetEnumerator()

    {

        for (int i = 1; i \&lt; limit; i++)

        yield return i;

    }

    // below is auto gen by compiler as well

    IEnumerator IEnumerable.GetEnumerator()

    {

        return GetEnumerator();

    }

}

Or:

public class Test

{

      public static IEnumerable \&lt;int\&gt; GetSomeIntegers()

      {

     yield return 1;

     yield return 2;

     yield return 3;

     }

}

IDisposable

Interface with Dispose() method

public interface IDisposable

{

Void Dispose();

}

Object implement this interface means that it contains an explicit Dispose method that can be used to tidy up when an app finished using it

Reason: Implement this interface when the object use resources that&#39;s not controllable by the built-in garbage collection mechanism

e.g. file handle, db connections, lock objects

define the &quot;release resource&quot; instruction in the Dispose() method

**even implemented, NOT auto** called when object delete by GC!!!

Need to Call it yourself by try(){…} finally( Dispose(); )

Or wrap the object construct with &quot;using&quot; statement which is a syntactic sugar of the above, and the action in the body block!

Ref: [https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/using-statement](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/using-statement)

[http://possiblythemostboringblogever.blogspot.com/2013/03/c-syntactic-sugar-using-statement.html](http://possiblythemostboringblogever.blogspot.com/2013/03/c-syntactic-sugar-using-statement.html)

IUnknown – copy from the book: C# in a nutshell

All COM interfaces are identified with a GUID

The root interface in COM is IUnknown — all COM objects must implement it. This interface has three methods:

• AddRef // memery management – keep alive! – done by runtime automatically

• Release // memery management – go to hell! – done by runtime automatically

• QueryInterface // give me the object reference!

AddRef and Release are for lifetime management, since COM uses reference counting rather than automatic garbage collection (COM was designed to work with unmanaged code, where automatic garbage collection isn&#39;t feasible).

The QueryInterface method returns an object reference that supports that interface, if it can do so.

To enable dynamic programming (e.g., scripting and automation), a COM object may also implement IDispatch.

This enables dynamic languages such as VBScript to call COM objects in a late-bound manner—rather like dynamic in C# (although only for simple invocations).

Study &quot;interop&quot; service for more detail!

# Find, execute, and create types at runtime by using reflection

C# program compiles into an assembly that includes

metadata, compiled code, and resources.

Inspecting the metadata and compiled code at runtime is called reflection. – quote from the C# nutshell book

This section is about three areas:

Add and read descriptive info (metadata) of the objects using attributes

Generate executable code as output

Create program to analyze the content of software object using reflection

Create and apply attributes

Metadata – data about data

Using attributes to describe data

C# use Attribute to contain metadata

Attribute can be added to class and its member

Attribute class is the base class

Some built-in attribute type:

[Serializable]

Doesn&#39;t hold any data

Mark the class as open and can be read by a serializer

Serializer take the content of a class and sends it into a stream

C# require classes to &quot;opt-in&quot; to serialize due to security concern

if don&#39;t Some member to be saved or serialized, they maybe mark as [NonSerialized]

some serializers don&#39;t need classes to be marked as serializable in order to work with them

XMLSerializer, JSONSerializer

[Conditional (&quot;VERBOSE&quot;), Conditional(&quot;TERSE&quot;)]

Activate and deactivate the contents of methods

In System.Diagnostics namespace

define symbols like VERBOSE will activate the marked method

this attribute controls the runtime not compilation

i.e. doesn&#39;t prevent passing code to the compiler, control whether or not to execute the code when it runs

Testing for attribute

Use a static method provide by the standard Attribute class:

IsDefined(): Check if a particular attribute class is attached to

Param: type of class being test, type of attribute class looking for

e.g. Attribute.IsDefined(typeof(Person), typeof(SerializableAttribute))

Creating attribute classes

Inherit base &quot;Attribute&quot; class, customize just like not object class

Use just like built-in class

Example:

// create custom Attribute

class ProgrammerAttribute: Attribute

{

    private string programmerValue;

    public ProgrammerAttribute (string programmer)

    {

        programmerValue = programmer;

    }

    public string Programmer

    {

        get

        {

            return programmerValue;

        }

    }

}

The above is using read-only property

not much value to change it as the changes won&#39;t be saved

// using custom attribute

[ProgrammerAttribute(programmer:&quot;Fred&quot;)]

class Person

{

    public string Name { get; set; }

}

The above uses named argument to call the constructor

Controlling the use of Attributes

By default, the attribute created can be use in anywhere on any class member

Class, variable, method, property, etc

Use &quot;AttributeUsage&quot; to limit the place that can apply the custom-made attribute

Example:

[AttributeUsage(AttributeTargets.Class)]

class ProgrammerAttribute: Attribute

{…}

if the attribute being applied elsewhere, compiler will generate error

use the or operator (|) to set multiple targets for a given attribute

e.g. [AttributeUsage(AttributeTargets.Property | AttributeTargets.Field)]

Read attributes

Read attribute data in using static method provided by standard attribute class

GetCustomeAttribute()

Pass in: the type of the class, the type of the attribute class

Return reference to the attribute u can enumerate

If not defined, return null

Using reflection

Searching and reading attribute values is an example of a program performing reflection

Reflection: Program on own content

Reflection is sometime called introspection

Testing system can use reflection to search for components that need testing

Identify the test required by the attributes

Use reflection to identify &quot;plugin&quot; component

Using type information from an object

GetType() – all objects has this

Return reference to the object&#39;s type

Example:

System.Type type;

Person p = new Person();

type = p.GetType();

Console.WriteLine(&quot;Person type: {0}&quot;, type.ToString());

&quot;Type&quot; object returned by GetType() contains:

All the field of the target object

Metadata describing the object

Use things in System.Reflection namespace to work with &quot;Type&quot; object

Extract info of the target object:

System.Type type;

Person p = new Person();

type = p.GetType();

foreach(MemberInfo member in type.GetMembers() )

{

    Console.WriteLine(member.ToString());

}

Calling a method on an object by using reflection

Use info provides by &quot;Type&quot; to call target object

GetMethod(&quot;methodIdentiferFoundInGetType&quot;)

Return MethodInfo type

To Use MethodInfo type to call method

Invoke(targetObjet, parameter)

Example:

Person p = new Person();

type = p.GetType();

MethodInfo setMethod = type.GetMethod(&quot;set\_Name&quot;);

setMethod.Invoke(p, new object [] { &quot;Fred&quot; });

Doing all the above is much slower than direct calling, but as example to demo how to use reflection on unknown object

Finding components in assemblies

Managed Extensibility Framework (MEF)

Search classes in an assembly and find components that implement particular interfaces

Assembly.GetExecutingAssembly()

Get current running assembly

Assembly.Load(&quot;SampleAssembly, Version=1.0.2004.0, Culture=neutral, PublicKeyToken=8744b20f8da049e3&quot;);

Load assembly using full name

Assembly.LoadFrom (@&quot;c:\temp1\lib.dll&quot;) or Assembly.LoadFile (@&quot;c:\temp1\lib.dll&quot;)

Load a target assembly file using file path

When loading identical assemblies, LoadFile() will give a fresh copy unless the path is the same, while LoadFrom() will give return same copy even path is different

Prefer to use LoadFrom()

Ref: C# in a nutshell 7.0 p770

IsAssignableFrom()

Check if a given object type implement the interface

Assembly. **ReflectionOnly** Load(byte)

Loads an assembly into the reflection-only context, where it can be examined but not executed

https://docs.microsoft.com/en-us/dotnet/api/system.reflection.assembly.reflectiononlyload?view=netframework-4.8

Example:

Search for &quot;objects&quot; implemented IAccount interface in an assemble

Get the assembly

Iterate all the type inside

Exclude those interface type, because we want &quot;object&quot; type implementing the interface, not interface

Check if the object can be assigned with the target interface

Add the object to the list

Assembly thisAssembly = Assembly.GetExecutingAssembly();

List\&lt;Type\&gt; AccountTypes = new List\&lt;Type\&gt;();

foreach ( Type t in thisAssembly.GetTypes() )

{

    if (t.IsInterface)

        continue;

    if(typeof(IAccount).IsAssignableFrom(t))

    {

        AccountTypes.Add(t);

    }

}

Use LINQ to simply the search!

Tell the program what the fuck do u want by conditioning the factors and let it do the execution instead of doing the execution yourself step by step!

Example:

var AccountTypes = from type in thisAssembly.GetTypes()

                    where typeof(IAccount).IsAssignableFrom(type) &amp;&amp; !type.IsInterface

                    select type;



Generate code at runtime by using CodeDOM and Lambda expressions

An app can be represented by object such as:

Assembly

Type

Programmatically add own element to other objects, so an app can create code at runtime

Automate the production of code

Create object that will have to interact with services providing a particular data schema

Automatically generating code in a design tool

Two techniques for generating code at runtime: CodeDOM, Lambda expression

CodeDOM

Document object model (DOM) is a way of representing &quot;the structure&quot; of a &quot;particular type&quot; of &quot;document&quot;

DOM can be seen as &quot; &#39;document&#39; represents in &#39;object model&#39; format&quot;

DOM being language-independent, can also be seen as one of the bridge / pivot languages between different domain specific programming.

A document is human thought expressed in text format.

Documents writing usually prioritize readability and writing efficiency by using techniques, such as using line breaks, paragraphs, sections for readability and using short forms and abbreviations for writing efficiency.

In programming documents technique such as syntactic sugar, property, class-based OOP are being used, and these documents usually has specific structures.

Documents with specific structures, such as C# and HTML source code, can use domain specific parsers to convert into verbose format for &quot;machine readability&quot;. One of the formats is tree structures.

A tree structure composes of two distinct things, tree elements and tree branches.

Tree elements are also called &quot;nodes&quot; which is the basic unit containing the data.

Tree branches represent the &quot;above and below&quot; (hierarchy) relationship between nodes.

The building block of tree structure is a simple and powerful way of presenting complex idea in a structured way even though the result may look verbose. Yet, simple and verbose is good for machine which require unambiguous instructions to process.

Each part of a source code document can be broken into multiple object models.

Each object model has the data and actions inside, with object reference and interface for defining where and how to access. (note: An object is a value in memory referenced by an identifier, can be a variable, a data structure, a function, or a method.)

Object models are represented by nodes and relationship of nodes are presented by tree branch in a hierarchy structure to form a DOM.

By converting a document in DOM format, it enables efficient programmatic access to the document.

The DOM can be view as a &quot;standard intermediate format&quot; to send the &quot;idea&quot; to other programs to interpret, such as doing LINQ to SQL, or as a programmable interface for other programs to manipulate.

For programs in different domains, it is much easier to interpret a kind of intermediate format common to each other than specific executable code or other irregular languages

Ref: wiki, and

[https://www.w3.org/TR/DOM-Level-2-Core/introduction.html](https://www.w3.org/TR/DOM-Level-2-Core/introduction.html)

[https://blogs.msdn.microsoft.com/charlie/2008/01/31/expression-tree-basics/](https://blogs.msdn.microsoft.com/charlie/2008/01/31/expression-tree-basics/)

https://www.huanlintalk.com/2011/08/csharp-expression-trees.html



CodeDOM object can be parsed to create a source file or executable assembly

construction of CodeDOM object represent the logical structure of the code

independent of the syntax of high-level language uses to create the document

copy from book:

CodeCompileUnit compileUnit = new CodeCompileUnit();

// Create a namespace to hold the types we are going to create

CodeNamespace personnelNameSpace = new CodeNamespace(&quot;Personnel&quot;);

// Import the system namespace

personnelNameSpace.Imports.Add(new CodeNamespaceImport(&quot;System&quot;));

// Create a Person class

CodeTypeDeclaration personClass = new CodeTypeDeclaration(&quot;Person&quot;);

personClass.IsClass = true;

personClass.TypeAttributes = System.Reflection.TypeAttributes.Public;

// Add the Person class to personnelNamespace

personnelNameSpace.Types.Add(personClass);

// Create a field to hold the name of a person

CodeMemberField nameField = new CodeMemberField(&quot;String&quot;, &quot;name&quot;);

nameField.Attributes = MemberAttributes.Private;

// Add the name field to the Person class

personClass.Members.Add(nameField);

// Add the namespace to the document

compileUnit.Namespaces.Add(personnelNameSpace);

// Now we need to send our document somewhere

// Create a provider to parse the document

CodeDomProvider provider = CodeDomProvider.CreateProvider(&quot;CSharp&quot;);

// Give the provider somewhere to send the parsed output

StringWriter s = new StringWriter();

// Set some options for the parse - we can use the defaults

CodeGeneratorOptions options = new CodeGeneratorOptions();

// Generate the C# source from the CodeDOM

provider.GenerateCodeFromCompileUnit(compileUnit, s, options);

// Close the output stream

s.Close();

// Print the C# output

Console.WriteLine(s.ToString());

Output:

namespace Personnel {

    using System;



    public class Person {

        private String name;

    }

}

Lambda expression tree

&quot;You need Expression when the code needs to be analyzed, serialized, or optimized **before it is run**.

Expression is for **thinking** about code, Func/Action is for **running** it.&quot;

&quot;When you want to treat lambda expressions as expression trees and look inside them instead of executing them.&quot;

&quot;Expression trees are not executable code, they are a form of data structure&quot;

Ref: [https://stackoverflow.com/questions/793571/why-would-you-use-expressionfunct-rather-than-funct](https://stackoverflow.com/questions/793571/why-would-you-use-expressionfunct-rather-than-funct)

Lambda expression: a way of expressing a data processing action (a value goes in and a result comes out)

Single action: single lambda expression

Complex action: multiple statements, or expression trees

ComDOM object is similar to a tree, root object contains elements that branch out from it and the elements from root also contain other element, resemble a tree like structure

Expression trees are widely used in C# esp in LINQ

Result of LINQ query will be created as an expression tree



Modifying an expression tree

Expression tree is immutable

Modify must make a copy, like string

Use types from the System.Reflection namespace

Assembly

An assembly is the output produced when a .NET project is compiled

Two Assembly types: currently executing, or, loaded from a file

Assembly class provide methods and properties to manage and examine the assemblies

Version, dependencies, definition of type declared, module defined, types in module, content of each type

PropertyInfo

GetProperties() from type object, return a enumerable object

The class provide details of a property, including the MethodInfo info for get and set behaviors

MethodInfo

Hole data about a method in a type

Signature, return type, detail of method parameters, byte code of the method body

Invoke() can &quot;call&quot; the method got from GetMethodBody()

Type

Describe the content in a c$ type

Collection of methods

Collection of class variables

Collection of properties

Collection of attribute

Details of the Base type

_Instance._GetType() can be called on any instance to obtain a reference the &quot;type object&quot; that describe the instance

typeof(_instance_) obtain a reference the &quot;type object&quot; that describe the instance

# Manage the object life cycle

Managed environment:

memory management via garbage collection process which remove all objects no longer being referenced. C# using .Net framework is in this environment

Unmanaged environment:

e.g. C++, need to include code to create and dispose of any object, or may lead to memory leak and crash.

Unmanaged resources:

Objects that connect to a particular (unmanaged) resource, such as file or database connection

Must make sure when the object is destroyed, resources connected to it must be released in a managed way

Two features to &quot;release resource&quot; or dispose unmanaged objects:

Finalization process

IDisposable interface

Garbage collection in .NET

Creating garbage

Garbage example 1:

Person p = new Person();

p = new Person()

the first &quot;Person object&quot; has no way to access it any more as no more reference is pointing to it

Garbage example 2:

{

    Person p = new Person();

}

the reference variable p goes out of scope, and hence the object no longer accessible

both objects above are already &quot;garbage&quot; but they are still occupying memory

clean up / garbage collection process, only occurs when

amount of memory for new object fall below a threshold

rate of GC will change depending on the loading on the host computer

Value types and garbage collection

reference type: objects that are referred to by reference stored by variable

Heap: area of memory where an app stores reference type

Value type: object stores in variable directly

Stack: store value type

Auto grow and contract

New block is entered: auto allocate for local value type variables

Block is ended: auto remove the variables and contract the space

Except case of closure!

The garbage collector

Active when app run low in memory

Search for objects no longer require and remove them

.NET runtime keeps an index of all the objects created since app started

GC has two phases: mark, and compact (which implicitly delete) to create continuous free space

Mark:

clear all existing flags

then search through all variable in use and follow the references to those objects

set flags on those in-use object

Compact:

in-use Objects are moved towards the beginning of heap area

those object without flags are &quot;unreachable&quot; memory, are &quot;deleted&quot;

ref: [https://en.wikipedia.org/wiki/Mark-compact\_algorithm](https://en.wikipedia.org/wiki/Mark-compact_algorithm)

all threads are suspended while GC is running

meaning app is irresponsive

GC can be invoked manually

GC uses &quot;generation counter&quot; to determine long lived and short lived (ephemeral) objects

object starts at gen 0 and add one gen as it survives each round of GC

max gen is 2

&quot;level 0&quot; GC target only new objects

&quot;level 2&quot; GC target all objects

GC can run in &quot;workstation&quot; or &quot;server&quot; modes

Options to run GC as separate concurrent thread, but will increase memory and CPU loading

Reducing the need for garbage collection

Modern app run in system with large memory

GC of .NET is highly efficient and responsive

Programmer no need to worry about it unless encounter problems

Visual Studio shows how app use memory, and has profiling tools to identify objects affected

Force GC process by:

GC.Collect();

Might enforce GC when known large objects have just been release or upon exit from a large and complex UI dialog

However, not recommended to interfere manually

Weak Reference

The target can be GC and save memory

Use for cache

If the weak reference is null, and need to be used, create the object again

var weak = new WeakReference (new StringBuilder (&quot;weak&quot;)); Console.WriteLine (weak.Target); // weak

GC.Collect();

Console.WriteLine (weak.Target); // (nothing)

- Manage unmanaged resources

.NET framework take care of create and destroy of objects

But NOT &quot;resources&quot; the object use

File handlers store in object

Two ways to get control at the point an object is being destroyed

1. Finalization
2. Disposable

Seize that moment to tidy up any resources that the object use

Object finalization using a finalizer method

GC triggers &quot;object finalization&quot; method, aka finalizer method is invoked by GC before object is removed from memory

Finalizer method gets control and can release any resources that are being used by the object

Declare finalizer method which is type-less:

**~ObjectClassIdentifier()**

Example:

public class Person

{

    ~Person() {…}

}

Problems with finalization

If the finalizer is slow-running, it will seriously impair the GC process

The delay might cause OOM

No guarantee the finalizer method will ever run

May be never short of memory

Only run at the end of program

Prefer to Use Dispose mechanism

- implement IDisposable, including interaction with finalization

Objects implement IDisposable interface AND use the &quot;using&quot; statement

Dispose() will auto execute when the execution leaves the block follows the &quot;using&quot; statement

Dispose()

does NOT cause object to be deleted nor mark the object for delete by GC

Object can no longer be used in an app!

Can call by app when an object is required to release all the resource its using

Can control EXACTLY when to call unlike finalizer

Using IDisposable and finalization on the same object

Avoid resource being released more than once buy Dispose() and Finalizer ~

GC.SuppressFinalize(this);

Prevent GC from running the object&#39;s finalizer

Use &quot;dispose pattern&quot;

If Dispose() is called, all also call Dispose() of other managed resources managed under it, and suppress the GC from calling the finalizer

If Finalizer ~ is called instead, don&#39;t not try to call Dispose() on other managed resources as they might have been deleted by GC, and log the event

For both, unmanaged resources has to be released

Example:

class Test : IDisposable

{

  public void Dispose()

  {

    Dispose (true);

    GC.SuppressFinalize (this);

// NOT virtual

// Prevent finalizer from running.

}

      protected virtual void Dispose (bool disposing)

      {

        if (disposing)

        {

          // Call Dispose() on other objects owned by this instance.

          // You can reference other finalizable objects here.

          // ...

}

        // Release unmanaged resources owned by (just) this object.

// ... }

~Test() {

        Dispose (false);

      }

}

- manage IDisposable by using the Using statement

&quot;using&quot; statement ensure the calling of Dispose() when the object is no longer required i.e. Dispose() is call then the block following it has ended

Will still call Dispose() Even exception is thrown

- manage finalization and garbage collection

Invoking a garbage collection

GC.Collect() – invoke GC process, with optional para 0, 1, 2 to pick which gen of objects to clear

GC.WaitForPendingFinalizers() – wait till all finalizer has finished

DO NOT invoke GC manually, as it will hurt performance by unnecessarily promo object generation and hurt the dynamic self-ability which dynamically tweak the threshold for each gen to maximum performance

Exception: know the app or service will go to sleep for an extended period of time. When it sleep, it will be holding the unneeded object, and GC process can be activated

Managing finalization

Suppress finalizer:

GC.SupressFinalize(this);

Sometime if fail to dispose or &quot;finalize&quot; the object due to bug or the resource still in use, can try to re-enable finalization by

GC.ReRegisterforFinalize(this);

# Manipulate strings

The string type

C# type string (lower case s) is mapped onto the .NET type String (with an upper-case S)

String type holds text with theoretical upper limited of 2GB

gcAllowLargeObject to override

Immutable strings

Strings in C# are managed by reference and immutable (once set, cant change)

When &quot;edit&quot; a string, a new string is created

Use &quot;StringBuilder&quot; type

String interning

At compile time, two or more strings construct the same string will refer to the same underlying string

Example:

string s1 = &quot;hello&quot;;

string s2 = &quot;hello&quot;;

s1 and s2 will refer to the same &quot;hello&quot;

Benefit is to save space, or comparison time

At run time, even a new &quot;hello&quot; is constructed, will not be referred to the old &quot;hello&quot;

Not performance friendly to search all string to find a match to &quot;intern&quot; to

Can force string interning by using Intern()

Example:

s3 = string.Intern(s3);

NOT recommended, unless have strong reason

- Manipulate strings by using the StringBuilder, StringWriter, and StringReader classes;

String type has three major classes to work with StringBuilder, StringWriter, StringReader

StringBuilder

**In-memory** string builder

Build using &quot;mutable&quot; string, output a &quot;immutable&quot; one

Benefit: reduce intermediate string garbage building when build a new string

Example:

string firstName = &quot;Rob&quot;;

string secondName = &quot;Miles&quot;;

string fullName = firstName + &quot; &quot; + secondName;

intermediate string: firstName, &quot; &quot;, secondName

extra work for GC process!

StringBuilder is implemented by char array, provides

methods: Append, Insert, Remove, and Replace

property: capacity – max no. of chars

ToString() to output the result

Example:

StringBuilder fullNameBuilder = new StringBuilder();

fullNameBuilder.Append(firstName);

fullNameBuilder.Append(&quot; &quot;);

fullNameBuilder.Append(secondName);

Console.WriteLine(fullNameBuilder.ToString());

StringWriter

Based on TextWriter abstract class (implemented the interface)

Data source from StringBuilder

**Operate on in-memory string**

For programs sending text output into a string

StringReader

Based on TextReader abstract class

Data source from StringBuilder

**Operate on in-memory string**

For programs reading text input into string

- search strings;

String type provides a number of methods for

finding the position of substring inside a string

Use the &quot;search methods&quot; to

control the copying from one string to another

parse string and search for particular words

Contains()

Test a string contains given string

Param: sub string

Return true if contain

e.g.

string input = &quot;      Rob Miles&quot;;

if(input.Contains(&quot;Rob&quot;))

StartsWith() and EndsWith()

Test a string start or end with given string

Param: sub string

Return true if yes

Note: whitespace characters block it from working

Space

Tab

Linefeed

Carriage-return

Formfeed

Vertical-tab

Newline character

Use Trim to remove both start and end whitespace

TrimStart, TrimEnd, only trim start or end

Example:

string input = &quot;      Rob Miles&quot;;

string trimmedString = input.TrimStart();

if(trimmedString.StartsWith(&quot;Rob&quot;))

IndexOf() and SubString()

IndexOf: give position of 1

# st
 occurrence of given string

LastIndexOf: give position of the last occurrence of given string

Param: sub string, optional overload to specific start search pos

SubString: extract string from given position and length

Param: start position, how long

Example:

string input = &quot;      Rob Miles&quot;;

int nameStart = input.IndexOf(&quot;Rob&quot;);

string name = input.Substring(nameStart, 3);

Console.Write(name);

Replace()

Replace a specific string with another string, return a new string

Param: part of string to be replaced, new string

Example:

string informalString = &quot;Rob Miles&quot;;

string formalString = informalString.Replace(&quot;Rob&quot;, &quot;Robert&quot;);

Console.WriteLine(formalString);

Split()

Split a string into a number of substrings return in an string array

Param: one or more &quot;separator&quot; strings

Example:

string sentence = &quot;The cat sat on the mat.&quot;;

string[] words = sentence.Split(&#39; &#39;);

foreach (string word in words)

{

    Console.WriteLine(word);

}

String comparison and cultures

Strings in C# are in UTF-16 encoding format

Provide a range of characters for specific culture

use &quot;StringComparision&quot; param in compare operation to set the culture context

// Set the current culture for this thread to EN-US

Thread.CurrentThread.CurrentCulture = CultureInfo.CreateSpecificCulture(&quot;en-US&quot;);

// Using the current culture the strings are equal

&quot;encyclopædia&quot;.Equals(&quot;encyclopaedia&quot;,  **StringComparison.CurrentCulture** ))

// We can use the IgnoreCase option to perform comparisions that ignore case

&quot;encyclopædia&quot;.Equals(&quot;ENCYCLOPAEDIA&quot;,  **StringComparison.CurrentCultureIgnoreCase** )

!&quot;encyclopædia&quot;.Equals(&quot;ENCYCLOPAEDIA&quot;,  **StringComparison.OrdinalIgnoreCase** )

Example:

// Default comparison fails because the strings are different

if (!&quot;encyclopædia&quot;.Equals(&quot;encyclopaedia&quot;))

    Console.WriteLine(&quot;Unicode encyclopaedias are not equal&quot;);

// Set the current culture for this thread to EN-US

**Thread.CurrentThread.CurrentCulture = CultureInfo.CreateSpecificCulture(&quot;en-US&quot;);**

// Using the current culture the strings are equal

if (&quot;encyclopædia&quot;.Equals(&quot;encyclopaedia&quot;,  **StringComparison.CurrentCulture** ))

    Console.WriteLine(&quot;Culture comparison encyclopaedias are equal&quot;);

// We can use the IgnoreCase option to perform comparisions that ignore case

if (&quot;encyclopædia&quot;.Equals(&quot;ENCYCLOPAEDIA&quot;,  **StringComparison.CurrentCultureIgnoreCase** ))

    Console.WriteLine(&quot;Case culture comparison encyclopaedias are equal&quot;);

if (!&quot;encyclopædia&quot;.Equals(&quot;ENCYCLOPAEDIA&quot;,  **StringComparison.OrdinalIgnoreCase** ))

    Console.WriteLine(&quot;Ordinal comparison encyclopaedias are not equal&quot;);



- enumerate string methods

String: an array of characters

With Length property – no. of char in a string

Example:

foreach(char ch in &quot;Hello world&quot;)

{

    Console.WriteLine(ch);

}

- format strings

Format string – request the output of the type to be formatted in the specified way

 &quot;Formatting means converting to a string;

parsing means converting from a string&quot;

c# in a nutshell ch6 p.256

Example:

string s = true.ToString();     // s = &quot;True&quot; – convert type value to a string

bool b = bool.Parse (s);        // b = true – &quot;reverse operation&quot; the convert



culture settings

provide the &quot;context&quot;

The Parse and TryParse methods on DateTime(Offset) and the numeric types respect local culture settings;

change this by specifying a CultureInfo object.

Specifying invariant culture is often a good idea.

Method signature of DateTime.TryPasre()

public static bool TryParse (string s, _IFormatProvider provider_, _System.Globalization.DateTimeStyles styles_, out DateTime result);

IFormatProvider

e.g. CultureInfo.CurrentCulture

DateTimeStyles, flag attribute that can use bitwise operator | to combine vlaue

e.g. AdjustToUniversal use with AssumeLocal

ref: https://docs.microsoft.com/en-us/dotnet/api/system.globalization.datetimestyles

Example:

in Germany, the period ( **.** ) in numeric type indicates a thousands separator rather than a decimal point

parsing &quot;1.234&quot; into a double gives us 1234:

    Console.WriteLine (double.Parse (&quot;1 **.** 234&quot;));   // 1234  (In Germany)

Specifying invariant culture fixes this:

double x = double.Parse (&quot;1.234&quot;, **CultureInfo.InvariantCulture** );

The same applies when calling ToString():

    string x = 1.234.ToString ( **CultureInfo.InvariantCulture** );

String with &quot;placeholder&quot; – **{** _format__item(s)_ **} aka Composite format strings**

Example:

int i = 99;

double pi = 3.141592654;

Console.WriteLine(&quot;  **{0,-10:D} {0, -10:X} {1,5:N2}**&quot;, i, pi);

Format items Detail:

{

pick which item in param

(optional item) , (optional -ve for left justified) width in no. of char occupy

(optional item) : formatting info (format string)

}

Formatting info (format string) example, to be discussed below:

D: decimal string

X: hexadecimal

N2: floating point, follow by no. of decimal places

&quot;formatting command&quot; – ToString()

ToString() –return string with specific format condition

optionally defined by

Format string – provides instructions

Format Provider – determines how the instructions are translated

Example:

NumberFormatInfo f = new NumberFormatInfo();

f.CurrencySymbol = &quot;$$&quot;;

Console.WriteLine (3.ToString (&quot;C&quot;, f));          // $$ 3.00

&quot;C&quot; is a format string that indicates currency,

NumberFormatInfo object is a format provider that determines

how currency representations are rendered.

This mechanism allows for globalization.

Many built-in .NET types (numeric, DateTime) has implemented **&quot;formatting command&quot; ( ToString() )**

int, double, DateTime, … - use ToString() will give meaningful output

Custom type can implement &quot; **IFormattable**&quot; interface

The interface:

public interface IFormattable

    {

      string ToString (string **format** , IFormatProvider **formatProvider** );

    }

Example of self-define custom &quot;format string&quot;

class MusicTrack : IFormattable

{

    string Artist { get; set; }

    string Title { get; set; }

    // ToString that implements the formatting behavior

    public string ToString(string  **format** , IFormatProvider formatProvider)

    {

        // Select the default behavior if no format specified

**        if (string.IsNullOrWhiteSpace(format))**

**            format = &quot;G&quot;;**

**        switch (format)**

**        {**

**            case &quot;A&quot;: return Artist;**

**            case &quot;T&quot;: return Title;**

**            case &quot;G&quot;: // default format**

**            case &quot;F&quot;: return Artist + &quot; &quot; + Title;**

**            default:**

**                throw new FormatException(&quot;Format specifier was invalid.&quot;);**

**        }**

    }

    // ToString that overrides the behavior in the base class

    public override string ToString()

    {

        return Artist + &quot; &quot; + Title;

    }

    public MusicTrack(string artist, string title)

    {

        Artist = artist;

        Title = title;

    }

}

MusicTrack song = new MusicTrack(artist: &quot;Rob Miles&quot;, title: &quot;My Way&quot;);

**Console.WriteLine(&quot;Track: {0:F}&quot;, song);**

**Console.WriteLine(&quot;Artist: {0:A}&quot;, song);**

**Console.WriteLine(&quot;Title: {0:T}&quot;, song);**



Format string

two kinds of format strings:

Standard format strings

provide general guidance.

a single letter

followed, optionally, by a digit (whose meaning depends on the letter).

Example:

G: general

D: decimal string

X: hexadecimal

N2: floating point, follow by no. of decimal places

F: fix point

F2: 2345.6 -\&gt; 2345.60

N: fix point with group separator

N2: 2345.6 -\&gt; 2,345.60

D: padding

D5: 123 -\&gt; 00123

D1: 123 -\&gt; 123

P: percent

Custom format strings (unrelated to custom format provider)

micromanage every character with a template.

example &quot;0:#.000E+00&quot;

#: Digit placeholder

0: zero placeholder

Etc……

Format provider – mainly work with numeric and time

NumberFormatInfo

DateTimeFormatInfo

CultureInfo – acts as an indirection mechanism for the other two format providers, returning a NumberFormatInfo or DateTi meFormatInfo object

Example:

double bankBalance = 123.45;

CultureInfo usProvider = new CultureInfo(&quot;en-US&quot;);

Console.WriteLine(&quot;US balance: {0}&quot;, bankBalance.ToString(&quot;C&quot;, usProvider));

CultureInfo ukProvider = new CultureInfo(&quot;en-GB&quot;);

Console.WriteLine(&quot;UK balance: {0}&quot;, bankBalance.ToString(&quot;C&quot;, ukProvider));

US balance: $123.45

UK balance: £123.45

- use string interpolation

Put values to be converted directly into the string text

$ , leading dollar sign, is used to identify an interpolated string (FormattableString type)

Syntactic sugar! Compiler will convert it to the format string shown above

{\&lt;interpolationExpression\&gt;[,\&lt;alignment\&gt;][:\&lt;formatString\&gt;]}

Example:

Console.WriteLine($&quot;Your name is {name} and your age is {age,-5:D}&quot;);

String type: has static method Format()

Call the ToString() of the passed in type

String.Format($&quot;Your name is {name} and your age is {age,-5:D}&quot;));

Using a FormattableString with string interpolation

FormattableString type: has ToString() method

Example:

double bankBalance = 123.45;

**FormattableString**  balanceMessage = $&quot;US balance : {bankBalance:C}&quot;;

CultureInfo usProvider = new CultureInfo(&quot;en-US&quot;);

Console.WriteLine(balanceMessage. **ToString** (usProvider));

## Debug Applications and Implement Security

Consider security during design and dev, not afterward

Good tools to do performance analysis and optimize afterward

No much option to add security

Design and dev with security in mind!

Implement security at all level

Input level: within expected range

Store and manage level: validate again

Data validation

Tools to convert ram input into values

Valid the incoming structured data

Encryption to secure communication better diff apps or diff elements

Protect and manage installed assemble

Assembly versioning

Management features of .NET when deploying and maintaining a solution

Debug technique, diagnostic, tool and language features for tracking app behavior

# Validate application input

Create Secure app

1

# st
 step: ensure incoming data is valid

Data must from somewhere: app UI or packet of formatted value from another app

Make sure own app is robust to combat invalid input as

Invalid input can have malicious intend

Start look at validating JSON formatted data object

Then consider the management of data collection in app

Also, Consider data integrity

e.g. Operation on data wont mess it up (invalid the data)

C# techniques that do data integrity

E.g use of regular expression to valid format

Other Functions can be used to validate incoming data

Using JSON

JavaScript Object Notation

Popular mean for apps to exchange data

The JSON document

Contains a number of name(key)/value pairs

Value can contains arrays of JSON objects

Language independent – but can map to make OOP object easily

Good medium to exchange data between diff apps

JSON doc is enclosed in braces and contains a series of name and value pairs

Example:

{

&quot;Artist&quot;:&quot;Rob Miles&quot;,

&quot;Title&quot;:&quot;My Way&quot;,

&quot;Length&quot;:150

}

Data type is not stored in part of the document

Only contains the names and values of data member of the instance

Value is stored as text string or array

numeric values also stored as string

Values expresses as array: name and value pairs separated by commas enclosed in square brackets

Simple syntax, lightweight design, able to represent structured data

JSON and C#

Use a 3

# rd
 party package install via NuGet

using Newtonsoft.json;

Serialize, convert c# to json object:

JsonConvert.SerializeObject(c# object);

Deserialize, convert json to c# object:

JsonConvert.DeserializeObject\&lt;c# type\&gt;(json object)

[JSONProperty] attribute

Need to add it to private properties in a class

Provide the type when deserializing

Use checksum or hash property to detect modification of JSON doc

JSON and XML

XML – extensible markup language

Another way to express content and portable and human readable form

More heavyweight

Contains more metadata

Built-in XML serializer

using System.Xml.Serialization;

XML doc are versioned and with encoding detail

Need a XmlSerializer for either serialize or deserialize

Serialize:

Create TextWriter,

put it into the serializer and put the object as well

ToString()

Deserialize:

Create TextReader to read the xml string

Put the TextReader into the serializer

XML serialization can only save and load public data, private data need to use Data Contract serializer

XML serialization needs the type to have a parameterless constructor

XML deserialization returns reference to object

XML schema, optional,

define what must be in the XML doc

can be used to do auto validation

XML elements can contain attribute for metadata, like indicate the type

XML doc is also vulnerable as JSON, however, attribute mechanism can be used to add validation info

- Validate JSON data;

Deserialize in try-catch pair to see if the json is valid in syntax / format

Exception: JsonReaderException Message will give out the error position in JSON

- choose the appropriate data collection type

Many options for organizing data in collection depending on situations

Learn to Decide which on to use during app dev

Use typed collection classes

Prefer typed collection over untyped collection

Untyped collections e.g. ArrayList are less secured, more error probing

use &quot;object&quot; reference to manage item inside

&quot;object&quot; is the base type of all objects

Meaning untyped collection can contain and manage any item types

If added diff item type into collection by mistakes

Compile time cannot catch the type error

Runtime might generate type exception error

Use collections support generic typing is better

e.g. List\&lt;T\&gt; class in System.Collections.Generic namespace

able to detect diff item type as declared in compile time

Consider threading

Standard collections are not thread-safe

Using collections support concurrency, example listed below

BlockingCollection\&lt;T\&gt;

ConcurrentQueue\&lt;T\&gt;

ConcurrentStack\&lt;T\&gt;

ConcurrentBag\&lt;T\&gt;

ConcurrentDictionary\&lt;TKey, TValue\&gt;



Use the correct kind of collection

Queue/Stack: process stream of incoming messages and discard, FIFO/FILO

Simple array: lookup table, which knows how many elements in advance, and use index to access

List\&lt;T\&gt;: don&#39;t know how many items in advance, with indexed access

LinkedList\&lt;T\&gt;: frequently insert and delete, with large amount of items

Sort() in List\&lt;T\&gt;: implement IComparable interface for the CompareTo() for sorting data in particular order

Dictionary\&lt;TKey,TValue\&gt;: index items using particular key, e.g. bank acc no. for bank acc

StringCollection, StringList: storing string

Plan to work with LINQ on your collections

LINQ works on object implements IEnumerable or IQueryable\&lt;T\&gt; interface

LINQ queries are compiled into expression tree and execute

LINQ is powerful as it has many features for processing result and queries are relatively easy to create

IQueryable\&lt;T\&gt; is more efficient than IEnumerable

Performs data filtering on the source DB instead of loading all data and filter locally to produce result

Prevent premature optimization! Use LINQ!

Start by Using LINQ for any search or sort operation, before trying to create custom data storage elements for better performance

Use the Entity Framework to design your **data storage**

Entity Framework is part of ADO.NET (Active Data Objects)

Can use to create data storage for app

Objects created as C# class in app can be mapped to auto-created DB using Entity Framework tools i.e. C# class migrates into a database design using EF

Object and DB design can be upgraded by performing successive migration as the app evolves

VS can generate a complete CRUD MVC based data store in DB project for simple C# class as data model

VS has a lot of good template for scaffolding,

UI can be generated by the scaffolding process, technical part are well design, with basic visual elements

Model View Controller and ASP web pages

MVC pattern breaks a complex rich UI app into the components

by separating the activities

Model:

Contains data and business logic for the app

Contains State of the app

items in model are exposed as an instance of a context, &quot;model context&quot;, which provided methods that can interact with data used by controllers

Controller:

Responds to events that are generated by the view and selects which view to render

Initiate any changes to the Model in response to user action

E.g. instruct the &quot;model context&quot; to create a new item

View:

Display what the user sees on the screen

Implement by the web page for each view

A controller can be associate with a number of views

HTML describe the web page can use &quot;Razor&quot; view engine which use C# to build the template for data presentation

- manage data integrity;

Consider how &quot;changes&quot; are made to the data, the changes might fail or subvert, and cause data corruption

To improve the integrity of object, access modifier of classes e.g. private, can be used

Data manage by object should be private

Provide as little as possible for public interaction with the data

More fine-grained controls can be implemented to protect data integrity

Integrity in method calls

Parameters to method calls should be range checked

to ensure the data won&#39;t be corrupted

e.g. withdrawal from acc should always be positive, negative amount will default the methods purpose by increasing the balance

using pass by reference to pass parameter to method call might have the source data being changed unexpectedly

i.e. pass an object to a seeming &quot;read only&quot; method call might assign new value to it, hence, data corruption occurred

aka, &quot;side effect&quot;: a method changes the data outside of the method

to make sure the method can&#39;t change the source data being pass by reference

make a copy of the object

pass the reference of the copied object to the method

to make a copy, create a &quot;copy constructor&quot; for the object&#39;s class

example code of normal constructor and copy constructor:

class  **MusicTrack**

    {

        public string Artist { get; set; }

        public string Title { get; set; }

        public int Length { get; set; }

        // Copy constructor for MusicTrack

**        ** public** MusicTrack(MusicTrack source)**

        {

            Artist = source.Artist;

            Title = source.Title;

            Length = source.Length;

        }

        public MusicTrack(string artist, string title, int length)

        {

            Artist = artist;

            Title = title;

            Length = length;

        }

    }

Example code of using the normal constructor and copy constructor

// Create a new music track instance

            MusicTrack _track_ = new MusicTrack(artist: &quot;Rob Miles&quot;, title: &quot;My Way&quot;,

                                              length: 120);

            // Use the copy constructor to send a copy of the track to be printed

            // Changes made by the PrintTrack method will have  **no effect on the original**

            PrintTrack(**new MusicTrack(**_track_**)**);

Good to use in multithread app as well

The copying is NOT needed for pass by value type

By passing object by reference, it may accidentally grant some extra ability, e.g. assign new value to the data source

&quot;shallow copy&quot; or &quot;deep copy&quot;

Shallow copy

just copy one/own level, i.e. copy the data to a new instance but some data might be &quot;reference&quot; to other objects as well, hence, the other passed by reference objects inside the copy is still subject to possible side effect

Deep copy

Copy those data inside the object that is passed by reference as a new instance as well

meaning extra effort is needed

Risk and impact

At the start of dev process, do risk and impact analysis on data

Identify which piece of data has higher risk of being changed maliciously

The impact to business if happens

Example using the MusicTrack:

Risk of someone change the name is low, and effect of change isn&#39;t dangerous

But the &quot;price&quot; property is different!

More incentive to change the price maliciously, means high risk and it has high impact to the business as it can incur direct financial loss

Similar to the data of bank account balance

Put Extra effort on protecting high risk high impact data

Pay some price of performance hit, e.g. deep copy

Pay some price of usability and code readability e.g. private data, must use method call which has logging to change the data, detail property setting in set and get method

Atomic actions

Action on data should be &quot;atomic&quot;, meaning &quot;indivisible&quot;

Set value to must-have data-fields can either be success or fail

Instead of able to set some fields while failing other

- evaluate a regular expression to validate the input format;

Regular expression

A mean of expressing a sequence of characters that **define a search pattern** that can be used to match for a target string

Use regular expression to valid user input such as date, times, email address, telephone is easier than using methods in string library

Start by learning how to use regular expression for search and replace elements in a text string

String editing using regular expressions

Regex object: in System.Text.RegularExpression namespace

Replace() in Regex

Param: input string, regular expression to match (the **search pattern** ), replacement string

Example:

string input = &quot;Rob Mary David Jenny Chris Imogen Rodney&quot;;

string regularExpressionToMatch = &quot; &quot;;

string patternToReplace = &quot;,&quot;;

string replaced = Regex.Replace(input, regularExpressionToMatch, patternToReplace);

replaced string: Rob,Mary,David,Jenny,Chris,Imogen,Rodney

Regexp can match more complex input string, e.g. a number of space instead of one

Use &quot;+&quot; character as a &quot;quantifier&quot;

Example:

string input = &quot;Rob    Mary David   Jenny  Chris   Imogen       Rodney&quot;;

string regularExpressionToMatch = &quot;  **+**&quot;;

string patternToReplace = &quot;,&quot;;

string replaced = Regex.Replace(input, regularExpressionToMatch, patternToReplace);

many things can be added, such as use grouping { } to do repetition, specific range of char to match using bracket expression

ref: https://en.wikipedia.org/wiki/Regular\_expression

Advanced use of Regex

Ref: [https://docs.microsoft.com/en-us/dotnet/api/system.text.regularexpressions.regex?view=netcore-2.2](https://docs.microsoft.com/en-us/dotnet/api/system.text.regularexpressions.regex?view=netcore-2.2%20)

Build own Regex object, five type of constructors, most common of five is shown below

Regex(String, RegexOptions)

Initializes a new instance of the Regex class for the specified regular expression, with options that modify the pattern.

Example:

// Define a case-sensitive regular expression for repeated words.

Regex rxSensitive = new Regex(@&quot;\b(?\&lt;word\&gt;\w+)\s+(\k\&lt;word\&gt;)\b&quot;,

          **RegexOptions.Compiled** );

RegexOptions.Compiled

Specifies that the regular expression is compiled to an assembly. This yields faster execution but increases startup time

Result:

Regexobj.Match(String) and Regexobj.Matches(String)

Return Match and MatchCollection respectively

Match return the 1

# st
 match, MatchCollection return all matches

MatchCollection to List\&lt;string\&gt;:

GetEnumerator()

Ref: https://docs.microsoft.com/en-us/dotnet/api/system.text.regularexpressions.matchcollection.getenumerator

Match class Inheritance from:

    Object

**    Capture**

**    Group**

A match has a collection of groups

The match.Value and match.group[0] will return the string of the match

Group[0] is the whole match, after 1 is the sub group

A Group has a collection of captures

Captures are the matches inside capture group

Data validation using regular expressions

Basic condition setting:

The character . (period) in a regular expression means &quot;match any character.&quot;

The character + means &quot;one or more of the previous item.&quot;

The character sequence &quot;.+&quot; means &quot;match one or more characters.&quot;

&quot;.+:.+:.+&quot; is made up of three &quot;match any one or more character&quot; items separated by colon characters

IsMatch() takes two strings params

Param:

first: string being tested

second: regular expression being used to test the first

returns True if the regular expression matches a substring in the string being tested

example:

string input = &quot;Rob Miles:My Way:120&quot;;

string regexToMatch = &quot;.+:.+:.+&quot;;

if (Regex.IsMatch(input, regexToMatch))

    Console.WriteLine(&quot;Valid music track description&quot;);

Set addition conditions for more refined pattern matching

Numeric digit sequence:

[0-9] will match any digit

[0-9]+ will match one or more digit

$ means match &quot;at the end of the string&quot;, &quot;anchor&quot; at the end

^ means match &quot;the start of a the string&quot;

| means &quot;or&quot;. E.g. use in match upper or lower-case letters

Example:

string input = &quot;Rob Miles:My Way:120&quot;;

string regexToMatch = @&quot;^([a-z]|[A-Z]| )+:([a-z]|[A-Z]| )+:[0-9]+$&quot;;

explain:

start at upper or lower case letter or space for at least once, then semicolon,

then same as 1

# st
 condition (should use grouping),

then numerical char at least once and end there

Verbatim string: **@** in front of string construct

e.g. @&quot; some string &quot;

tell compile don&#39;t process any escape sequences

regexp frequently contains characters that compile regards as escape sequence

use verbatim string when constructing regular expression

regexp can be hard to read as it contains a mix of commands and text

powerful and can use count value to match exact number of items

ref: https://docs.microsoft.com/en-us/dotnet/standard/base-types/regular-expressions

- use built-in functions to validate data type and content

Defensive programming mindset to be use on user input

Assume the user input can be a malicious attempt

Safe guard the input to reduce program crash, exception, failure

Regexp is a good way to do pattern matching on the user input for validation usage

Some other build-in functions by C# to help with data validation

Reading values

Basic conversion: a string to a number

ALL numeric type has static methods: Parse(), TryParse()

Parse() method take in string and return number

But it throws exception when an invalid string is provided

TryParse() method takes in two params

First: string to be converted

Second: &quot;out&quot; type variable to receive the result

Function return true or false; return false on parse fails

Example:

int result;

if (int.TryParse(&quot;99&quot;, out result))

    Console.WriteLine(&quot;This is a valid number&quot;);

else

    Console.WriteLine(&quot;This is not a valid number&quot;);

C# Convert class – convert between various types

String into number, string into Boolean

Example:

string stringValue = &quot;99&quot;;

int intValue = Convert.ToInt32(stringValue);

Console.WriteLine(&quot;intValue: {0}&quot;, intValue);

stringValue = &quot;True&quot;;&quot;

bool boolValue = Convert.ToBoolean(stringValue);

Console.WriteLine(&quot;boolValue: {0}&quot;, boolValue);

Different in error behaviors response by different implementations of &quot;string to number&quot;

invalid string: can&#39;t be convert to numeric

int.Parse() – throw exception on null and invalid string

int.TryParse() – return false on null and invalid string

Convert.ToInt32() – throw exception on invalid string, but return &quot;zero&quot; on null

Validation in ASP.NET

Adding attribute to the model class

e.g. restrict the value range

public class MusicTrack

{

    public int ID { get; set; }

    public string Artist { get; set; }

    public string Title { get; set; }

    **[Range(20,600)]**

    public int Length { get; set; }

}

# Perform symmetric and asymmetric encryption

Identify situations where data security is required

e.g. transfer and storage of data

how much effort needs to put into ensuring its done in secured way

consider the impact of data security breaches as part of the risk management

just like the risk and impact analysis in validating data

two important things to remember on how to implement security in app

1. Never write own security code

Use the &quot;battle hardened&quot; that have &quot;industrial strength&quot; implementation provided by the framework

1. Never try implement security through &quot;obscurity&quot; (not clear, difficult to understand)

Use confusing file name or hide security information in somewhere are bad idea

Just the illusion of security

Cryptography and cryptoanalysis

Cryptography:

practice and study of technical for secure communication

in the presence of third parties trying to eavesdrop, secretly or stealthily listening to the communication without consent

Cryptoanalysis

Look for the weakness or insecurity in a cryptographic scheme. i.e. Breaking the cryptographic practice

Cryptography in detail:

To transfer message in &quot;open network&quot; (e.g. internet, present of third party) securely,

Sender &quot;encrypt&quot; the message

Receiver &quot;decrypt&quot; the message

Anyone intercept the encrypted message is unable to understand it easily

Early encryption techniques involved letter substitution or re-arrange the order of letter according to a sequence.

The substitution or re-arrangement are agreed upon the sender and recipient beforehand, in &quot;closed conversion&quot;

Cryptoanalysis in detail:

Basic method is to examine an encrypted message and look for statistical pattern that reflect the language and convention of the sender

Detect the patterns and used it as the basis of attempt to &quot;break&quot; the code/encryption by performing repeated guesses until the original message is retrieved

Computers are good are cryptoanalysis.

But techniques can be used to prolong the &quot;breaking&quot; using computer as well

- Choose an appropriate encryption algorithm;

Symmetric and asymmetric encryption

Symmetric encryption

Both sender and recipient share a &quot;common key&quot;

Simple analog implementation:

Share a &quot;common book&quot; as encryption index

Encrypt message by page number and letter number

Look up the letters to decrypt the message

Simple and effective

Statistic cryptoanalysis cant break this encryption because same message can be encrypted in many different way

Only way to break is to search thoroughly for the common copies of books between the sender and recipient

In practice by real spy

Problem:

How to securely tell a new message recipient what is the &quot;common key&quot;, esp. via the same &quot;open network&quot;

Asymmetric encryption

Two keys: message encrypted by one can be decrypted by another!

Keys are each end of the conversation are different

Aka public-private key. Public key to the world, private key only one keep

Problem: 100x slower speed than using symmetric key

Send symmetric key using asymmetric key

Confidentiality: Message encrypted with public key can only be decrypted by the corresponding private key

Digital signature for verification: message encrypted with private key can only be decrypted by the corresponding public key

Use case: encrypted by sender&#39;s private key, then encrypt with receiver&#39;s public key. Only the receiver can read the message and can verify its from the sender

Conversing using asymmetric keys

Alice and bob want to start a secured conversation in &quot;open network&quot; and Eve is &quot;listening&quot; (eavesdropping)

1. Alice and Bob each generate an asymmetric pair of keys: public private keys.
2. Alice and Bob exchange their public keys, Eve gets the keys too
3. **Alice generate a symmetric key and encrypt this key using Bob public key**
4. The encrypted symmetric key is sent to Bob. While Eva also get the encrypted key, without Bob&#39;s private key, Eve is unable to get the symmetric key
5. Bob and Alice can now exchange encrypted files using the shared symmetric key. The process of generate and share the symmetric key can be done by Bob to Alice as well
6. Eve can get the encrypted files, but unable to decrypted it easily. The conversation between Alice and Bob is now secured

Using asymmetric keys to &quot;sign&quot; message

To proof the document is valid and not tampered by 3

# rd
 party
1. Bob creates a document.
2. Instead of using his private key to &quot;sign&quot; the whole document which is big will make the &quot;sign&quot; process slow; he &quot;hash&quot; the document to generate a &quot;document signature&quot; aka &quot;message digest&quot; in the form of &quot;checksum&quot; which can be used to authenticate the document is valid. (other kind of &quot;message digest&quot; is better than checksum)
3. Bob &quot;sign&quot; the &quot;message digest&quot; with his private key
4. Bob sign the document to Alice, along with his public key and the signed &quot;message digest&quot; of the document
5. Alice can use the public key to decrypt the &quot;message digest&quot; and &quot;hash&quot; the document she received to see if the &quot;message digest&quot; is same as the one she has decrypted
6. If the &quot;message digests&quot; are the same, the document is sent by Bob to Alice without being tampered by Eve

&quot;Signing&quot; can be used in software download to make sure the download file is valid

Data encryption using AES symmetric encryption

AES: Advanced Encryption Standard

Symmetric encryption system

Supersedes the old DES: Data Encryption Standard

AES class in C# is in the &quot;System.Security.Cryptography&quot; namespace

Encryption process is implemented as a stream

Add &quot;Initialization vector&quot; to defense against attack code breaking

\&gt;\&gt;\&gt;\&gt;\&gt;\&gt;\&gt;\&gt;TBC why its better

Decoding require both initialization vector and encryption key

Code Example of AES encryption:

using System;

using System.IO;

using System.Security.Cryptography;

namespace LISTING\_3\_14\_AES\_encryption

{

    class Program

    {

        static void DumpBytes(string title, byte [] bytes)

        {

            Console.Write(title);

            foreach (byte b in bytes)

            {

                Console.Write(&quot;{0:X} &quot;, b);

            }

            Console.WriteLine();

        }

        static void Main(string[] args)

        {

            string plainText = &quot;This is my super secret data&quot;;

            // byte array to hold the encrypted message

            byte[] cipherText;

            // byte array to hold the key that was used for encryption

            byte[] key;

            // byte array to hold the initialization vector that was used for encryption

            byte[] initializationVector;

            // Create an Aes instance

            // This creates a random key and initialization vector

            using (Aes aes = Aes.Create())

            {

                // copy the key and the initialization vector

                key = aes.Key;

                initializationVector = aes.IV;

                // create an encryptor to encrypt some data

                // should be wrapped in using for production code

                ICryptoTransform encryptor = aes.CreateEncryptor();

                // Create a new memory stream to receive the

                // encrypted data.

                using (MemoryStream encryptMemoryStream = new MemoryStream())

                {

                    // create a CryptoStream, tell it the stream to write to

                    // and the encryptor to use. Also set the mode

                    using (CryptoStream encryptCryptoStream =

                           new CryptoStream(encryptMemoryStream,

                                   encryptor, CryptoStreamMode.Write))

                    {

                        // make a stream writer from the cryptostream

                        using (StreamWriter swEncrypt =

                               new StreamWriter(encryptCryptoStream))

                        {

                            //Write the secret message to the stream.

                            swEncrypt.Write(plainText);

                        }

                        // get the encrypted message from the stream

                        cipherText= encryptMemoryStream.ToArray();

                    }

                }

            }

            // Dump out our data

            Console.WriteLine(&quot;String to encrypt: {0}&quot;, plainText);&quot;

            dumpBytes(&quot;Key: &quot;, key);

            dumpBytes(&quot;Initialization Vector: &quot;, initializationVector);

            dumpBytes(&quot;Encrypted: &quot;, cipherText);

            Console.ReadKey();

        }

    }

}

// Now do the decryption

string decryptedText;

using (Aes aes = Aes.Create())

{

    // Configure the aes instances with the key and

    // initialization vector to use for the decryption

    aes.Key = key;

    aes.IV = initializationVector;

    // Create a decryptor from aes

   // should be wrapped in using for production code

    ICryptoTransform decryptor = aes.CreateDecryptor();

    using (MemoryStream decryptStream = new MemoryStream(cipherText))

    {

        using (CryptoStream decryptCryptoStream =

            new CryptoStream(decryptStream, decryptor, CryptoStreamMode.Read))

        {

            using (StreamReader srDecrypt = new StreamReader(decryptCryptoStream))

            {

                // Read the decrypted bytes from the decrypting stream

                // and place them in a string.

                decryptedText = srDecrypt.ReadToEnd();

            }

        }

    }

}

Encrypting data using other symmetric standards

All of the symmetric encryptions provided by C# are based on the same base class: SymmetricAlgorithm class, which is in System.Security.Cryptography namespace

Example: DES, RC2, Rigndael, Triple DES

DES: Data Encryption Standard was developed in 1970s. It is now regarded as insecure. Should use AES as &quot;drop in&quot; replacement.

RC2: is also regarded as insecure

Rigndael: AES is based on this and is implemented as a subset of it

TripleDES: encrypt the data three times in succession using three different keys. Electronic payment industry use this

Use RSA asymmetric encryption to create public and private keys

RSA: Rivest-Shamir-Alderman is a popular asymmetric encryption standard

RSACryptoServiceProvider class in System.Security.Cryptography namespace

Code example:

using System;

using System.Security.Cryptography;

using System.Text;

namespace LISTING\_3\_16\_RSA\_encryption

{

    class Program

    {

        static void Main(string[] args)

        {

            string plainText = &quot;This is my super secret data&quot;;

            Console.WriteLine(&quot;Plain text: {0}&quot;, plainText);

            // RSA works on byte arrays, not strings of text

            // This will convert our input string into bytes and back

            ASCIIEncoding converter = new ASCIIEncoding();

            // Convert the plain text into a byte array

            byte[] plainBytes = converter.GetBytes(plainText);

            dumpBytes(&quot;Plain bytes: &quot;, plainBytes);

            byte[] encryptedBytes;

            byte[] decryptedBytes;

            // Create a new RSA to encrypt the data

            // should be wrapped in using for production code

            RSACryptoServiceProvider rsaEncrypt = new  **RSACryptoServiceProvider** ();

            // get the keys out of the encryptor

            string publicKey = rsaEncrypt.ToXmlString(includePrivateParameters: false);

            Console.WriteLine(&quot;Public key: {0}&quot;, publicKey);

            string privateKey = rsaEncrypt.ToXmlString(includePrivateParameters: true);

            Console.WriteLine(&quot;Private key: {0}&quot;, privateKey);

            // Now tell the encyryptor to use the public key to encrypt the data

            rsaEncrypt. **FromXmlString** (publicKey);

            // Use the encryptor to encrypt the data. The fOAEP parameter

            // specifies how the output is &quot;padded&quot; with extra bytes

            // For maximum compatibility with receiving systems, set this as

            // false

            encryptedBytes = rsaEncrypt. **Encrypt** (plainBytes, fOAEP:false);

            dumpBytes(&quot;Encrypted bytes: &quot;, encryptedBytes);

            // Now do the decode - use the private key for this

            // We have sent someone our public key and they

            // have used this to encrypt data that they are sending to us

            // Create a new RSA to decrypt the data

            // should be wrapped in using for production code

            RSACryptoServiceProvider rsaDecrypt = new  **RSACryptoServiceProvider** ();

            // Configure the decryptor from the XML in the private key

            rsaDecrypt. **FromXmlString** (privateKey);

            decryptedBytes = rsaDecrypt. **Decrypt** (encryptedBytes, fOAEP: false);

            dumpBytes(&quot;Decrypted bytes: &quot;, decryptedBytes);

            Console.WriteLine(&quot;Decrypted string: {0}&quot;,

                               converter.GetString(decryptedBytes));

            Console.ReadKey();

        }

    }

}

- implement key management;

Implementing public and private key management

To extract public key or private key:

Use &quot;RSACryptoServiceProvider&quot; class and &quot;ToXmlString()&quot; method

Public or private depends on the &quot;includePrivateParameters&quot; boolean argument

Example:

RSACryptoServiceProvider rsaEncrypt = new RSACryptoServiceProvider();

string  **publicKey**  = rsaEncrypt.ToXmlString( **includePrivateParameters: false** );

string  **privateKey**  = rsaEncrypt.ToXmlString( **includePrivateParameters: true** );

To use the key:

Use &quot;RSACryptoServiceProvider&quot; class and &quot;FromXmlString()&quot; method

Example:

RSACryptoServiceProvider rsaDecrypt = new RSACryptoServiceProvider();

rsaDecrypt.FromXmlString( **privateKey** );

Important to store the key file safely, especially the private key, two storage levels:

User level key storage

pass &quot;CspParameters&quot; to &quot;RSACryptoServiceProvider&quot; where to store the keys

Example code:

string containerName = &quot;MyKeyStore&quot;;

CspParameters csp = new CspParameters();

csp.KeyContainerName = containerName;

// Create a new RSA to encrypt the data

RSACryptoServiceProvider rsaStore = new RSACryptoServiceProvider( **csp** );

To delete the stored key, set &quot;PersistKeyInCsp&quot; property to &quot;false&quot;

And run &quot;rsaStore.Clear()&quot;

Machine level key storage

Useful when the machine has multiple users requiring the same key

Usually store in: C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys

Similar to user level key storage, add some more flag to specify:

cspParams.Flags = CspProviderFlags.UseMachineKeyStore;

Example code:

CspParameters cspParams = new CspParameters();

cspParams.KeyContainerName = &quot;Machine Level Key&quot;;

// Specify that the key is to be stored in the machine level key store

**cspParams.Flags = CspProviderFlags.UseMachineKeyStore;**

Delete is the same as the user level key storage

- manage and create certificates;

Digital signatures and certificate:

For verifying A public key associated with Bob actually came from Bob, not Eve

Identity verification process using the public key infrastructure (PKI) is involved

https://en.wikipedia.org/wiki/Public\_key\_infrastructure

&quot;Digital Certificate&quot; can be used to prove the ownership of a public key is issued by &quot;Certificate authority (CA)&quot;

Ususally the CA is the entity implements the PKI

it publishes a certificate policy and Certification Practice Statement

stating the PKI&#39;s requirement and the CA practice and standard in issuing, revoking, managing the certificates

These documents allow outsiders to analyze the PKI&#39;s trustworthiness

&quot;Digital Certificate&quot; aka &quot;public key certificate&quot; or &quot;identity certificate&quot; contains

public key

info of the key owner, e.g. domain name, email, real name

most importantly, the Certificate authority &quot;signs&quot; the certificate, usually in X.509 format

https://en.wikipedia.org/wiki/Public\_key\_certificate

https://en.wikipedia.org/wiki/X.509

Digital Certificate can be requested directly from CA who verifies the requestor identify and isuue the certificate

CA can also delegate the verification to authorized registration authority (RA)

CA stores, issues and signs the digital certificates

CA hosts a certificate repository and provide directory search and download for public to access

Bob as sender can use the corresponding private key to sign a message

i.e. sign the message digest as &quot;Digital Signature&quot;

https://en.wikipedia.org/wiki/Digital\_signature

Alice as receiver can verify the signature using a public key in a digital certificate

which can be verified through CA&#39;s central directory or authorized validation authority (VA)

reference:

https://security.stackexchange.com/questions/24383/public-key-infrastructure?rq=1

[pic to be included]

Website which use HTTPS also use Digital Certificate technique supported by PKI

visitor&#39;s browser can check the if the website serve a valid digitial certificate

using protocol such as Online Certificate Status Protocol

https://en.wikipedia.org/wiki/Online\_Certificate\_Status\_Protocol

Visitors can know for sure they are visiting the authentic site

and can encrypt the communication in between server and client

reference:

https://superuser.com/questions/620121/what-is-the-difference-between-a-certificate-and-a-key-with-respect-to-ssl

[pic to be included]

[pic to be included]

Its important to check if the certificate is still valid e.g. expiry date, revocation status

One of the main reason the certificate being revoked is due to the corresponding private key being stolen

https://en.wikipedia.org/wiki/Certificate\_revocation\_list

CA usually issue the whole public private key in pair to requestors

as its more user friendly due to less work in the requestors side

CA privodes the requestors a .p12 file containing the public private key and digital certicate

Advanced users can generate the private key themselves, keep it save, and provide the public key to CA to sign

Hongkong Post is CA and do the RA and VA work with no authorized RA, VA

Repository / Certificate Revocation List

https://www.hongkongpost.gov.hk/product/ecert/crl/index.html

Search and Download Certificate

https://www.hongkongpost.gov.hk/product/ecert/status/index.html

Hong Kong Post: THE CERTIFICATION PRACTICE STATEMENT

https://www.ecert.gov.hk/product/cps/ecert/img/cps\_en47.pdf

There are other PKI like protocols like PGP which more emphasis on secured communication and decentralization

Signing documents using certificates

Self-signed Test certificate can be created for test purpose

Use Visual Studio developer command prompt to access a program called &quot;makecert&quot; which will create a X.509 certificate

Steps to &quot;sign&quot; a message:

1. Convert the message text to byte

ASCIIEncoding converter = new ASCIIEncoding();

byte[] messageToSignBytes = converter.GetBytes(messageToSign);

1. Generate the hash of the message byte

HashAlgorithm hasher = new SHA1Managed();

// Use the hasher to hash the message

byte[] hash = hasher.ComputeHash(messageToSignBytes);

dumpBytes(&quot;Hash for message: &quot;, hash);

1. Get the certificate from certificate store

X509Store store = new X509Store(&quot;msDemoCertStore&quot;, StoreLocation.CurrentUser);

store.Open(OpenFlags.ReadOnly);

// should be wrapped in using for production code

X509Certificate2 certificate = store.Certificates[0];

1. Sign the hash using the certificate private key

RSACryptoServiceProvider encryptProvider =

                            certificate.PrivateKey as RSACryptoServiceProvider;

byte[] signature = encryptProvider.SignHash(hash,CryptoConfig.

            MapNameToOID(&quot;SHA1&quot;));

Example:

makecert democert.cer

or to generate a certificate with name and store name:

makecert -n &quot;CN=MichaelSo&quot; -sr currentuser -ss msDemoCertStore

sample code for using the certificate to sign(encrypt action) the message digest which is generated by hashing:

using System;

using System.Security.Cryptography.X509Certificates;

using System.Security.Cryptography;

using System.Text;

namespace LISTING\_3\_20\_Signing\_data

{

    class Program

    {

        static void Main(string[] args)

        {

            // This will convert our input string into bytes and back

            ASCIIEncoding converter = new ASCIIEncoding();

            // Get a crypto provider out of the certificate store

            // should be wrapped in using for production code

            X509Store store = new X509Store(&quot;msDemoCertStore&quot;, StoreLocation.CurrentUser);

            store.Open(OpenFlags.ReadOnly);

            // should be wrapped in using for production code

            X509Certificate2 certificate = store.Certificates[0];

            // should be wrapped in using for production code

            RSACryptoServiceProvider encryptProvider =

                            certificate.PrivateKey as RSACryptoServiceProvider;

            string  messageToSign = &quot;This is the message I want to sign&quot;;

            Console.WriteLine(&quot;Message: {0}&quot;, messageToSign);

            byte[] messageToSignBytes = converter.GetBytes(messageToSign);

            dumpBytes(&quot;Message to sign in bytes: &quot;, messageToSignBytes);

            // need to calculate a hash for this message - this will go into the

            // signature and be used to verify the message

            // Create an implementation of the hashing algorithm we are going to use

            // should be wrapped in using for production code

            HashAlgorithm hasher = new SHA1Managed();

            // Use the hasher to hash the message

            byte[] hash = hasher.ComputeHash(messageToSignBytes);

            dumpBytes(&quot;Hash for message: &quot;, hash);

            // Now sign the hash to create a signature

            byte[] signature = encryptProvider.SignHash(hash,CryptoConfig.

            MapNameToOID(&quot;SHA1&quot;));

            dumpBytes(&quot;Signature: &quot;, messageToSignBytes);

            // We can send the signature along with the message to authenticate it

            // Create a decryptor that uses the public key

            // should be wrapped in using for production code

            RSACryptoServiceProvider decryptProvider =

                            certificate.PublicKey.Key as RSACryptoServiceProvider;

            // Now use the signature to perform a successful validation of the message

            bool validSignature = decryptProvider.VerifyHash(hash,

                 CryptoConfig.MapNameToOID(&quot;SHA1&quot;), signature);

            Console.WriteLine(&quot;Correct signature validated OK: {0}&quot;, validSignature);

            // Change one byte of the signature

            signature[0] = 99;

            // Now try the using the incorrect signature to validate the message

            bool invalidSignature = decryptProvider.VerifyHash(hash,

                                    CryptoConfig.MapNameToOID(&quot;SHA1&quot;), signature);

            Console.WriteLine(&quot;Incorrect signature validated OK: {0}&quot;, invalidSignature);

            Console.ReadKey();

        }

    }

}

To find specific certificate in the certificate store, use:

X509Certificate2Collection.Find(X509FindType, Object, Boolean) Method

Ref: [https://docs.microsoft.com/en-us/dotnet/api/system.security.cryptography.x509certificates.x509certificate2collection.find?view=netframework-4.8](https://docs.microsoft.com/en-us/dotnet/api/system.security.cryptography.x509certificates.x509certificate2collection.find?view=netframework-4.8)

X509FindType is Enum type which has &quot;FindBySubjectDistinguishedName&quot;

Ref: https://docs.microsoft.com/en-us/dotnet/api/system.security.cryptography.x509certificates.x509findtype?view=netframework-4.8

- implement the System.Security namespace;

Contains the cryptography classes for encrypt and decrypt data

All the encryption classes are based on:

SymmetricAlgorithm abstract class

To implement own encryption class, extend the SymmetricAlgorithm class

And the new class can be used as drop in replacement

BUT strongly not recommended to implement own encryption class

The namespace also has classes for file access permissions



- hash data;

Data integrity by hashing data

checksum: easy, but not good!

Because too many collisions happen easily

e.g. Hello world, world Hello, Hemmm world

all give same checksum value!

Hash: map data of any size to a fixed size, irreversible, collision resistance / hard to collide, reasonable fast

Unlike checksum, hash algorithm will weight the value according to the position

reference: [https://en.wikipedia.org/wiki/Cryptographic\_hash\_function](https://en.wikipedia.org/wiki/Cryptographic_hash_function)

All C# objects has GetHash() method

hash value of GetHash() method

Default: based on memory location

Can override to base on content of that object

String object is based on contents of the string

Varies between machine, version of .NET library i.e. not deterministic

Length: Single integer value, held in four bytes of memory

Good enough for local usage for index and search, but not good enough for global or cryptographic application which need larger hash value

MD5 hashing

MD: Message Digest, ver. 5 algorithm

Size: 16 bytes / 128 bits

Relatively high chance for collision on purpose (malicious)

No longer suitable for cryptographic purpose e.g. message signing

Still good for:

Detect data corruption on storage and transmission

Main advantage: fast!

SHA1 hashing

Secure Hash Algorithm (SHA) 1

Size: 20 byte (160 bit)

Better than MD5, but still vulnerable to brute force attack

Malicious attempt on intentional collision

Avoid!

SHA2 hashing

Improve over SHA1

Family of six hash functions

Size: 224, 256, 384, 512

Sample:

static byte[] calculateHash(string source)

        {

            // This will convert our input string into bytes and back

            ASCIIEncoding converter = new ASCIIEncoding();

            byte[] sourceBytes = converter.GetBytes(source);

**            HashAlgorithm hasher = SHA256.Create();**

            byte[] hash =  **hasher.ComputeHash** (sourceBytes);

            return hash;

        }

Vulnerable to length attack

Malicious attempt of adding things to the end of file without changing hash code

- encrypt streams

Accept &quot;stream&quot; type, e.g. MemoryStream, including CryptoStream

CryptoStream takes in: stream, encryptor/decryptor, and write or read mode

Sample:

string message = &quot;SECRET MESSAGE&quot;;

**SymmetricAlgorithm symmetric = SymmetricAlgorithm.Create();**

**ICryptoTransform encryptor = symmetric.CreateEncryptor(symmetric.Key, symmetric.IV);**

MemoryStream memoryStream = new MemoryStream();

//crptoStream know encrptor and stream in which data to written

**CryptoStream crptoStream = new CryptoStream(memoryStream, encryptor, CryptoStreamMode.**

**Write);**

//writer has reference of cryptoStream (what to encrypt and where)

using (StreamWriter streamWriter = new StreamWriter(crptoStream))

{

  //write the ecrypted message into memeory stream

  streamWriter.Write(message);

}

# Manage assemblies

Assemble:

individual component of an application

output of compiled source code

physical code to be used in software deployment

usually in two form:

exe: executable program

dll: dynamic linked library (without Main() method)

Assembles in .NET:

Expressed in &quot;Intermediate Language&quot;, MSIL: Microsoft Intermediate Language

Along with assets like picture, audio

When execute by .NET runtime:

Just In Time compiler (JIT) converts MSIL files into machine code for the underlying hardware

.NET app from compile to execute are divided into Two stages shown above:

1

# st
: Source code to Intermediate Language

2

# nd
: Intermediate Language to machine code

Advantage for dividing into two stages:

Intermediate Language is not bind to particular programming languages

i.e. C#, F#, VB source code can all be compiled to the same Intermediate Language

Intermediate Language use JIT to compile to machine code depending on hardware it runs on

i.e. output is portable across different platforms

using Visual Studio Reference Manager

Project in Visual Studio can add components from other Visual Studio projects

One-way Dependency relationship is created between the two projects

changes in one can affect on other

e.g. updates of the underlying depending library will be reflected in projects using it

NO Circular dependency is allowed

i.e. two library can not depend on each other

To create and distribute app, basic understanding on how to manage assembles is essential

- Version assemblies

Sharing DLL libraries can cause problems as app may depend on diff version of DLL

Old windows has the &quot;DLL Hell&quot; problem

.NET solve this problem by letting the App specific which version of DLL is needed

To set assembly version: edit &quot;Assembly information&quot; in Visual Studio

Assembly information is store in a file called &quot;AssemblyInfo.cs&quot;, in attribute format

Can edit the info in file directly

Version number format in Microsoft:

major.minor[.build[.revision]]

major: breaking API, breaking backward compatibility, user need re-training

minor: internal behaviors, feature update, bug and performance fix

build: build server identifier

revision: urgent security or bug fix in production environment

reference:

[https://www.reddit.com/r/learnprogramming/comments/3d3xli/how\_do\_you\_decide\_when\_to\_increment\_major\_minor/](https://www.reddit.com/r/learnprogramming/comments/3d3xli/how_do_you_decide_when_to_increment_major_minor/)

[https://intellitect.com/making-sense-of-assemblyversion-numbers/](https://intellitect.com/making-sense-of-assemblyversion-numbers/)

[https://softwareengineering.stackexchange.com/questions/24987/what-exactly-is-the-build-number-in-major-minor-buildnumber-revision](https://softwareengineering.stackexchange.com/questions/24987/what-exactly-is-the-build-number-in-major-minor-buildnumber-revision)

other version number format: [https://semver.org/](https://semver.org/)

- sign assemblies using strong names

A strong-named assembly has 4 things:

a fully qualified name that includes the assembly&#39;s name,

culture info (optional),

digital signature with corresponding public key,

version number

e.g. &quot;myApp&quot;, Version=1.0.1234.0, Culture=en-US, PublicKeyToken=b77a5c561934e089c

Similar to signing file using cryptography method, i.e. public private key

Assemble can be signed using private key to create &quot;strong-named&quot; assembly

Benefit for creating &quot;strong-name&quot; assembly:

Version checking only work with signed assembles

Strong-name app/lib requires its dependency also strong-named to ensure the entire integrity

Strong-named assembly can be loaded into Global Assembly Cache (GAC) for better performance and can be shared with other app

Strong-named assembly has unique name, therefore, it allows multiple versions of same assembly to co-exist on a system, while no confusion or &quot;DLL Hell&quot;

&quot;sn.exe&quot; command line tool can be used to generate public private key to be used for signing an assembly

4 ways to sign and create strong named assembly:

1. &quot;al.exe&quot; assembly linker can assign the key pair to the assembly

al /out:\&lt;assemblyName\&gt; \&lt;moduleName\&gt; /keyfile:\&lt;keyfileName\&gt;

ref: https://docs.microsoft.com/en-us/dotnet/framework/app-domains/how-to-sign-an-assembly-with-a-strong-name

Ref: https://docs.microsoft.com/en-us/dotnet/framework/app-domains/create-and-use-strong-named-assemblies

1. Visual Studio also provide tools to generate keys and sign the assembles

Corresponding Public key is placed in the manifest of the assembly for validation

Ref: https://docs.microsoft.com/en-us/dotnet/framework/app-domains/how-to-sign-an-assembly-with-a-strong-name#to-create-and-sign-an-assembly-with-a-strong-name-by-using-visual-studio

1.     Add the System.Reflection.AssemblyKeyFileAttribute or AssemblyKeyNameAttribute attribute to your source code file, and specify the name of the file or container that contains the key pair to use when signing the assembly with a strong name.

Compile the source code file normally.

Ref: [https://docs.microsoft.com/en-us/dotnet/framework/app-domains/how-to-sign-an-assembly-with-a-strong-name#to-sign-an-assembly-with-a-strong-name-by-using-attributes](https://docs.microsoft.com/en-us/dotnet/framework/app-domains/how-to-sign-an-assembly-with-a-strong-name#to-sign-an-assembly-with-a-strong-name-by-using-attributes)

1. To sign an assembly with a strong name by using the compiler

Ref: https://docs.microsoft.com/en-us/dotnet/framework/app-domains/how-to-sign-an-assembly-with-a-strong-name#to-sign-an-assembly-with-a-strong-name-by-using-the-compiler

Full qualified assembly name and public key token

Strong-named assembly has a &quot;fully qualified name&quot; which is made of:

Assembly&#39;s name

Culture of the assembly

Public key token

Version number

These ensure the name is unique

Public key token is used in the manifest to specify which assemblies it should work with

Delay Signing

Private key should be kept securely

Use public key and set special attribute during development

[assembly: **AssemblyKeyFileAttribute** (&quot;myKey.snk&quot;)]

[assembly: **AssemblyDelaySignAttribute** (true)]

Sign using private key right before release for distribution

Limitations of strong-names

Prevent name crash, tampering the content

NO protection of viewing the code by dis-assembling the intermediate code

Can NOT identify the person who signed it

If want the publish a signed app able to be identified by other who made it, need to obtain a digital certificate, by Authenticode, just like signing message digest

Global assembly cache (GAC)

Put the assembly if want it to be shared among different apps

GAC is implemented as a folder named &quot;assembly&quot; in Windows folder

Assembly being copied to GAC folder during app installation

.NET Runtime automatically searches for an assembly in GAC and other places

- implement side-by-side hosting

&quot;side hosted&quot;: GAC can contain multiple versions of the same assembly which are strong named

.NET assembly has manifest contains reference to specify the assembly version it works with, hence resolve the issue of DLL Hell

Assembly binding redirection

the app (assembly) has specified a particular version of assembly to be used:

app/assembly will keep using the one specified (original) instead of any newer bugfix-ed assembly that is deployed into GAC

to tell the assembly to use new updated underlying, use &quot;assembly binding redirection&quot;

assembly binding redirection modify the behavior of GAC using &quot;policy file&quot;

Redirection can be specified at three levels:

Application level

Publisher level

Machine level

&quot;policy file&quot; is used to specify requests to a particular assembly to be redirected to its replacement

Details of using &quot;policy file&quot;, XML formatted:

Application level:

Same name as the app, with &quot;.config&quot; extension

Contains app configuration options for a specific app

Example:

\&lt;configuration\&gt;

    \&lt;runtime\&gt;

        \&lt;assemblyBinding xmlns=&quot;urn:schemas-microsoft-com:asm.v1&quot;\&gt;

            \&lt;dependentAssembly\&gt;

                \&lt;assemblyIdentity name=&quot; MusicStorage &quot;

                                         publicKeyToken=&quot;9aa4095193e2d7b8&quot; /\&gt;

                \&lt;bindingRedirect oldVersion=&quot;1.0.0.0-1.2.0.0&quot; newVersion=&quot;2.0.0.0&quot;/\&gt;

            \&lt;/dependentAssembly\&gt;

        \&lt;/assemblyBinding\&gt;

    \&lt;/runtime\&gt;

\&lt;/configuration\&gt;

Replacement MUST have the same public key token value as the original to ensure new version is issued by same publisher

Publisher level:

Instead of modifying the app one by one

Publisher can create policy file

bind it to an assembly

install it int the GAC

Can be done during install of a service pack or app update

Machine level:

Adding entries into system wide &quot;machine.config&quot; file

System admin can do it, and redirect all app to use particular versions of assembles

- put an assembly in the global assembly cache

Consider is it necessary to put assembly in GAC

Primary reason: assembly sharing for different apps

Assemblies added into GAC can only be removed by admin, prevent being remove easily

Use side by side loading, keep different version and use assembly binding redirection

Add assembly to GAC using:

Installation process, use in distribution/production

gacutil, the Global Assembly Cache tool, use in development

- create a WinMD assembly

WinMD: Windows Metadata

.NET assembly contains metadata that describe the assembly

WinRT: Windows Runtime, as Windows API

WinRT component is held in a WinMD file

WinMD contains the metadata, with executable code or wrapping some compiled code

WinRT is one of the main tech for developing in Universal Windows Platform

WinRT apps interact with Windows via WinMD files

App dev Can create in Visual Studio

Limitation:

Public class must be sealed

Can not use generic

Must use WinRT types for all member variables

Must not have public member name start with word &quot;Windows&quot;

Namespace of public type must match name of the assembly

# Debug an application

Common cause for bug is a fox for a previous one

Important to walk away for the code away and like the &quot;background&quot; mode of the brain kick in

Generate more data about the unexpected bad behavior

- Create and manage preprocessor directives

Modify compiler behaviors by giving compiler directives

compiler directives aka &quot;preprocessor directives&quot;: pre-ceded by the # character

e.g. #define, #if, #elif, #endif

C/C++ preprocessor directives are more powerful than C#

Use the #if directive to control code compilation

Code instrumentation:

add code that produce diagnostic messages for working out what the program is doing

DO NOT use program logic for diagnostic purpose

As it will increase unnecessary output size

Make the program run slower due to extra check

Easier for reverse engineering to figure out how it works

#if SYMBOL &amp; #endif pair:

control whether or not a particular set of statements inside the &quot;if&quot; block is processed by the compiler

if not processed by compiler, won&#39;t be in assembly

#define:

define a symbol that controls the behavior of #if

if the &quot;symbol&quot; of #if using is defined, then the block would be compiled

aka: conditional compilation symbol

conditional compilation symbols

can only define at the start of file

can use with logical expression with multiple symbols

can also be defined in the properties of an app in VS

set multiple properties and separated by the semicolon character

two built in symbols can be used, DEBUG, TRACE

#undef SYMBOL can be placed at the start of file to remove the symbol

Can be used to created platform specific behaviors for each different configuration

DO NOT use for multiple languages or different screen size support

Use culture provisions of .NET and adaptive displays instead

Manage method execution with the Conditional attribute

Defined in System.Diagnostics namespace

As metadata attach to the code

Use to flag a method as like #if, enable or disable a method

Example:

[Conditional(&quot;DEBUG&quot;)]

Method will be in the assembly, but run it or not is depend on the flag

Mark code as obsolete using the Obsolete directive

Class, interface, method, property

Pass a message and Boolean value to indicate compiler should produce warning (false) or error (true)

Example:

[Obsolete (&quot;This method is obsolete. Call  NewMethod instead.&quot;, false)]

public string OldMethod()

Control compilation with warning and error directives

#warning: produce compilation warning, display the message

Example:

**#warning**  This version of the library is no longer maintained. Use Version 2.5

#error: compilation error and prevent source code from compiling

Example:

#if DIAGNOSTICS &amp;&amp; DEBUG

**#error**  Cannot run with both diagnostics and debug enabled

#endif

Manage compiler warning reporting with the pragma directive

#pragma warning directive: disable warning reporting for a region of code.

Example:

**#pragma warning disable**

        public async Task\&lt;IActionResult\&gt; Resume()

        {

            return View();

        }

**#pragma warning restore**

Or look up the warning code number and disable specify by:

#pragma warning disable  **CS1998**



Identify error positions with #line

Some auto-generated elements may disrupt the line number show in code editor

#line directive can set the reported line number and specify the file name

Need to restore it after using

Example:

**#line 1 &quot;kapow.ninja&quot;**

            throw new Exception(&quot;Bang&quot;);

**#line default**

Use with care!

Hide code using #line

Some auto-generated elements may not want debugger&#39;s stepper to go through it

As programmer only interested in the manually written code

#line hidden can &quot;hide&quot; the code from debugging

Example:

#line hidden

// The debugger will not step through these statements

Console.WriteLine(&quot;You haven&#39;t seen me&quot;);

#line default

Use DebuggerStepThrough to prevent debugger stepping

Prevent line by line debug for some specific code, esp. useful for auto gen code

Can mark a method, or mark a class, so all methods in a class won&#39;t be line by line debugged

Breakpoints inside the marked elements will not be triggered

Example:

[DebuggerStepThrough]

public void Update() {…}

- choose an appropriate build type

Build configuration controls the settings apply to the build process

Built in two configs: Debug, Release;

for two different situations, different behaviors is generated

e.g. Debug mode would Initialize unused variable while Release mode won&#39;t

Debug: &#39;nop&#39; statements, &quot;no operation&quot;

Trigger when there are breakpoints in the program

&quot;Just My Code&quot; is a VS feature for skip over library function when debugging

Without it, debug can go into the implementation of Console.WriteLine()

Release mode will not hit breakpoint and will raise warning if try to break

Release mode: optimization like &quot;inline&quot; implementation of short methods

If the method body is very short, compiler will copy the code instead of generating instruction that manage a method call

Release mode: more aggressive in running garbage collection, finalizer methods

Basically, debug add extra things for &quot;dig in&quot;, release remove and optimize code

Configuration manager can be used to build custom configs

- manage program database files (debug symbols)

Build in debug mode will produce not only the executable assembly,

also produce a file end with &quot;pdb&quot;

pdb: program debug database file, aka symbol file

pdb contains info for debugger

mapping of program source code to the compiled statement

names of all symbol and

their address in program memory space when program run

new pdb is generated when program is compiled

GUID: globally unique identifier, is used to link the executable and the debug db

The GUID is checked and only db with matching GUID can be used

Symbol servers

Provide debug database information to program

Team Foundation Server (TFS) can save all debug files to central server

Public and private symbols

Pdb contains two kind of symbol information, public and private

Public: descriptions of the elements that are public

Private: private methods, local variables, etc

To hide private symbol from other, can use &quot;pdbcopy&quot; tool to remove it

pdbcopy: can make a copy of pdb and remove private elements, can also take in a list of item to be removed

output from pdbcopy has the same GUID as the incoming, so the copy can be used with the same executable

pdbcopy is not in VS, its part of Debugging Tools set for Windows

good practice to deploy pdb to production environment

ref: [https://stackoverflow.com/questions/5457095/release-generating-pdb-files-why](https://stackoverflow.com/questions/5457095/release-generating-pdb-files-why)

how to: https://stackoverflow.com/questions/14464151/deploying-pdb-files-in-release

# Implement diagnostics in an application

Easy fix bug is the bug is &quot;obvious&quot; and easily repeat

Bad bugs are something happen now and then, not easy to repeat or has obvious pattern

To fix bad bug, diagnostic output is important

it gives much more info,

contain the situation the program is in and

conditions that triggered the bug

Important to consider security during diagnostics design

Conduct risk and impact analysis

Decide how much effort is require for securing diagnostic components

.NET framework offers a number of diagnostics features for different use case

- Implement logging and tracing

Without direct access to the local debug build of a failing program

Maybe Due to its deployed in a remote user

Logging and tracing are crucial too

Logging:

Find out what the program is doing and what happened when it failed

Tracing:

Discover the path followed by the program to perform a particular action

Logging -\&gt; macro history, the environment Tracing-\&gt; micro history, the path, breadcrumb

Trace execution using Debug and Trace

System.Diagnostics namespace provides Debug and Trace classes

Both has similar features, but to be used in different situation

Debug: debug mode, active and executed in debug build, not in release build

Trace: active in release build, also active in debug build

i.e. Debug mode: both Debug and Trace, Release mode: trace only

basic set of static methods for Debug and Trace

Output Text, can be with condition:

WriteLine(&quot;string&quot;)

WriteLineIf(Bool\_Condition, &quot;String&quot;)

Output Text Styling:

Indent()

Unindent()

Test, prompt if bool is false:

Assert(bool)

Act as &quot;Save&quot; button, Push the &quot;write&quot; to listeners:

Flush()

better set the AutoFlush property to true, flush() will be called on every msg. unhandled exception or critical error can cause last 4KB of data loss

ref: c# in a nutshell p546

Close and save:

Close()

Debug static methods are not active due to Conditional attribute setting

Trace class has additional methods for different levels, work with the Bool\_Condition in WriteLineIf():

TraceInformation(string)

TraceWarning(string)

TraceError(string)

Use assertion in Debug and Trace

Assertion:

Dictionary definition: a statement that you strongly believe is true

Wiki: In computer programming, an assertion is a statement that a predicate (Boolean-valued function, i.e. a true–false expression) is always true at that point in code execution.

E.g. &quot;name of a customer is never an empty string&quot;, then make an assertion to check the variable and see if it return true, if not, issue an error

If assertion return false, Fail(msgString) will be called

Debug.Assert(condition\_tested\_ret\_bool)

True, pass the test

False, fail the test and display message offering options:

Abort, retry, ignore

Use listeners to gather tracing information

Debug and Trace has a default listener attached which send output to the VS output window by default and pop dialog when fail() is called

Other Listeners can be attached to direct the output to other places

Similar to event and delegate!

When Trace.Write() or similar methods are called, messages will be written to all the attached listeners

Example:

//create new listener

**TraceListener consoleListener = new ConsoleTraceListener();**

**//attach to the Trace**

**Trace.Listeners.Add(consoleListener);**

Trace.TraceInformation(&quot;This is an information message&quot;);

Trace.TraceWarning(&quot;This is a warning message&quot;);

Trace.TraceError(&quot;This is an error message&quot;);

**consoleListener.**** TraceEvent(TraceEventCache, String, TraceEventType, Int32, String)**

// Writes trace information, a message, and event information to the listener specific output.

TraceEventCache Class

Provides trace event data specific to a thread and a process.

Ref: https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.tracelistener.traceevent

If only want to write to a specific listener instead of all attached:

Grab the specific listener and call write() or similar methods

Code sample:

System.Diagnostics.XmlWriterTraceListener listener = new XmlWriterTraceListener(&quot;logfile.log&quot;);

listener.WriteLine(msgString);

listener.Close();

List of TraceListener:

ConsoleTraceListener – send to console

DelimitedTextTraceListener – send to text writer

EventLogTraceListener – send to event log

EvnetSchemaTraceListner – send to XML encoded file compliant with the event log schema

TextWriterTraceListner – send to a given TextWriter

XMLWriterTraceListner – send XML formatted to XML writer

Can add multiple listeners, and can remove each

Debug class also can use the counterpart listeners

Trace using the TraceSource class

Trace and Debug class provide tracing in the form of message

TraceSource class provides a more &quot;structured&quot; and &quot;defined&quot; status to use

uses &quot;event&quot; to log instead of simple message

TraceSource create as an object and has a name which can be used as reference in the app config later

basic event contains:

event type and event number

String and data object can be attached to an event optionally

Event number is self-defined integer

String can be the message

data object is in the form of object collection

TraceEvent() – use for without date object attached

TraceData() – use for with date object attached

A wide range of event in the type of TraceEventType. Listed in the order of significance:

Stop, Start, Suspend, Resume, Transfer

Activity event types

Indicated what has occur in normal operation, not error

Verbose

Detail info about app activity during normal operation

e.g. method enter, exit, object create, destroy

Information

Significant info about the app during normal operation

e.g. transaction start, or ended

Warning

Warning event

e.g. login fail, operation took longer to complete than expected, low in resource such as disk or memory

Error

Error has occurred and dealt with by the app

e.g. wrong format for incoming data, operation fail to complete and retry, properly handled exception

Critical

Error may the app cannot continue or no meaning to continue

e.g. exception unable to handle, a required resource or required resource or connection is no longer available

Events are delivered to VS Output window unless TraceListener is added

Sample code:

TraceSource trace = new TraceSource(&quot;Tracer&quot;,SourceLevels.All);

trace.TraceEvent(TraceEventType.Start, 10000);

trace.TraceEvent(TraceEventType.Warning, 10001);

trace.TraceEvent(TraceEventType.Verbose, 10002, &quot;At the end of the program&quot;);

trace.TraceData(TraceEventType.Information, 1003, new object[] { &quot;Note 1&quot;, &quot;Message 2&quot; });

trace.Flush();

trace.Close();

Use TraceSwtich to control tracing output

Control what level of Trace need to be outputted

Three level of trace, info, warning, error

If set at warning, only warning and error trace level will be output

Sample code:

TraceSwitch control = new TraceSwitch(&quot;Control&quot;, &quot;Control the trace oupput&quot;);

control.Level = TraceLevel.Warning;

if(control.TraceError)

{

    Console.WriteLine(&quot;An error has occurred&quot;);

}

Trace.WriteLineIf(control.TraceWarning, &quot;A warning message&quot;);

Use SourceSwitch to control tracing

Similar to TraceSwitch, it controls the level of output

It works on TraceSource instead of Trace

Full name should be TraceSource&#39;s Switch

Create a SourceSwitch object with name and description

Set the targeted level using SourceLevels struct refers to TraceSource&#39;s TraceEventType

Assign the TraceSwitch object to the TraceSource&#39;s Switch property

Sample code:

TraceSource trace = new TraceSource(&quot;Tracer&quot;, SourceLevels.All);

SourceSwitch control = new SourceSwitch(&quot;control&quot;, &quot;Controls the tracing&quot;);

&quot;control.Level = SourceLevels.Information;

trace.Switch = control;

trace.TraceEvent(TraceEventType.Start, 10000);

trace.TraceEvent(TraceEventType.Warning, 10001);

trace.TraceEvent(TraceEventType.Verbose, 10002, &quot;At the end of the program&quot;);

trace.TraceEvent(TraceEventType.Information, 10003, &quot;Some information&quot;,

new object[] { &quot;line1&quot;, &quot;line2&quot; });

trace.Flush();

trace.Close();&quot;

Configure tracing using application config files

App config like those used in Assembly binding redirection can be use to configure TraceSource&#39;s output level

Config of target TraceSource&#39;s switch name and level can be set in the XML config file

Sample:

\&lt;?xml version=&quot;1.0&quot; encoding=&quot;utf-8&quot; ?\&gt;

\&lt;configuration\&gt;

    \&lt;startup\&gt;

        \&lt;supportedRuntime version=&quot;v4.0&quot; sku=&quot;.NETFramework,Version=v4.6.1&quot; /\&gt;

    \&lt;/startup\&gt;

**  \&lt;system.diagnostics\&gt;**

**    \&lt;switches\&gt;**

**      \&lt;add name=&quot;configControl&quot; value=&quot;All&quot; /\&gt;**

**    \&lt;/switches\&gt;**

**  \&lt;/system.diagnostics\&gt;**

\&lt;/configuration\&gt;

If a Trace can&#39;t be found by the name, a trace is still created but without any output

Create trace listener in app config file

\&lt;configuration\&gt;

  \&lt;system.diagnostics\&gt;

**    \&lt;trace autoflush=&quot;false&quot; indentsize=&quot;4&quot;\&gt;**

**      \&lt;listeners\&gt;**

        \&lt;add name=&quot;myListener&quot; type=&quot;System.Diagnostics.TextWriterTraceListener&quot; initializeData=&quot;TextWriterOutput.log&quot; /\&gt;

        \&lt;remove name=&quot;Default&quot; /\&gt;

**      \&lt;/listeners\&gt;**

**    \&lt;/trace\&gt;**

  \&lt;/system.diagnostics\&gt;

\&lt;/configuration\&gt;

Trace.TraceInformation(&quot;Test message.&quot;);

// You must close or flush the trace to empty the output buffer.

Trace.Flush();

Ref: [https://docs.microsoft.com/en-us/dotnet/framework/debug-trace-profile/how-to-create-and-initialize-trace-listeners](https://docs.microsoft.com/en-us/dotnet/framework/debug-trace-profile/how-to-create-and-initialize-trace-listeners)



Advanced tracing

Create and assign listeners to TraceSource object in config file

Apply filter to listeners, so a given listener only receive particular levels of tracing info

- profiling applications

Don&#39;t optimize performance too early

VS and .NET provide tools for this issue

The StopWatch class

Measure a part of program spent how much time

Use the info to understand performance

StopWatch object provides

Start, Stop, Reset, Restart methods

Restart – reset stop watch and start again

Sample code:

Stopwatch stopwatch = new Stopwatch();

stopwatch. **Start** ();

sequentialTest();

stopwatch. **Stop** ();

Console.WriteLine(&quot;Sequential time in milliseconds: {0}&quot;,

                 stopwatch. **ElapsedMilliseconds** );

stopwatch. **Restart** ();

parallelTest();

stopwatch. **Stop** ();

Console.WriteLine(&quot;Parallel loop time in milliseconds: {0}&quot;,

                  stopwatch. **ElapsedMilliseconds** );

Profiling in Visual Studio

Diagnostics display in VS gives an overview of the app performance

For finding where the app spends most of its time, VS contains other tools for more detailed result

Performance Profiler tools has a Performance Wizard which can be used as starting point for profiling

Performance wizard has for profiling options:

CPU sampling – monitor CPU bound app

Instrumentation – measure function call counts and timing

.NET memory allocation – log create and destroy of objects and activity of garbage collector, if too many activity, can consider create pools of reuse object

Resource contention data – identify bottlenecks of multi-thread app by discovering the blocking shared resource



- create and monitor performance counters

Windows as the OS level provide performance counter that can be used to monitor the computer

Performance Monitor (perfmon) app can view these counters

Perfmon can be accessed from Windows Powershell or command prompt

Program can read the performance counters to monitor the system

Read performance counters

Performance counter is identified by:

Category name, counter name, instance name

Sample code:

PerformanceCounter processor = new **PerformanceCounter** (

                **categoryName** :&quot;Processor Information&quot;,

                **counterName** : &quot;% Processor Time&quot;,

                **instanceName** :&quot;\_Total&quot;);

Console.WriteLine(&quot;Processor time {0}&quot;, processor.**NextValue()**);

Create your own performance counters

Program can create own&#39;s performance counters in the system

These created counters can be accessed by other programs on the machine

The program creates the counters using PerformanceCounterCategory.Create()

The counter detail can be defined using CounterCreationData() and then group in CounterCreationDataCollection

Then pass the collection to the create()

PerformanceCounterType: in many cases, Counter and BaseCounter works in pair

counter pairs:

SampleFraction with SampleBase,

RawFraction with RawBase, CounterMultiTimer,

CounterMultiTimerInverse, CounterMultiTimer100Ns, and CounterMultiTimer100NsInverse with CounterMultiBase,

AverageTimer32 and AverageCount64 with AverageBase

The program can get the counter, and write to it using methods like Increment()

Sample code on create performance counter:

            **CounterCreationData** [] counters = new CounterCreationData[] {

                new CounterCreationData(counterName:&quot;# of images processed&quot;,

                counterHelp:&quot;number of images resized&quot;,

                counterType:PerformanceCounterType.NumberOfItems64),

                new CounterCreationData(counterName: &quot;# images processed per second&quot;,

                counterHelp:&quot;number of images processed per second&quot;,

                counterType:PerformanceCounterType.RateOfCountsPerSecond32)

            };

            **CounterCreationDataCollection** counterCollection = new CounterCreationDataCollection(counters);

            **PerformanceCounterCategory.Create** (categoryName:categoryName,

                categoryHelp:&quot;Image processing information&quot;,

                categoryType: **PerformanceCounterCategoryType.SingleInstance** ,

counterData: **counterCollection** );

Sample code for using the counter:

        static PerformanceCounter TotalImageCounter;

        static PerformanceCounter ImagesPerSecondCounter;

        static CreationResult SetupPerformanceCounters()

        {

            string categoryName = &quot;Image Processing&quot;;

            if (PerformanceCounterCategory.Exists(categoryName))

            {

                TotalImageCounter = new PerformanceCounter(categoryName:categoryName,

                    counterName:&quot;# of images processed&quot;,

                    readOnly:false);

                ImagesPerSecondCounter = new PerformanceCounter(categoryName:categoryName,

                    counterName: &quot;# images processed per second&quot;,

                    readOnly:false);

                return CreationResult.LoadedCounters;

}



TotalImageCounter.**Increment()**;

ImagesPerSecondCounter.**Increment()**;

- write to the event log

Windows Event Log is used by system components and application to log the operations.

EventLog class in System.Diagnostics namespace can be used to add or read events similar to performance counter

A log has to be created before it can be written

Sample code for create event log:

**EventLog.CreateEventSource** (source: categoryName,

logName: categoryName + &quot; log&quot;);

Sample code for reading event log:

static EventLog imageEventLog;

string categoryName = &quot;Image Processing&quot;;

imageEventLog = new **EventLog** ();

imageEventLog. **Source** = categoryName;

Sample code for writing event log:

imageEventLog. **WriteEntry** (&quot;Image processing started&quot;);

imageEventLog.WriteEntry(&quot;Image processing ended&quot;);

multiple overload options:

a common type is: WriteEntry(String, EventLogEntryType, Int32)

ref: https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.eventlog.writeentry?view=netframework-4.8

Reading from the event log

If the program try to read a log has not been created, program will print a warning message and end

Sample code for reading log:

EventLog imageEventLog = new **EventLog** ();

imageEventLog.Source = categoryName;

foreach( **EventLogEntry** entry in imageEventLog. **Entries** )

{

          Console.WriteLine(&quot;Source: {0} Type: {1} Time: {2} Message: {3}&quot;,

              entry. **Source** , entry. **EntryType** , entry. **TimeWritten** , entry. **Message** );

}

Binding to log events

Program can bind to an event log and receive event notifications when the log has new info

Can use this as monitor of system or create a dashboard displaying app activity

Program bind to a handler, like event handler and delegate

Sample code:

string categoryName = &quot;Image Processing&quot;;

EventLog imageEventLog = new EventLog();

imageEventLog.Source = categoryName;

imageEventLog. **EntryWritten** += **ImageEventLog\_EntryWritten** ;

imageEventLog. **EnableRaisingEvents** = true;

private static void **ImageEventLog\_EntryWritten** (object sender, EntryWrittenEventArgs e)

{

            Console.WriteLine(e.Entry.Message);

}

## Implement Data Access

Program use variable to store values as it runs, but it also needs a way to store values when its not running into persistence data storage.

This section talks about data storage and access abilities

Consider how c$ program can store and manipulate data

Use file storage to store data and how to manage the storage

Some database store and retrieve technique

The use of LINQ

Serialization

Data collection facilities offered by the .NET framework

# Perform I/O operations

Fundamental input/output (I/O) operation

How files are managed by OS

How .NET Framework libraries allow programs to store and load data

Async I/O helps to keep app responsive while operating on large amount of data which is slow

- Read and write files and streams

Stream:

a sequence of data elements made available over time

can be thought of as items on a conveyor belt being processed one at a time rather than in large batches, e.g. array

Have Potentially unlimited data, cannot operate as a whole

In C#, it&#39;s a software object represents a stream of data, **Byte as data unit**

standard set of methods for:

reading,

writing, and

positioning (seeking): set the position of &quot;file pointer&quot; for read/write

with some secondary methods such as close/flush, timeouts

backing store:

data source

the endpoint that makes input and output useful, a storage medium, such as a disk or memory, or, a network connection

i.e. A source from which bytes can be sequentially read, and

A destination to which bytes can be sequentially written

&quot;backing store&quot; stream:

Each different backing store implements its own stream as an implementation of the Stream class

Example:

FileStream

IsolatedStorageStream

MemoryStream

NetworkSteam

Each stream type need different initializing value, e.g. FileStream needs file path, MemoryStream needs buffer in memory

Ref: https://docs.microsoft.com/en-us/dotnet/standard/io/composing-streams

&quot;Decorator&quot; Stream

A stream implementing the &quot;decorator pattern&quot;

Add functions while keeping the same interface

Take in a stream, **manipulate it** , output a stream

Extend the stream ability while not bulking up the backing store stream

Can be chained

Example:

DeflateStream

GZipStream

CryptoStream

BufferedStream

Ref: C# in a nutshell

Both &quot;backing store&quot; and &quot;Decorator&quot; Stream types operate on byte exclusively

Stream &quot;adaptor&quot; implements the &quot;adaptor pattern&quot; i.e. wrapper, translator

Higher level data are in text (string type) or XML format instead of byte

Adapters bridge the &quot;string to byte gap&quot; by wrapping a stream in a class with specialized methods typed to a particular format e.g. text, XML

For example

a text reader exposes a ReadLine method;

an XML writer exposes a WriteAttributes method.

Text: StreanReader / StreamWriter

Int, float, string…: BinaryReader / BinaryWriter

XML data: XMLReader / XML Writer

To compose a chain, you simply pass one object into another&#39;s constructor.
[pic to be included]

Ref: C# in a nutshell

Use FileStream

Stream (of byte) connects to a file (backing store)

The stream object instance convert calls into the stream, into commands for the filesystem

Filesystem provide the interface to the physical device doing data storage

FileStream(FilePath, FileMode, FileAccess)

Example:

            // Writing to a file – write a block of byte to file

            FileStream outputStream = new FileStream(&quot;OutputText.txt&quot;, FileMode.OpenOrCreate, FileAccess.Write);

            string outputMessageString = &quot;Hello world&quot;;

            byte[] outputMessageBytes = Encoding.UTF8.GetBytes(outputMessageString);

            outputStream.Write(outputMessageBytes, 0, outputMessageBytes.Length);

            outputStream.Close();

            // Reading to a file – read a block of byte to file

            FileStream inputStream = new FileStream(&quot;OutputText.txt&quot;, FileMode.Open, FileAccess.Read);

            long fileLength = inputStream.Length;

            byte[] readBytes = new byte[fileLength];

            inputStream.Read(readBytes, 0, (int)fileLength);

            inputStream.Close();

string readString = Encoding.UTF8.GetString(readBytes);

Control file use with FileMode and FileAccess

FileMode – a set of enumeration, to be used in FileStream constructor, to indicate how the file is to be opened

List of 6 file modes:

FileMode.Append – use with write access, create new file, or move seek position to the end for existing file

FileMode.Create – create file for writing, overwrite existing file, existing content will be lost

FileMode.CreateNew – create for writing, throw exception if exist

FileMode.Open – open exist, throw exception if not exist

FileMode.OpenOrCreate – open for read or write, if not exist, new file is created

FileMode.Trucate – open existing file for write or remove existing content

FileAccess – a set of enumeration, to be used in FileStream constructor, to indidcate how the file is to be used

List of 3 file access modes:

FileAccess.Read – open for read

FileAccess.ReadWrite – open for read and write

FileAccess.Write – open for write

If FileMode and FileAccess are not compactable, FileStream creation will fail with exception

Convert text to binary data with Unicode

Encoding class is in System.Text namespace

Has Unicode properties, like UTF8, UTF16, UTF32

Unicode is a mapping of character symbols to numeric values

e.g. UTF8 map Unicode characters onto 8-bit values that can be stored in arrays of bytes

GetByte – take string and returns bytes

Encoding.UTF8.GetBytes(outputMessageString)

GetString – take buffer full of bytes and return string

Encoding.UTF8.GetString(readBytes)

IDispose and FileStream objects

FileStream class implements the IDipose interface

Use &quot;using&quot; construction to make sure the dispose method in IDispose interface would be called, and hence, close the file

Example:

using (FileStream outputStream = new FileStream(&quot;OutputText.txt&quot;, FileMode.OpenOrCreate,

FileAccess.Write))

{

    string outputMessageString = &quot;Hello world&quot;;

    byte[] outputMessageBytes = Encoding.UTF8.GetBytes(outputMessageString);

    outputStream.Write(outputMessageBytes, 0, outputMessageBytes.Length);

}

Work with text files

C# provide &quot;stream adapter&quot; kind of class for easier working with text

e.g. StreamWriter, StreamReader class which extend from the abstract class of the TextWriter, TextReader

as a wrapper / translator, Provides many specialized methods to work in target data type, instead of working on byte

example:

using (StreamWriter writeStream = new StreamWriter(&quot;OutputText.txt&quot;))

{

    writeStream. **Write** (&quot;Hello world&quot;);

}

using (StreamReader readStream = new StreamReader(&quot;OutputText.txt&quot;))

{

    string readString = readStream. **ReadToEnd** ();

    Console.WriteLine(&quot;Text read: {0}&quot;, readSTring);

}

Chain streams together

Pass the stream object to another stream constructor

Work for backing store stream and decorator stream as they have same interface

Example:

using (FileStream  **readFile**  = new FileStream(&quot;CompText.zip&quot;, FileMode.Open,

 FileAccess.Read))

{

    using (GZipStream  **readFileZip**  = new GZipStream( **readFile** ,

    CompressionMode.Decompress))

    {

        using (StreamReader readFileText = new StreamReader( **readFileZip** ))

        {

            string message = readFileText.ReadToEnd();

            Console.WriteLine(&quot;Read text: {0}&quot;, message);

        }

    }

}

Use the File Class

File Class is a &quot;helper&quot; class

Provide a set of static methods for:

Append text to file

e.g. File.AppendAllText(path: &quot;TextFile.txt&quot;,   contents: &quot; - This goes on the end&quot;);

Copy a file

e.g. File.Copy(sourceFileName: &quot;TextFile.txt&quot;,  destFileName: &quot;CopyTextFile.txt&quot;);

Create a file

Delete a file

Move a file

Open a file

Read a file

Manage file security

Handle stream exceptions

Exception case are those having no meaning to continue execute the program thread

Example are: try to open a file that does not exist,, write to a full storage device, thread access to file being used by other thread

Wrap with try catch

Example:

try

{

    string contents = File.ReadAllText(path: &quot;Testfile.txt&quot;);

    Console.WriteLine(contents);

}

catch(FileNotFoundException notFoundEx)

{

    // File not found

    Console.WriteLine(notFoundEx.Message);&quot;

}

catch(Exception ex)

{

    // Any other exception

    Console.WriteLine(ex.Message);

}

File storage

Storage device, e.g. disk drive, usb disk, can be divided into partitions

Partition represents an area on the storage device that can be used to store data

Partition on a storage device is exposed as a drive

In Windows OS, a drive is represented by a drive letter, and used as the root of an absolute path to a file

Disk Management app in Windows OS allow admin to re-assign drive letter, combine physical drives into a single logical drive, attach virtual hard drives create from drive images

Each partition is formatted using a particular filling system

DriveInfo class in System.IO namespace can be used to obtain info about the drives attached

Example:

DriveInfo[] drives = DriveInfo.GetDrives();

foreach (DriveInfo drive in drives)

{

    Console.Write(&quot;Name:{0} &quot;, drive.Name);

    if (drive.IsReady)

    {

        Console.Write(&quot;  Type:{0}&quot;, drive.DriveType);

        Console.Write(&quot;  Format:{0}&quot;, drive.DriveFormat);

        Console.Write(&quot;  Free space:{0}&quot;, drive.TotalFreeSpace);

    }

    else

    {

        Console.Write(&quot;  Drive not ready&quot;);

    }

    Console.WriteLine();

}

Use FileInfo

File system maintains info about each file it stores

Example info:

File name

Permission

Create date

Modify date

Physical location of the file on the storage device

List of attributes, represent as a single value with bits

To operate on the list of attributes, logical operators e.g. &amp;, |, ~, can be used (not the same as bool operators!)

FileAttributes.Archive – file has not been backed up yet

FileAttributes.Compressed – file is compressed

FileAttributes.Directory – file is a directory

FileAttributes.Hidden – file not appear in ordinary directory listing

FileAttributes.Normal – no special attribute assigned

FileAttributes.ReadOnly – cant be written

FileAttributes.System – part of OS

FileAtrributes.Temporary – temp file that will not be required

Sample code

string filePath = &quot;TextFile.txt&quot;;

File.WriteAllText(path: filePath, contents: &quot;This text goes in the file&quot;);

FileInfo info = new FileInfo(filePath);

Console.WriteLine(&quot;Name: {0}&quot;, info.Name);

Console.WriteLine(&quot;Full Path: {0}&quot;, info.FullName);

Console.WriteLine(&quot;Last Access: {0}&quot;, info.LastAccessTime);

Console.WriteLine(&quot;Length: {0}&quot;, info.Length);

Console.WriteLine(&quot;Attributes: {0}&quot;, info.Attributes);

Console.WriteLine(&quot;Make the file read only&quot;);

info.Attributes  **|=**  FileAttributes.ReadOnly;

Console.WriteLine(&quot;Attributes: {0}&quot;, info.Attributes);

Console.WriteLine(&quot;Remove the read only attribute&quot;);

info.Attributes  **&amp;= ~** FileAttributes.ReadOnly;

File class is useful for single file, FileInfo class is useful for multiple files

Use the Directory and DirectoryInfo classes

Directory, aka folder: File contains collections of file information items

Directory can contain info about directories to create a nest directories tree structure

Similar to file, has two classes Directory class contains a set of static methods for create and manipulate directory, and DirectoryInfo for describe content

Sample code:

Directory.CreateDirectory(&quot;TestDir&quot;);

if (Directory.Exists(&quot;TestDir&quot;))

    Console.WriteLine(&quot;Directory created successfully&quot;);

Directory.Delete(&quot;TestDir&quot;);



DirectoryInfo localDir = new DirectoryInfo(&quot;TestDir&quot;);

localDir.Create();

if(localDir.Exists)

    Console.WriteLine(&quot;Directory created successfully&quot;);

localDir.Delete();

Console.WriteLine(&quot;Directory deleted successfully&quot;);

File and paths

Path:

define location of a file on a storage device

can be relative or absolute

Relative path:

location of a file relative to the folder in which the program is running

&quot;.&quot; Current directory

&quot;..&quot; parent directory, i.e. directory above the current one

@ character at the start of string: mark as verbatim string

verbatim definition: 1. using exactly the same words as were originally used

Absolute path:

Include drive letter and all sub-directories in path

Path class provides a set of static methods

Sample:

string fullName = @&quot;c:\users\rob\Documents\test.txt&quot;;

string dirName = Path.GetDirectoryName(fullName);

string fileName = Path.GetFileName(fullName);

string fileExtension = Path.GetExtension(fullName);

string lisName = Path.ChangeExtension(fullName, &quot;.lis&quot;);

string newTest = Path.Combine(dirName, &quot;newtest.txt&quot;);

Searching for file

DirectoryInfo object has a GetFiles() method, one of the overload can be a search string using regex format

Sample code, use recursion for sub-directory:

        static void FindFiles(DirectoryInfo dir, string searchPattern)

        {

            foreach (DirectoryInfo directory in dir.GetDirectories())

            {

                FindFiles(directory, searchPattern);

            }

            FileInfo[] matchingFiles = dir.GetFiles(searchPattern);

            foreach (FileInfo fileInfo in matchingFiles)

            {

                Console.WriteLine(fileInfo.FullName);

            }

}

- read and write from the network by using classes in the System.Net namespace

.NET framework library provide class to create &quot;network sockets&quot; to communicate over network using TCP or UDP over TCP/IP

HTTP is based on TCP/IP (can work on UDP, but uncommon)

ASP app, server can generate content dynamically

HTTP servers serve HTML page to request clients

HTML page is human-readable webpage

Nowadays, the request return can be XML / JSON as well

REST (REpresentational State Transfer)-ful style protocol is using HTTP operations of GET, POST, PUT, DELETE to perform function in client-server app

Basic operation is sending HTTP request to server as &quot;web request&quot;

WebRequest

Provide more fine grain control, more complicated

URL: universal resource identifier, specifies the resource to be used

WebRequest is the basic abstract class for HttpWebRequest, FTP, File…

If the URL start with &quot;http/https&quot; is passed into Create() method of WebRequest class, a HttpWebRequest object will be returned

In HttpWebRequest object:

GetResponse() return WebResponse object describing the server response

GetResponseStream() return a stream object can be used with StreamReader to get the HTML webpage

Sample code:

            WebRequest webRequest = WebRequest.Create(&quot;http://www.microsoft.com&quot;);

            WebResponse webResponse = webRequest.GetResponse();

            using (StreamReader responseReader = new StreamReader(webResponse.GetResponseStream()))

            {

                string siteText = responseReader.ReadToEnd();

                Console.WriteLine(siteText);

}

WebClient

Simple to use compare to WebRequest, provide less control

Return text string result directly

Sample code:

WebClient client = new WebClient();

string siteText = client.DownloadString(&quot;http://www.microsoft.com&quot;);

Console.WriteLine(siteText);

async Task\&lt;string\&gt; readWebpage(string uri)

{

    WebClient client = new WebClient();

    return await client.DownloadStringTaskAsync(uri);

}

One way to send form encoded data is using method of UploadValuesAsync(Uri, NameValueCollection)

Ref: [https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.uploadvaluesasync?view=netframework-4.8#System\_Net\_WebClient\_UploadValuesAsync\_System\_Uri\_System\_Collections\_Specialized\_NameValueCollection\_](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.uploadvaluesasync?view=netframework-4.8#System_Net_WebClient_UploadValuesAsync_System_Uri_System_Collections_Specialized_NameValueCollection_)

HttpClient

Modern way of WebClient

Only provide async method

Sample:

async Task\&lt;string\&gt; readWebpage(string uri)

{

    HttpClient client = new HttpClient();

    return await client.GetStringAsync(uri);

}

Adapt RESTful style by providing GET, PUT, POST, DELETE methods

To send form encoded data, use PostAsync(Uri, HttpContent, CancellationToken)

HttpContent can be constructed using FormUrlEncodedContent Class which is a child class of HttpContent, that takes in a dictionary of name value pair

Ref: https://docs.microsoft.com/en-us/dotnet/api/system.net.http.httpclient.postasync?view=netframework-4.8

Exception handling

Network related is error probing

Wrap with try-catch

Sample:

try

{

    string webText = await readWebpage(PageUriTextBox.Text);

    ResultTextBlock.Text = webText;

}

catch (Exception ex)

{

    var dialog = new MessageDialog(ex.Message, &quot;Request failed&quot;);

    await dialog.ShowAsync();

}

- implement asynchronous I/O operations

Synchronous: user has to wait for the program to complete an action before it gives out further response; the irresponsiveness may lead to poor UX

File class has no async, have to use FileStream class instead

Handling exceptions in asynchronous methods

Async methods should return something, either a result or a Task, in order to catch the exception

Exceptions throw by Void type async methods can&#39;t be caught

ONLY event handler should return void

# Consume data

Data storage techniques that build on a file store

How a database engine expose info to app

How to use XML and JSON to impose structure on a file of stored data

How a program use Web services to expose data to other program over the network in a portable manner

- Retrieve data from a database

Database is subset of file

Use to store structured, related data

For the purpose of future query in a simple structured manner

i.e. in order to make query of the data easier, you need to store the data in a structured way in some specific format and rules

ADO / EF (activex data objects / entity framework):

Class-based design to express the data storage need of an app

SQL DB server can be local or remote

How to manage ASP app to use remote DB server

Data in a database

A Class contains data members, one of which is &quot;ID&quot;, can be &quot;translated&quot; to database table

Using Entity Framework toolchain

DB can be configurated to generate a unique ID for Each new entry into the table

A database table can be view as a collection of objects or a list of reference to objects

To access data in DB, program has to make a connection with it and send a command to request of info

Data in DB is structured, and more &quot;specialized&quot; than in file system stores as files which can be binary files or unstructured text

DataAdapters: read / write; all data in memory so higher usage, cursor back and fore, auto close connection

DataReaders: read only

Several type of adapter/reader for different kind of database

All based on DbDataReader and a DbDataAdapter object

Object Linking and Embedding (OLE) DB uses OleDbDataReader and OleDbDataAdapter

Mostly Use for DB not supporting SQL

DB support SQL use SqlDataReader and SqlDataAdapter

DB support ODBC, relational DB, use OdbcDataReader and OdbcDataAdapter

OLE uses ODBC to work with relational DB, and also support non-relational db

OLE can access all, while SQL is SQL only, Odbc is a lower layer

Use ADO .Net or OLE or SQL

Ref: [https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/dataadapters-and-datareaders](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/dataadapters-and-datareaders)

[https://stackoverflow.com/questions/1676753/sqldataadapter-vs-sqldatareader](https://stackoverflow.com/questions/1676753/sqldataadapter-vs-sqldatareader)

[https://stackoverflow.com/questions/103167/what-is-the-difference-between-ole-db-and-odbc-data-sources](https://stackoverflow.com/questions/103167/what-is-the-difference-between-ole-db-and-odbc-data-sources)



Read with SQL

Default DB by EF and .NET is relational type

Accept Structured Query Language (SQL) command

SQL is domain specific language as it is used solely for expressing command to instruct DB what to do

SQL query is &quot;declarative&quot;, describe what you need to get instead of Imperative, detailing each step of getting the data e.g. a for loop and use if conditions

Ref: [https://stackoverflow.com/questions/1619834/what-is-the-difference-between-declarative-and-procedural-programming-paradigms](https://stackoverflow.com/questions/1619834/what-is-the-difference-between-declarative-and-procedural-programming-paradigms)

Declarative is like mental chuck, one chunk can consist of many steps, and can take in some &quot;parameters&quot; as adjustment for different cases

\* character is a &quot;wildcard&quot; that matches all of the entries in the table

SQL command to read all of the elements in a table:

SELECT \* FROM db\_table

For program to send the command to DB, a connection with DB is needed

DbConnection class is abstract class

SqlConnection class inherent DbConnection class and represent the implementation of SQL DB connection

To connect to SQL DB, a connection string is given to the constructor of SqlConnection class

Info in the connection string: address of DB server, the port, username, password

Express as name-value pairs

Sample code for development, a connection string, SQL command, read from DB:

// You will need to edit this string to match your database file

string connectionString = &quot;Server=(localdb)\\mssqllocaldb;&quot; +

                &quot;Database=MusicTracksContext-e0f0cd0d-38fe-44a4-add2-359310ff8b5d;&quot; +

                &quot;Trusted\_Connection=True;MultipleActiveResultSets=true&quot;;

using (SqlConnection connection = new SqlConnection(connectionString))

{

        connection.Open();

        SqlCommand command = new SqlCommand(&quot;SELECT \* FROM MusicTrack&quot;, connection);

        SqlDataReader reader = command.ExecuteReader();

        while (reader.Read())

        {

            string id = reader[&quot;ID&quot;].ToString();

            string artist = reader[&quot;Artist&quot;].ToString();

            string title = reader[&quot;Title&quot;].ToString();

            Console.WriteLine(&quot;ID: {0} Artist: {1} Title: {2}&quot;, id, artist, title);

        }

}

DO NOT use the above connection string style, &quot;hard-wire&quot; for production or distribution of binary

Other people can obtain the connection string using decomplier and access the DB

And have to change the source code for using different DB

Connection string management in ASP.NET

DB connection string is stored in the &quot;appsettings.json&quot; file

&quot;appsettings.json&quot; file contains app setting info which describe the &quot;environments&quot; for development and production

Development settings, environment, dev db connection string, can be stored in &quot;appsettings.Development.json&quot; while production stores in &quot;appsettings.Production.json&quot;

&quot;Environment&quot; can mean two things, ASP application environment or environment variable maintained by OS

ASP application environment determines the settings for logging, tracing, debugging, and the DB connection string that will be used when the ASP app runs

environment variable maintained by OS can be use by processes running in it. Info include name of computer, file paths for searching programs



SQL query operation

&quot;SqlCommand&quot; instance can make a query of DB by taking 2 things:

SQL command e.g. &quot;SELECT \* FROM MusicTrack&quot;, and

DB connection object

Query is executed as a &quot;reader command&quot; and return a SqlReader instance

e.g. SqlDataReader reader = command.ExecuteReader();

SqlReader provides methods, e.g. Read(), to move through the results returned by the query

Read() method return False when there are no more result

Individual items in the element are accessed using the name

Sample code for reading and moving through the query result:

while (reader.Read())

{

    string artist = reader[&quot;Artist&quot;].ToString();

    string title = reader[&quot;Title&quot;].ToString();

    Console.WriteLine(&quot;Artist: {0} Title: {0}&quot;, artist, title);

}



- update data in a database

SQL &quot;UPDATE&quot; command to update contents of &quot;an entry&quot; in DB

Use &quot;WHERE&quot; to pick out the entry need to be updated

If more than one entry is return, then all returned will be updated

Sample code:

using (SqlConnection connection = new SqlConnection(connectionString))

{

    connection.Open();

    dumpDatabase(connection);

    SqlCommand command = new SqlCommand(

       &quot;UPDATE MusicTrack SET Artist=&#39;Robert Miles&#39; WHERE ID=&#39;1&#39;&quot;, connection);

    int result = command.ExecuteNonQuery();

    Console.WriteLine(&quot;Number of entries updated: {0}&quot;, result);

    dumpDatabase(connection);

}

SQL injection and database commands

Avoid dangerous code as below, especially for user input:

&quot;UPDATE MusicTrack SET Artist=&#39;&quot; + newArtist +

&quot;&#39; WHERE Title=&#39;&quot; + searchTitle + &quot;&#39;&quot;;

Malicious code can easily plant into the SQL command statement

e.g. searchTitie = &quot;Fred&#39;); DELETE FROM Music&quot;

NEVER construct SQL command DIRECTLY from user input

Use &quot;parameterized&quot; SQL statement

Construct SqlCommand object with &quot;placeholders&quot;

Use @para\_name as placeholder inside the SqlCommand

Then fill the value of the placeholder using Parameters.AddWithValue(@para\_name, value\_var) method

Sample code:

string SqlCommandText = &quot;UPDATE MusicTrack SET Artist=@artist WHERE Title=@searchTitle&quot;;

SqlCommand command = new SqlCommand(SqlCommandText, connection);

command.Parameters.AddWithValue(&quot;@artist&quot;, newArtist);

command.Parameters.AddWithValue(&quot;@searchTitle&quot;, searchTitle);



Asynchronous database access

Async version for executing SQL command

ExecuteReader() -\&gt; ExecuteReader**Async()**

Read() -\&gt; Read**Async()**

Especially useful for windowed UI app to keep the UI responsive

Using SQL in ASP applications

No SQL statement, as program use the &quot;context&quot; object

For dynamic html with C#, the file extension is .cshtml, called Razon

The c# elements in the .cshtml are started by an @ character

Sample code:

**@foreach (var item in Model)** {

        \&lt;tr\&gt;

            \&lt;td\&gt;

**                @Html.DisplayFor(modelItem =\&gt; item.Artist)**

            \&lt;/td\&gt;

            \&lt;td\&gt;

**                @Html.DisplayFor(modelItem =\&gt; item.Title)**

            \&lt;/td\&gt;

            \&lt;td\&gt;

**                @Html.DisplayFor(modelItem =\&gt; item.Length)**

            \&lt;/td\&gt;

            \&lt;td\&gt;

                \&lt;a asp-action=&quot;Edit&quot; asp-route-id=&quot; **@item.ID**&quot;\&gt;Edit\&lt;/a\&gt; |

                \&lt;a asp-action=&quot;Details&quot; asp-route-id=&quot; **@item.ID**&quot;\&gt;Details\&lt;/a\&gt; |

                \&lt;a asp-action=&quot;Delete&quot; asp-route-id=&quot; **@item.ID**&quot;\&gt;Delete\&lt;/a\&gt;

            \&lt;/td\&gt;

        \&lt;/tr\&gt;

}&quot;

- consume JSON and XML data

Consumer JSON data

Construct a class representing the data

Pass the class into the generic type placeholder of JsonConvert.DeserializeObject\&lt; T\&gt;

Sample code:

        public class ImageOfDay

        {

            public string date { get; set; }

            public string explanation { get; set; }

            public string hdurl { get; set; }

            public string media\_type { get; set; }

            public string service\_version { get; set; }

            public string title { get; set; }

            public string url { get; set; }

        }

        async Task\&lt;ImageOfDay\&gt; GetImageOfDay(string imageURL)

        {

            string NASAJson = await readWebpage(imageURL);

            ImageOfDay result = JsonConvert.DeserializeObject\&lt; ImageOfDay\&gt;(NASAJson);

            return result;

}

Consume XML data

XML is bulkier, JSON is taking over

XMLTextReader class

Work through a stream of text and decode each XML element

Use StringReader class to create a string stream and pass it to XMLTextReader

Iterate the enumerate reader, each element get the property of:

reader.NodeType – element, whitespace, text

reader.Name – don&#39;t have for text, whitespace

reader.Value – only text has it

XML documents

XmlDocument instance creates a XML DOM

Much more efficient to work with

A hierarchy of items with a rootElement object on top

Access element using index of string

Sample code, document also loaded as string:

            XmlDocument doc = new XmlDocument();

            doc.LoadXml(XML\_String);

            System.Xml.XmlElement rootElement = doc.DocumentElement;

            // make sure it is the right element

            if (rootElement.Name != &quot;MusicTrack&quot;)

            {

                Console.WriteLine(&quot;Not a music track&quot;);

            }

            else

            {

                string artist = rootElement[&quot;Artist&quot;].FirstChild.Value;

                Console.WriteLine(&quot;&quot;, artist);

                string title = rootElement[&quot;Title&quot;].FirstChild.Value;

                Console.WriteLine(&quot;Artist:{0} Title:{1}&quot;, artist, title);

}

- retrieve data by using web services

Windows Communication Foundation (WCF) is a modern way provided by Microsoft for programs communicate via internet

&quot;Proxy object&quot; is created for interaction with server e.g. call methods on server to retrieve data

Detail of how message construct and transfer are taken care by the WCF framework

Only need to create server methods and calls from the client

Note: WCF has phased out in the future .NET framework, ASP.NET Web API and gRPC are replacement tech

Create a web service

Start by creating an interface

To denote the interface as web service and the method to be called

Attach [ServiceContract] attribute to the interface

Attach [OperationContract] attribute to the method signature

Implement the interface

Update the &quot;App.config&quot; file for defining service name, base address and endpoint

Joke of the Day Client

Use Add service reference dialog to type in the service network address

Visual studio will scan and read what is available to use

A &quot;proxy object&quot; would be created at the client side to be used

# Query and manipulate data and objects by using LINQ

Language Integrated Query (LINQ)

Declarative way to let programmers work with data source

Query any data collection that implement Enumerable\&lt;T\&gt; interface

e.g. array list, built-in collections, XML DOM, SQL DB

Basic unit of data in LINQ are &quot;sequence&quot; and &quot;elements&quot;

Sequence: object implement Enumerable\&lt;T\&gt; interface

Element: each item in the sequence

Query operator is a method that transform a sequence

Work like map(), filter(), reduce()

Take in a sequence, map/filter, output a sequence

Or can take in a sequence, output an aggregated value

Or can take in some rarer use case, take in some para, output a sequence

Query over local sequence are called local query, or LINQ-to-object query

e.g. string[] names = { &quot;Apple&quot;, &quot;Belief&quot;, &quot;Cydia&quot; };

Query over remote data source, which also implement &quot;IQuerable\&lt;T\&gt;&quot; interface, are called interpreted query, aka LINQ-to-db, this type of query emits &quot;expression tree&quot; that are interpreted at runtime at DB side

Around 40 standard query operators are implemented in System.Linq

- Query data by using operators, including projection, join, group, take, skip, aggregate

Two styles for making query: Fluent syntax, query expressions

Fluent syntax aka fluent interface

method calls will return the pass in object type

great for &quot;method chaining&quot; i.e. invoking multiple method calls without the need of variable for intermediate results

this is implemented based on extension method



- create method-based LINQ queries

- query data by using query comprehension syntax

- select data by using anonymous types

- force execution of a query

- read, filter, create, and modify data structures by using LINQ to XML

# Serialize and deserialize data

Serialization: process of converting an object or a graph of objects into a linear sequence of bytes

for either storage or

transmission to another location

Deserialization is the process of taking in stored information and recreating objects from it

- Serialize and deserialize data by using binary serialization, custom serialization, XML Serializer, JSON Serializer, and Data Contract Serializer

3 engines:

Binary serializer

XML serializer

Data contract serializer (DCS)

First two are for old services, remoting and Web Service

DCS is the modern one, created with Windows Communication Foundation (WCF) which replace both remoting and Web Service

.NET Core and future .NET make WCF obsolete

Use web API and gRPC instead of WCF

Ref:

https://devblogs.microsoft.com/dotnet/net-core-is-the-future-of-net/

https://devblogs.microsoft.com/dotnet/supporting-the-community-with-wf-and-wcf-oss-projects/

DCS is here to stay as it can be used for other services

DataContractSerializer vs XmlSerializer:

DCS: opt-in, by marking

[DataContract] attribute for type / class declartion

[DataMember] attribute for field/property

use for WCF

**Net** DataContractSerializer vs DataContractSerializer:

**Net** DataContractSerializer includes CLR type information in the serialized XML, whereas the DataContractSerializer does not

NetDataContractSerializer can be used only if both the serializing and deserializing ends share the same CLR types

Ref:

[https://docs.microsoft.com/en-us/dotnet/api/system.runtime.serialization.netdatacontractserializer?view=netframework-4.8](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.serialization.netdatacontractserializer?view=netframework-4.8)



serializer needs to work with a &quot;formatter&quot; hand in hand

these two are decoupled for cleaner code and flexibility

the two combined are ready to work with stream

serialization: serializer + formatter + stream

common method calls to use serializer:

new DataContractSerializer (typeof (class\_name))

serialize:

ds.WriteObject (formatterRef, objectRef);

deserialize:

(classCast) ds.ReadObject (formatterRef, objectRef);

common method calls to use formatter:

using (XmlDictionaryWriter w = XmlDictionaryWriter.CreateBinaryWriter (streamRef))

using (XmlDictionaryReader r = XmlDictionaryReader.CreateBinaryReader (streamRef,

                                   XmlDictionaryReaderQuotas.Max))

combine them:

Person p = new Person { Name = &quot;Stacey&quot;, Age = 30 };

var ds = new DataContractSerializer (typeof (Person));

var s = new MemoryStream();

using (XmlDictionaryWriter w = XmlDictionaryWriter.CreateBinaryWriter (s))

      ds.WriteObject (w, p);

var s2 = new MemoryStream (s.ToArray());

Person p2;

using (XmlDictionaryReader r = XmlDictionaryReader.CreateBinaryReader (s2,

                                   XmlDictionaryReaderQuotas.Max))

      p2 = (Person) ds.ReadObject (r);

# Store data in and retrieve data from collections
[pic to be included]

Ref:

[https://www.c-sharpcorner.com/UploadFile/57a357/overview-of-collection-array-list-hash-table-sorted-list/](https://www.c-sharpcorner.com/UploadFile/57a357/overview-of-collection-array-list-hash-table-sorted-list/)

- Store and retrieve data by using dictionaries, arrays, lists, sets, and queues

- choose a collection type

- initialize a collection

- add and remove items from a collection; use typed vs. non-typed collections

- implement custom collections

- implement collection interfaces