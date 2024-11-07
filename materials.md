# Материалы для изучения

**Легенда**

🎥 - Видео  
❗️ - Сложный материал

Если в разделе есть видео, сначала прочитай текстовые материалы: после просмотра желание читать что-либо может пропасть.

Ссылки на материалы microsoft могут являться базовым вектором для изучения, так как зачастую ссылаются на корневую страницу разделов документации. Не пренебрегай возможностью изучить эти разделы целиком.

Разделы без ссылок на материалы полразумевают, что для старта вам нужно знать эти темы из корневого раздела. Основной материал распологается под корневым разделом.  
Например, в разделе [Background Jobs](#background-jobs) вам нужно знать базовые концепты из ссылки в разделе и понимать назначение интерфейсов `IHostedService` и `BackgroundService`.

# Platform & Language

## Types

### Value types

[Value types (C# reference)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/value-types)

### Reference types

[Reference types (C# reference)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/reference-types)  
[Типы значений и ссылочные типы](https://metanit.com/sharp/tutorial/2.16.php)

### Reflection

[Рефлексия](https://metanit.com/sharp/tutorial/14.1.php)  
❗️[.NET Reflection. Упрощаем работу и используем в тестировании](https://habr.com/ru/articles/716104/)

## Memory management

[CLRium #5: Курс "Garbage Collector". 1/7: Основы строения памяти](https://www.youtube.com/watch?v=DVnmGW6964o&list=PLBwwJL9lzKMY3At-QQQijfiHdsYnOQ7vY) - Плейлист о строении памяти в .net.

### Managed code

[What is "managed code"?](https://learn.microsoft.com/en-us/dotnet/standard/managed-code)

### Garbage Collector

[Fundamentals of garbage collection](https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/fundamentals)

### Finalizers & Disposable

[Finalizers (C# Programming Guide)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/finalizers)  
[Финализируемые объекты](https://metanit.com/sharp/tutorial/8.2.php)

## Async programming

[CPU vs IO bound](https://stackoverflow.com/a/868577/14095048)  
[Microsoft docs: The managed thread pool](https://learn.microsoft.com/en-us/dotnet/standard/threading/the-managed-thread-pool)  
[Microsoft docs: CancellationToken Struct](https://learn.microsoft.com/en-us/dotnet/api/system.threading.cancellationtoken?view=net-8.0)  

### Task

[Task-based asynchronous programming](https://learn.microsoft.com/en-us/dotnet/standard/parallel-programming/task-based-asynchronous-programming)  
[Microsoft docs: System.Threading.Tasks.Task class](https://learn.microsoft.com/en-us/dotnet/fundamentals/runtime-libraries/system-threading-tasks-task)  
🎥❗️ [CLRium #6: Tasks](https://youtu.be/HiKzuWbRgPM?si=aBcgmxYIUj6nLRYp)

### async/await

[Asynchronous programming with async and await](https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/)  
[Async/await в C#: концепция, внутреннее устройство, полезные приемы](https://habr.com/ru/articles/470830/)  
❗️ [Как на самом деле работает Async/Await в C# (Часть 1)](https://habr.com/ru/articles/727850/)  
🎥❗️ [CLRium #6: async/await. Машина состояний](https://youtu.be/_suxE9frTFA?si=ORA-S7AmEyEL4BqJ)

### Как отвечать на вопросы на собеседовании

#### Для чего нужно асинхронное программирование?

Асинхронное программирование позволяет эффективно использовать потоки при работе с IO bound операциями: запись на диск, взаимодействие по сети, обращение к БД и т. д. IO bound операции в отличии от CPU bound зависят от "производительности" (скорость чтения с диска, скорость взаимодействия по сети и т. д.) внешнего ресурса, поэтому нельзя контроллировать время, которое понадобится на их выполнение. 
При обращении к таким ресурсам в синхронном режиме вызывающий поток ожидает возвращения управления, а значит простаивает. Асинхронные вызовы позволяют вернуть ожидающий поток в пул потоков, чтобы он мог быть использован для выполнения полезной работы.

#### Для чего нужны ключевые слова async/await?

Ключевое слово async нужно чтобы пометить метод как асинхронный. 
Ключевое слово await нужно чтобы ожидать выполнения асинхронного метода в асинхронном режиме. Может быть использовано только внутри метода помеченного async.

#### Во что разворачивается async?

При сборке кода компилятор развернет асинхронный метод в конечный автомат, который будет содержать количество состояний равное количеству вызовов await внутри метода. Посмотрим, во что развернется следующий код:
```csharp
public class Program  
{  
    public static async Task Main(params string[] args)   
    {  
        Console.WriteLine("Hello");  
  
        await Task.Delay(1000);
        
        Console.WriteLine("World");  
        
        await Task.Delay(1000);
        
        Console.WriteLine("I'm Jerry");  
    }    
}
```

На C#
```csharp
using System;
using System.Diagnostics;
using System.Reflection;
using System.Runtime.CompilerServices;
using System.Security;
using System.Security.Permissions;
using System.Threading.Tasks;

[assembly: CompilationRelaxations(8)]
[assembly: RuntimeCompatibility(WrapNonExceptionThrows = true)]
[assembly: Debuggable(DebuggableAttribute.DebuggingModes.Default | DebuggableAttribute.DebuggingModes.IgnoreSymbolStoreSequencePoints | DebuggableAttribute.DebuggingModes.EnableEditAndContinue | DebuggableAttribute.DebuggingModes.DisableOptimizations)]
[assembly: SecurityPermission(SecurityAction.RequestMinimum, SkipVerification = true)]
[assembly: AssemblyVersion("0.0.0.0")]
[module: UnverifiableCode]
[module: RefSafetyRules(11)]

public class Program
{
    [CompilerGenerated]
    private sealed class <Main>d__0 : IAsyncStateMachine
    {
        public int <>1__state;

        public AsyncTaskMethodBuilder <>t__builder;

        [Nullable(new byte[] { 0, 1 })]
        public string[] args;

        private TaskAwaiter <>u__1;

        private void MoveNext()
        {
            int num = <>1__state;
            try
            {
                TaskAwaiter awaiter;
                TaskAwaiter awaiter2;
                if (num != 0)
                {
                    if (num == 1)
                    {
                        awaiter = <>u__1;
                        <>u__1 = default(TaskAwaiter);
                        num = (<>1__state = -1);
                        goto IL_00ed;
                    }
                    Console.WriteLine("Hello");
                    awaiter2 = Task.Delay(1000).GetAwaiter();
                    if (!awaiter2.IsCompleted)
                    {
                        num = (<>1__state = 0);
                        <>u__1 = awaiter2;
                        <Main>d__0 stateMachine = this;
                        <>t__builder.AwaitUnsafeOnCompleted(ref awaiter2, ref stateMachine);
                        return;
                    }
                }
                else
                {
                    awaiter2 = <>u__1;
                    <>u__1 = default(TaskAwaiter);
                    num = (<>1__state = -1);
                }
                awaiter2.GetResult();
                Console.WriteLine("World");
                awaiter = Task.Delay(1000).GetAwaiter();
                if (!awaiter.IsCompleted)
                {
                    num = (<>1__state = 1);
                    <>u__1 = awaiter;
                    <Main>d__0 stateMachine = this;
                    <>t__builder.AwaitUnsafeOnCompleted(ref awaiter, ref stateMachine);
                    return;
                }
                goto IL_00ed;
                IL_00ed:
                awaiter.GetResult();
                Console.WriteLine("I'm Jerry");
            }
            catch (Exception exception)
            {
                <>1__state = -2;
                <>t__builder.SetException(exception);
                return;
            }
            <>1__state = -2;
            <>t__builder.SetResult();
        }

        void IAsyncStateMachine.MoveNext()
        {
            //ILSpy generated this explicit interface implementation from .override directive in MoveNext
            this.MoveNext();
        }

        [DebuggerHidden]
        private void SetStateMachine([Nullable(1)] IAsyncStateMachine stateMachine)
        {
        }

        void IAsyncStateMachine.SetStateMachine([Nullable(1)] IAsyncStateMachine stateMachine)
        {
            //ILSpy generated this explicit interface implementation from .override directive in SetStateMachine
            this.SetStateMachine(stateMachine);
        }
    }

    [NullableContext(1)]
    [AsyncStateMachine(typeof(<Main>d__0))]
    [DebuggerStepThrough]
    public static Task Main(params string[] args)
    {
        <Main>d__0 stateMachine = new <Main>d__0();
        stateMachine.<>t__builder = AsyncTaskMethodBuilder.Create();
        stateMachine.args = args;
        stateMachine.<>1__state = -1;
        stateMachine.<>t__builder.Start(ref stateMachine);
        return stateMachine.<>t__builder.Task;
    }
}
```
Это и есть машина состояний. Попробуй [убрать](https://sharplab.io/#v2:EYLgtghglgdgNAExAagD4AEBMBGAsAKHQAYACdbAVgG4CD0BmMzM7AdhJIIG8OCOOGLAGxkAHGREBZaDAAUABwgAnCGADOLIgG0AuiWUBzNQEp+ffj075+NlgE5ZAIgASAUwA27gPaPjVXtZWtvzodhIAdAAiHhAAnrLYREl+5sGptuQOjgDqXkruCL7+QcElwaER0e5xCUlEKYFpjRnYWQCSAORgJABSrkpKsUUBNgC+NqkEo0A) один `Task.Delay` чтобы убедиться, что количество состояний равно количеству await в методе. Также можешь посмотреть, какой IL-код будет сгенерирован.

#### Для чего нужен класс Task?

`Task` абстрагирует асинхронную задачу и её выполнение. `Task` позволяет проще и эффективнее работать с потоками по сравнению с ручным управлением. `Task` выполняется на потоке из `ThreadPool`. У `Task` есть статус, по которому можно определить ее состояние. 

#### Что происходит при создании Task? Откуда берутся потоки?

При создании таски берется свободный поток из пула потоков, на который она себя планирует. Если свободного потока нет, то CLR попробует создать его. Если лимит доступных потоков исчерпан, то таска будет ждать пока не появится свободный поток.

#### Что происходит с вызывающим потоком, когда он доходит до await?

Он возвращается в пул потоков. При этом таска выполняется на одном из потоков, взятых из пула потоков. После выполнения асинхронной задачи управление возвращается коду, который вызвал её. Он продолжит свое выполнение в случайном потоке, взятом из пула потоков.

#### Можно ли гарантировать, что код продолжит выполнение в том же потоке, в котором начал, после того, как успешно отработал await?

Нет. Но можно использовать метод `ConfigureAwait()` с параметром `true`, которые сигнализирует о том, что выполнение стоит продолжить в том же потоке.

```csharp
var result = await DoAsync().ConfigureAwait(true);
```

В ASP.NET приложениях `ConfigureAwait(true)` не работает, так как там нет контекста синхронизации.

#### Для чего нужен метод Task.WhenAll? Можно ли ожидать выполнение нескольких Task, выполняющихся параллельно?

Да. Для этого следует использовать метод `Task.WhenAll`, который принимает коллекцию `Task`. `WhenAll` ожидает, пока завершатся все `Task`, переданные в него. При возникновении исключения в одной из `Task`, переданных в `WhenAll`, во время ожидания (`await Task.WhenAll`) будет выброшено первое исключение. Для того, чтобы получить оригинальные исключения из всех `Task` нужно получить их результат через `await`, `.Wait()` или `.Result`. 
```csharp
public static async Task Main(params string[] args)  
{  
    Task t1 = Do();  
    Task t2 = Throws();  
    Task t3 = Throws();  
  
    // Создаем массив Task, они уже выполняются  
    Task[] tasks = new[] { t1, t2, t3 };  
  
    // Создаем Task, который будет ожидать завершения всех Task из массива tasks  
    Task waitingTask = Task.WhenAll(tasks);  
    try  
    {  
        // Ожидаем выполнения всех Task, помещенных в WhenAll  
        await waitingTask;  
    }    
    // Обрабатываем исключения из всех неуспешно завершившихся Task  
    catch (Exception ex)  
    {        
	    Console.WriteLine("WhenAll done with exceptions");  
  
        // Все Task, завершившиеся неуспешно  
        var tasksWithErrors = tasks.Where(x => x.IsFaulted); 
	    // Обрабатываем исключения из всех неуспешно завершившихся Task 
        foreach (var tasksWithError in tasksWithErrors)  
        {            
	        try  
            {  
                // Получаем результаты неуспешных Task, чтобы обработать исключения  
                await tasksWithError;  
            }            
            catch (Exception taskEx)  
            {                
	            Console.WriteLine(taskEx.Message);  
            }       
        }   
    }
}  
  
public static async Task Do()  
{  
    await Task.Delay(2000);  
    Console.WriteLine("World");  
}  
  
public static async Task Throws()  
{  
    await Task.Delay(2000);  
    throw new InvalidOperationException();  
}
```

Этот код выведет следующее
```
World
WhenAll done with exceptions
Operation is not valid due to the current state of the object.
Operation is not valid due to the current state of the object.
```

#### Можно ли выполнить/запустить синхронный код асинхронно?

Да. Это можно сделать с помощью `Task.Run` или `Task.Factory.StartNew`.  
`Task.Factory.StartNew` предоставляет расширенные возможности по запуску асинхронных задач за счет перегрузок. Например, можно указать тип таски.
```csharp
Task.Run(() => Console.WriteLine("Hello"));  
 
Task.Factory.StartNew(() => Console.WriteLine("World"));
```

#### Как отменить запущенную Task

Для отмены `Task` следует использовать `CancellationToken`.

#### Практическое задание

Аналоги данного задания зачастую встречаются на собеседованиях. 
Спрашивают, что происходит в коде. 
Меняют `Do()` на `Do().Wait()` или `await Do()` и спрашивают в чем отличие.

```csharp
public class Program
{
	public static async Task Main(params string[] args) 
	{
		Console.WriteLine("Hello");
	
		Do();
	
		Console.WriteLine("I'm Jerry");
	}
	
	public static async Task Do()
	{
		await Task.Delay(2000);
		Console.WriteLine("World");
	}
}
```

В код будет выведено "Hello" и "I'm Jerry". Вывод "World" не произойдет, так как в методе Main мы не ожидаем выполнения таски `Do(),` а внутри нее ждем 2000 миллисекунд. Это обусловлено тем, что консольное приложение завершается, когда основной поток доходит до конца метода Main. Поэтому таска `Do()` не будет выполнена.
При этом, если снизить время ожидания в Delay до нескольких миллисекунд, то таска `Do()` может успеть выполниться до завершения программы и последовательность вывода "World" и "I'm Jerry" будет неопределена.

Если заменить `Do()` на `Do().Wait()`, то вывод будет следующим: "Hello", "World", "I'm Jerry". Метод `Wait()` ожидает выполнения таски в синхронном режиме. Это означает, что поток метода Main будет заблокирован и будет ожидать выполнения таски `Do()`
```csharp
public class Program
{
	public static async Task Main(params string[] args) 
	{
		// Поток 1
		Console.WriteLine("Hello");

		// Поток 1 ждет
		Do().Wait(); // Do выполняется в случайном потоке из пула
		
		// Поток 1 продолжает выполнение
		Console.WriteLine("I'm Jerry");
	}
	
	public static async Task Do()
	{
		await Task.Delay(2000);
		Console.WriteLine("World");
	}
}
```

Если заменить `Do()` на `await Do(`), произойдет тоже самое, что и в случае с `Wait()`, но основной поток Main не будет ожидать выполнения таски и вернется в пул потоков. Выполнение метода продолжится в случайном потоке.
```csharp
public class Program
{
	public static async Task Main(params string[] args) 
	{
		// Поток 1
		Console.WriteLine("Hello");

		// Поток 1 возвращается в пул потоков
		await Do(); // Do выполняется в случайном потоке из пула
		
		// Случайный поток из пула продолжает выполнение
		Console.WriteLine("I'm Jerry");
	}
	
	public static async Task Do()
	{
		await Task.Delay(2000);
		Console.WriteLine("World");
	}
}
```

Чтобы проверить вышеописанное можно запустить код в дебаге:
```csharp
public class Program  
{  
    public static async Task Main(params string[] args)   
    {  
        // Поток 1  
        Console.WriteLine("Hello");  
        var threadBeforeWait = Thread.CurrentThread.ManagedThreadId;  
  
        // Поток 1 ждет  
        await Do(); // Do выполняется в случайном потоке из пула  
    
	    // Случайный поток из пула продолжает выполнение  
        var threadAfterWait = Thread.CurrentThread.ManagedThreadId;  
        
        Console.WriteLine("I'm Jerry");  
    }    
    
    public static async Task Do()  
    {       
	    await Task.Delay(2000);  
        Console.WriteLine("World");  
    }
}
```

## Synchronization

[.NET: Инструменты для работы с многопоточностью и асинхронностью. Часть 1](https://habr.com/ru/articles/452094/)  
[.NET: Инструменты для работы с многопоточностью и асинхронностью. Часть 2](https://habr.com/ru/articles/459514/)

### Monitor (lock)

### Semaphore

### ReaderWriterLock

### Interlocked

### Mutex

# ASP.NET

## Execution Pipeline

### Middlewares

[ASP.NET Core Middleware](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/middleware/?view=aspnetcore-8.0)

### Exception filters

[Filters in ASP.NET Core: Exception filters](https://learn.microsoft.com/en-us/aspnet/core/mvc/controllers/filters?view=aspnetcore-8.0#exception-filters)

### Access to http-context

[Access HttpContext in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/http-context?view=aspnetcore-8.0)

## Dependency Injection

[.NET dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection)  
[Dependency injection in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-8.0)

### Lifetimes

[.NET dependency injection: Service lifetimes](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-8.0#service-lifetimes)

## Background Jobs

[Worker services in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/workers)

### IHostedService

### BackgroundService

# ORM

## EntityFramework Core

**Что почитать**

Джон П. Смит: EntityFramework Core в действии

### Pros & Cons

[Advantages and disadvantages of Entityframework.](https://medium.com/@anandugnath/advantages-and-disadvantages-of-entityframework-4c7b0e016788)  
[Raw SQL vs Entity Framework Core: Which is Right for Your Application?](https://habr.com/ru/sandbox/190738/)

### Unit Of Work

[Unit of Work Pattern in C# for Clean Architecture: What You Need To Know](https://www.codeproject.com/Articles/5377618/Unit-of-Work-Pattern-in-Csharp-for-Clean-Architect)  
[Implementing the Repository and Unit of Work Patterns in an ASP.NET MVC Application (9 of 10)](https://learn.microsoft.com/en-us/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)

### Migrations

[Managing Database Schemas](https://learn.microsoft.com/en-us/ef/core/managing-schemas/)  
[Migrations Overview](https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/?tabs=dotnet-core-cli)

### Change Tracking

[Change Tracking in EF Core](https://learn.microsoft.com/en-us/ef/core/change-tracking/)  
[Отслеживание объектов и AsNoTracking](https://metanit.com/sharp/entityframeworkcore/5.7.php)  
[Entity Framework Core и высокая производительность](https://habr.com/ru/articles/675310/)

## Dapper

[Learn Dapper](https://www.learndapper.com) - Общие материалы

### Pros & Cons

[EF VS DAPPER](https://www.manojphadnis.net/entity-framework/ef-vs-dapper)

## Concurrency Models

[Optimistic vs Pessimistic Concurrency: What Every Developer Should Know](https://cult.honeypot.io/reads/optimistic-vs-pessimistic-concurrency/)  
[Optimistic Concurrency - ADO.NET](https://learn.microsoft.com/en-us/dotnet/framework/data/adonet/optimistic-concurrency)

# Databases

## Relational

[Что такое реляционная база данных (РСУБД)?](https://www.oracle.com/cis/database/what-is-a-relational-database/)

### General Database Knowledge

#### Indexes

Начни с видео.

[SQL Indexing and Tuning e-Book](https://use-the-index-luke.com)  
[What is a Database Index?](https://www.codecademy.com/article/sql-indexes)  
🎥 [Что такое SQL ИНДЕКСЫ за 10 минут: Объяснение с примерами](https://www.youtube.com/watch?v=LpEwssOYRKA)

#### Stored procedures

[Understanding Stored Procedures in SQL: Benefits & Creation!](https://www.simplilearn.com/tutorials/sql-tutorial/stored-procedure-in-sql)
[Stored procedure](https://www.techtarget.com/searchoracle/definition/stored-procedure#:~:text=A%20stored%20procedure%20is%20a,and%20shared%20by%20multiple%20programs.)

### SQL

#### Select

[SQL SELECT Statement](https://www.w3schools.com/sql/sql_select.asp)  
[SQL WHERE Clause](https://www.w3schools.com/sql/sql_where.asp)  
[SQL AND Operator](https://www.w3schools.com/sql/sql_and.asp)  
[SQL OR Operator](https://www.w3schools.com/sql/sql_or.asp)  
[SQL NOT Operator](https://www.w3schools.com/sql/sql_not.asp)

#### Limit, offset

[SQL TOP, LIMIT, FETCH FIRST or ROWNUM](https://www.w3schools.com/sql/sql_top.asp)

#### Order by

[SQL ORDER BY Keyword](https://www.w3schools.com/sql/sql_orderby.asp)

#### Join

[SQL Joins](https://www.w3schools.com/sql/sql_join.asp)  
[SQL INNER JOIN](https://www.w3schools.com/sql/sql_join_inner.asp)  
[SQL LEFT JOIN Keyword](https://www.w3schools.com/sql/sql_join_left.asp)  
[SQL RIGHT JOIN Keyword](https://www.w3schools.com/sql/sql_join_right.asp)  
[SQL FULL OUTER JOIN Keyword](https://www.w3schools.com/sql/sql_join_full.asp)  
[SQL Self Join](https://www.w3schools.com/sql/sql_join_self.asp)

#### Group by, having

[SQL GROUP BY Statement](https://www.w3schools.com/sql/sql_groupby.asp)  
[SQL HAVING Clause](https://www.w3schools.com/sql/sql_having.asp)

#### Aggregate functions

[SQL Aggregate Functions](https://www.w3schools.com/sql/sql_aggregate_functions.asp)

## NoSQL

[What is NoSQL?](https://www.mongodb.com/resources/basics/databases/nosql-explained)

### Difference vs Relational

[Relational vs. Non-Relational Databases](https://www.mongodb.com/resources/compare/relational-vs-non-relational-databases)

# Brokers

## RabbitMQ

[RabbitMQ. Часть 1. Introduction. Erlang, AMQP](https://habr.com/ru/articles/488654/)
[Когда и зачем нужен RabbitMQ](https://habr.com/ru/companies/slurm/articles/684412/)

## Kafka

[Асинхронное взаимодействие. Брокеры сообщений. Apache Kafka](https://habr.com/ru/companies/vivid_money/articles/534858/)

### Difference vs AMQP Brokers

[RabbitMQ против Kafka: два разных подхода к обмену сообщениями](https://habr.com/ru/companies/itsumma/articles/416629/)

## Delivery Guarantees

[Outbox, Inbox patterns and delivery guarantees explained](https://event-driven.io/en/outbox_inbox_patterns_and_delivery_guarantees_explained/)  

# Principles & Patterns

## Principles

### OOP

[ООП в картинках](https://habr.com/ru/articles/463125/)  
[Overview of object oriented techniques in C#](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/)

### SOLID

[Принципы SOLID в картинках](https://habr.com/ru/companies/productivity_inside/articles/505430/)  
[Принципы SOLID на примерах](https://habr.com/ru/articles/688530/)  
[Принципы Solid и как они помогают сделать код лучше](https://habr.com/ru/companies/itentika/articles/694730/)

### KISS

[KISS — принцип проектирования, содержащий все остальные принципы проектирования](https://habr.com/ru/articles/249639/)

### DRY

[Три ключевых принципа ПО, которые вы должны понимать](https://habr.com/ru/articles/144611/)

## Patterns

### Transactional Outbox / Inbox

[Outbox, Inbox patterns and delivery guarantees explained](https://event-driven.io/en/outbox_inbox_patterns_and_delivery_guarantees_explained/)  
[Pattern: Transactional outbox](https://microservices.io/patterns/data/transactional-outbox.html)


### Saga

[Pattern: Saga](https://microservices.io/patterns/data/saga.html)

# Architecture

## Microservices

🎥 [Про микросервисы за 8 минут](https://youtu.be/rCbdQc42eCw?si=UQ-mfnXQOvTpb2cA)

**Что почитать**

- Крис Ричардсон: Микросервисы. Паттерны разработки и рефакторинга

### Pros & Cons

[Микросервисы: плюсы, минусы, когда и зачем внедрять](https://habr.com/ru/companies/slurm/articles/674600/)

# General Concepts

## Methodologies

### Agile

[Что такое методология Agile](https://evrone.ru/blog/articles/agile)  
[Agile-манифест разработки программного обеспечения](https://agilemanifesto.org/iso/ru/manifesto.html)

### Scrum

[Что такое Scrum и как это освоить](https://www.atlassian.com/ru/agile/scrum)  
🎥 [Что такое Scrum за 8 минут](https://youtu.be/jlFtHghpMhk?si=35c9t2z3KxR1lUbV)

## CI/CD

[Что такое CI/CD и как эта практика связана с DevOps](https://gitinsky.com/chto-takoe-ci-cd)  
🎥 [Про CI/CD за 5 минут](https://youtu.be/Y-hYifHkjMs?si=NyscZjYmEOq4mZdl)