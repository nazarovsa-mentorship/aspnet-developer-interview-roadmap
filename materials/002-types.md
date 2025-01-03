# Типы в платформе dotnet

# Какие типы существуют в платформе dotnet?

Типы делятся на значимые и ссылочные.

## Значимые типы 

**Значимые типы** - это простые типы: int, long, float, double и т. д., а также структуры (struct). 

## Ссылочные типы 

**Ссылочные типы** - это классы, массивы, списки, упакованные значимые типы и т.д.

# В чем отличие ссылочных и значимых типов?
Ссылочные и значимые типы имеют отличия при хранением в памяти и передаче в качестве параметра.

| **Типы**      | **Расположение в памяти**                                                                                                          | **Передача в качестве параметра**                                                                                                                           | **Создание экземпляра**                                                                                                                 |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| **Ссылочные** | Хранятся в управляемой куче.                                                                                                       | Передаются по ссылке.<br>**Изменения внутри метода повлияют на экземпляр снаружи метода.** Ссылка снаружи и внутри метода указывает на один сегмент памяти. | На стеке метода выделяется память под указатель. Экземпляр помещается в управляемую кучу. Указатель из стека ссылается на адрес в куче. |
| **Значимые**  | Хранятся в стеке. Могут храниться в куче:<br>1. При упаковке<br>2. Если являются членом класса или при нахождении внутри коллекции | Передаются по значению.<br>**Изменения внутри метода не повлияют на экземпляр снаружи метода.** При этом в методе создается копия переданного экзепляра.    | На стеке метода выделяется память размером значимого типа. Тип помещается в стек памяти.                                                |

Помимо этого есть особенности представления экземпляров типов в памяти.  

Экземпляр значимого типа в памяти всегда представляет собой строгий блок памяти без какой-либо информации. 

