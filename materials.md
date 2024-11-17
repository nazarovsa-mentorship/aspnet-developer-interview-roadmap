# Материалы для изучения

**Легенда**

🎥 - Видео  
❗️ - Сложный материал
🗂️ - Тема исключена из роадмапа, но материалы остались для общего ознакомления

Если в разделе есть видео, сначала прочитай текстовые материалы: после просмотра желание читать что-либо может пропасть.

Ссылки на материалы microsoft могут являться базовым вектором для изучения, так как зачастую ссылаются на корневую страницу разделов документации. Не пренебрегай возможностью изучить эти разделы целиком.

Разделы без ссылок на материалы полразумевают, что для старта вам нужно знать эти темы из корневого раздела. Основной материал распологается под корневым разделом.  
Например, в разделе [Background Jobs](#background-jobs) вам нужно знать базовые концепты из ссылки в разделе и понимать назначение интерфейсов `IHostedService` и `BackgroundService`.

# Platform & Language

### Managed code

[What is "managed code"?](https://learn.microsoft.com/en-us/dotnet/standard/managed-code)

## Types

[Авторские материалы](./materials/002-types.md)

### Value types

[Value types (C# reference)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/value-types)

### Reference types

[Reference types (C# reference)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/reference-types)  
[Типы значений и ссылочные типы](https://metanit.com/sharp/tutorial/2.16.php)

### Reflection

[Рефлексия](https://metanit.com/sharp/tutorial/14.1.php)  
❗️[.NET Reflection. Упрощаем работу и используем в тестировании](https://habr.com/ru/articles/716104/)

### Boxing

[Microsoft docs: Boxing and unboxing](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/types/boxing-and-unboxing)  
[Microsoft docs: Boxing and unboxing performance](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/types/boxing-and-unboxing)

## Memory management

[CLRium #5: Курс "Garbage Collector". 1/7: Основы строения памяти](https://www.youtube.com/watch?v=DVnmGW6964o&list=PLBwwJL9lzKMY3At-QQQijfiHdsYnOQ7vY) - Плейлист о строении памяти в .net.

### Garbage Collector

[На спор: прочитав до конца, вы поймёте, как и почему именно так работает GC](https://habr.com/ru/companies/clrium/articles/464169/)
[Fundamentals of garbage collection](https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/fundamentals)

### Finalizers & Disposable

[Finalizers (C# Programming Guide)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/finalizers)  
[Финализируемые объекты](https://metanit.com/sharp/tutorial/8.2.php)

## Async programming

[Авторские материалы](./materials/001-async.md)
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

## Synchronization

[Авторские материалы](./materials/003-synchronization.md)

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

Джон П. Смит: EntityFramework Core в действии - Главы 1, 2, 3, 4, 5

### Pros & Cons

[Advantages and disadvantages of Entityframework.](https://medium.com/@anandugnath/advantages-and-disadvantages-of-entityframework-4c7b0e016788)  
[Raw SQL vs Entity Framework Core: Which is Right for Your Application?](https://habr.com/ru/sandbox/190738/)

### 🗂️ Unit Of Work

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

#### 🗂️ Stored procedures

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

[AWS: What’s the Difference Between Kafka and RabbitMQ?](https://aws.amazon.com/compare/the-difference-between-rabbitmq-and-kafka/)
[RabbitMQ против Kafka: два разных подхода к обмену сообщениями](https://habr.com/ru/companies/itsumma/articles/416629/)

## Delivery Guarantees

[Outbox, Inbox patterns and delivery guarantees explained](https://event-driven.io/en/outbox_inbox_patterns_and_delivery_guarantees_explained/)  


# Architecture

## Microservices

🎥 [Про микросервисы за 8 минут](https://youtu.be/rCbdQc42eCw?si=UQ-mfnXQOvTpb2cA)

**Что почитать**

- Крис Ричардсон: Микросервисы. Паттерны разработки и рефакторинга

### Pros & Cons

[Микросервисы: плюсы, минусы, когда и зачем внедрять](https://habr.com/ru/companies/slurm/articles/674600/)

## Patterns

### Transactional Outbox

[Pattern: Transactional outbox](https://microservices.io/patterns/data/transactional-outbox.html)

### Transactional Inbox

[Outbox, Inbox patterns and delivery guarantees explained](https://event-driven.io/en/outbox_inbox_patterns_and_delivery_guarantees_explained/)  

### Saga

[Pattern: Saga](https://microservices.io/patterns/data/saga.html)

# Principles

## OOP

[ООП в картинках](https://habr.com/ru/articles/463125/)  
[Overview of object oriented techniques in C#](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/)

## SOLID

[Принципы SOLID в картинках](https://habr.com/ru/companies/productivity_inside/articles/505430/)  
[Принципы SOLID на примерах](https://habr.com/ru/articles/688530/)  
[Принципы Solid и как они помогают сделать код лучше](https://habr.com/ru/companies/itentika/articles/694730/)

## KISS

[KISS — принцип проектирования, содержащий все остальные принципы проектирования](https://habr.com/ru/articles/249639/)

## DRY

[Три ключевых принципа ПО, которые вы должны понимать](https://habr.com/ru/articles/144611/)

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
