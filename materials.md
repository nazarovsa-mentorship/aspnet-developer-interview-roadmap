# Материалы для изучения

**Легенда**

🎥 - Видео  
❗️ - Сложный материал

Если в разделе есть видео, сначала прочитай текстовые материалы: после просмотра желание читать что-либо может пропасть.

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

### "Types" of memory

#### Stack

#### Small object heap

#### Large object heap

## Async programming

### Task

[Task-based asynchronous programming](https://learn.microsoft.com/en-us/dotnet/standard/parallel-programming/task-based-asynchronous-programming)  
🎥❗️ [CLRium #6: Tasks](https://youtu.be/HiKzuWbRgPM?si=aBcgmxYIUj6nLRYp)

### async/await

[Asynchronous programming with async and await](https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/)  
[Async/await в C#: концепция, внутреннее устройство, полезные приемы](https://habr.com/ru/articles/470830/)  
❗️ [Как на самом деле работает Async/Await в C# (Часть 1)](https://habr.com/ru/articles/727850/)  
🎥❗️ [CLRium #6: async/await. Машина состояний](https://youtu.be/_suxE9frTFA?si=ORA-S7AmEyEL4BqJ)

## Synchronization

[.NET: Инструменты для работы с многопоточностью и асинхронностью. Часть 1](https://habr.com/ru/articles/452094/)  
[.NET: Инструменты для работы с многопоточностью и асинхронностью. Часть 2](https://habr.com/ru/articles/459514/)

### Monitor (lock)

### Semaphore

### ReaderWriterLock

### Interlocked

### Mutex

# ASP.NET

##  Execution Pipeline

###  Middlewares

###  Exception filters

###  Access to http-context

##  Dependency Injection

###  Lifetimes

##  Background Jobs

###  IHostedService

###  BackgroundService

# ORM

##  EntityFramework Core

###  Pros & Cons

###  Unit Of Work

###  Code-first

###  Migrations

###  Change Tracking

##  Dapper

###  Pros & Cons

# Databases

##  Relational

###  General Database Knowledge

####  Indexes

#####  Clustered

#####  Composite

#####  Covering

####  Triggers

####  Stored procedures

####  Concurrency Models

#####  Optimistic

#####  Pessimistic

###  SQL

####  Select, from, where, limit, offset

####  Order by

####  Group by, having

####  Aggregate functions

#####  Avg

#####  Count

#####  Min

#####  Max

###  PostgreSQL

###  MySQL

##  NoSQL

###  Difference vs Relational

# Brokers

##  RabbitMQ

[RabbitMQ. Часть 1. Introduction. Erlang, AMQP](https://habr.com/ru/articles/488654/)
[Когда и зачем нужен RabbitMQ](https://habr.com/ru/companies/slurm/articles/684412/)

##  Kafka

[Асинхронное взаимодействие. Брокеры сообщений. Apache Kafka](https://habr.com/ru/companies/vivid_money/articles/534858/)

###  Difference vs AMQP Brokers

[RabbitMQ против Kafka: два разных подхода к обмену сообщениями](https://habr.com/ru/companies/itsumma/articles/416629/)

# Principles & Patterns

##  OOP

[ООП в картинках](https://habr.com/ru/articles/463125/)  
[Overview of object oriented techniques in C#](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/)

##  SOLID

[Принципы SOLID в картинках](https://habr.com/ru/companies/productivity_inside/articles/505430/)  
[Принципы SOLID на примерах](https://habr.com/ru/articles/688530/)  
[Принципы Solid и как они помогают сделать код лучше](https://habr.com/ru/companies/itentika/articles/694730/)

##  KISS

[KISS — принцип проектирования, содержащий все остальные принципы проектирования](https://habr.com/ru/articles/249639/)

##  DRY

[Три ключевых принципа ПО, которые вы должны понимать](https://habr.com/ru/articles/144611/)

# Architecture

##  Microservices

🎥 [Про микросервисы за 8 минут](https://youtu.be/rCbdQc42eCw?si=UQ-mfnXQOvTpb2cA)

**Что почитать**

- Крис Ричардсон: Микросервисы. Паттерны разработки и рефакторинга

###  Pros & Cons

[Микросервисы: плюсы, минусы, когда и зачем внедрять](https://habr.com/ru/companies/slurm/articles/674600/)

# General Concepts

##  Methodologies

###  Agile

[Что такое методология Agile](https://evrone.ru/blog/articles/agile)  
[Agile-манифест разработки программного обеспечения](https://agilemanifesto.org/iso/ru/manifesto.html)

###  Scrum

[Что такое Scrum и как это освоить](https://www.atlassian.com/ru/agile/scrum)  
🎥 [Что такое Scrum за 8 минут](https://youtu.be/jlFtHghpMhk?si=35c9t2z3KxR1lUbV)

##  CI/CD

[Что такое CI/CD и как эта практика связана с DevOps](https://gitinsky.com/chto-takoe-ci-cd)  
🎥 [Про CI/CD за 5 минут](https://youtu.be/Y-hYifHkjMs?si=NyscZjYmEOq4mZdl)