Экземпляр ссылочного тип помимо основных данных имеет дополнительную информацию: указатель на таблицу виртуальных методов (Type Handle) и индекс блока синхронизации (Sync Block Index). Каждый из них занимает дополнительную память равную размеру указателя ([IntPtr](https://learn.microsoft.com/ru-ru/dotnet/api/system.intptr)) в операционной системе: 4 байта в x32 и 8 байт в x64. 

Указатель на таблицу виртуальных методов позволяет определить, экземпляром какого типа является объект хранящийся в куче. Благодаря ему можно наследовать ссылочные типы. 

Индекс блока синхронизации используется для синхронизации во время блокировок. Именно он позволяет вызывать `lock` на экземплярах ссылочного типа. 

[DotNetBook: Структура объектов в памяти](https://dotnetbook.readthedocs.io/ru/latest/ObjectsStructure/)

# Могут ли структуры наследовать от других типов?

Нет. Из-за отсутствия указателя на таблицу виртуальных методов. 
Структуры могут реализовывать интерфейсы.

# Можно ли передать значимый тип по ссылке?

Да. Для этого можно использовать ключевые слова ref или in.

# Можно ли передать ссылочный тип по ссылке?

Да. Для этого нужно использовать ref. Это позволит поменять ссылку на объект в вызывающем коде внутри метода. 

При передаче ссылочного типа в качестве аргумента без ref невозможно заменить ссылку на объект снаружи метода.  

**Ссылка** - значимый тип. При передаче экземпляра ссылочного типа в метод на стеке этого метода создается новая ссылка, указывающая на тот же сегмент памяти, что и переданная. Это позволяет менять оригинальный объект, но не позволяет заменить его значение, так как изменяется ссылка внутри метода, которая будет удалена из стека после его завершения.

```csharp
// Создаем экземпляр ссылочного типа. Пусть он указывает на адрес X в куче
Point point = new Point(0, 0);

// Передаем экземпляр ссылочного типа в метод, а точнее ссылку на адрес X
ChangePoint(point);

Console.WriteLine(point.X + " " point.Y); // -> Консоль выведет "0 0"

public void ChangePoint(Point point)
{
	// Так как ссылка - значимый тип, point имеет новую ссылку указывающую на адрес X
	// При изменении ссылки, оно не повлияет на ссылку вызовом меньше
	point = new Point(1, 1);
}

public sealed class Point(int X, int Y);
```

Использование ref позволяет передать ссылку по ссылке.

```csharp
// Создаем экземпляр ссылочного типа. Пусть он указывает на адрес X в куче
Point point = new Point(0, 0);

// Передаем экземпляр ссылочного типа в метод, а точнее ссылку на адрес X по ссылке
ChangePoint(ref point);

Console.WriteLine(point.X + " " point.Y); // -> Консоль выведет "1 1"

public void ChangePoint(ref Point point)
{
	// Так как ссылка переданна с использованием ref, point является той же ссылкой, что и в вызывающем коде
	// Изменение ссылки повлияет на ссылку в вызывающем коде 
	point = new Point(1, 1);
}
```

[Microsoft docs: Method parameters](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/method-parameters?redirectedfrom=MSDN)

# Для чего нужны in, ref, out? В чем отличие между in и ref?

Ключевые слова `in`, `ref`, `out` могут быть использованы с параметрами методов.  
`in` и `ref` позволяют передать параметр по ссылке. in не позволяет изменять значение параметра внутри метода.  
`out` позволяет вернуть значение из метода через параметр. То есть заполнить параметр внутри метода.  

| **Ключевое слово** | **Тип параметра** | **Требует явного указания при передаче параметра** | **Делает параметр неизменяемым** |
| ------------------ | ----------------- | -------------------------------------------------- | -------------------------------- |
| **ref**            | Входной           | Нет                                                | Нет                              |
| **in**             | Входной           | Нет                                                | Да                               |
| **out**            | Выходной          | Да                                                 | Нет                              |
```csharp
// Передача ссылочного типа с ref
public void ChangePointRef(ref object obj)  
{  
	// Можно изменить внешнюю ссылку
	obj = new object();  
}  
  
// Передача ссылочного типа с in
public void ChangePointIn(in object obj)  
{  
	// Нельзя изменить внешнюю ссылку: ошибка компиляции
	obj = new object();  
}  
  
// Передача значимого типа с ref
public void ChangeIntRef(ref int value)  
{  
	// Можно изменить внешнее значение
	value = 0;  
}  

// Передача значимого типа с in
public void ChangeIntIn(in int value)  
{  
	// Нельзя изменить внешнее значение: ошибка компиляции
	value = 0;  
}
```

[Reference parameters](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/method-parameters#reference-parameters)

# Что такое рефлексия? 

**Рефлексия** - это механизм, позволяющий получать информацию о типах и выполнять с ними действия время выполнения програмы.

Недостатки рефлексии:
- Рефлексия работает медленнее прямого вызова
- Нарушает инкапсуляцию, так как позволяет получить доступ к приватным членам типов
- Усложняет поддержку кода, так как сложна для восприятия

## Примеры использования рефлексии

### Работа с атрибутами

```csharp
[Obsolete]
class MarkedClass { }

// Проверка наличия атрибута
bool hasAttribute = typeof(MarkedClass).IsDefined(typeof(ObsoleteAttribute), false);
// Получение атрибутов
var attributes = typeof(MarkedClass).GetCustomAttributes(false);
```
### Получение информации о типе и его членах: свойствах, методах полях

```csharp
Type type = typeof(string);
// или
object obj = "test";
Type type2 = obj.GetType();

// Получение информации о членах типа
PropertyInfo[] properties = type.GetProperties();
MethodInfo[] methods = type.GetMethods();
FieldInfo[] fields = type.GetFields();
```

### Создание экземпляров типа с помощью метода `Activator.CreateInstance()`

```csharp
// Создание объекта по имени типа
Type type = Type.GetType("System.String");
object instance = Activator.CreateInstance(type);

// Создание с параметрами конструктора
var obj = Activator.CreateInstance(type, new object[] { "parameter" });
```

### Вызов методов на экземпляре типа

```csharp
class Example {
    private void TestMethod(string param) { }
}

Example ex = new Example();
MethodInfo method = typeof(Example).GetMethod("TestMethod", 
    BindingFlags.NonPublic | BindingFlags.Instance);
method.Invoke(ex, new object[] { "test" });
```
### Получение доступа к полям и свойствам

```csharp
class Person {
    private string _name;
    public int Age { get; set; }
}

Person person = new Person();
// Получение/установка приватного поля
FieldInfo field = typeof(Person).GetField("_name", 
    BindingFlags.NonPublic | BindingFlags.Instance);
field.SetValue(person, "John");

// Работа со свойством
PropertyInfo prop = typeof(Person).GetProperty("Age");
prop.SetValue(person, 25);
```

# Чем GetType отличается от typeof?

## Время выполнения

```csharp
// typeof - разрешается на этапе компиляции
Type stringType = typeof(string);

// GetType - выполняется в рантайме
string str = "test";
Type stringType2 = str.GetType();
```
## Использование с обобщенными типами

```csharp
// typeof работает с generic типами
Type listType = typeof(List<>);
Type listStringType = typeof(List<string>);

// GetType можно использовать только с конкретным экземпляром
List<string> list = new List<string>();
Type actualType = list.GetType(); // List<string>
```
## Работа с наследованием

```csharp
class Base { }
class Derived : Base { }

Base obj = new Derived();

Type type1 = typeof(Base);     // Всегда вернёт тип Base
Type type2 = obj.GetType();    // Вернёт реальный тип - Derived
```

## Производительность 

`typeof` работает быстрее, так как тип определяется на этапе компиляции
`GetType` требует дополнительных операций во время выполнения

## Когда что использовать 

`typeof`: когда тип известен на этапе компиляции. 
`GetType`: когда нужно определить фактический тип объекта во время выполнения

# Что такое Boxing?

**Boxing или упаковка** - это механизм позволяющий разместить значимый тип на куче.

```csharp
var boxed = (object)10; // <-- Выполнилась упаковка, т.е. 10 упаковалось в объект, который разместился на куче
``` 

**Unboxing или распаковка** - это операция обратная упаковке, которая позволяет извлечь упакованное значение из объекта.
```csharp
var boxed = (object)10; 
var unboxed = (int)boxed;
```

Основной недостаток упаковки и распаковки - это низкая производительность.  
Для операции упаковки необходимо разместить объект на куче, что может занимать в 20 раз больше времени, чем присвоение ссылки.  
Для операции распаковки выполнить приведение к нужному типу, который может занимать в 4 раза больше времени, чем присвоение.  

[Microsoft docs: Boxing and unboxing performance](https://learn.microsoft.com/en-us/dotnet/framework/performance/performance-tips#boxing-and-unboxing)