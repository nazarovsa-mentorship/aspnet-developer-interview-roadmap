# Управление памятью

# Какие типы памяти существуют? Где могут храниться экземпляры ссылочных и значимых типов?

В CLR существуют два основных типа памяти для хранения экземпляров типов: стэк и управляемая куча.  

**Стэк** - это быстрая память, которая используется для хранения значения экземпляров значимых типов и ссылок на экземпляры ссылочных типов в куче, во время выполнения методов.  
Под стэк резервируется сегмент оперативной памяти, не нужно путать со стэком процессора.  

**Управляемая куча** - это память, которая используется для хранения экземпляров ссылочных типов и упакованных значимых.  
Существует три управляемых кучи:
- **Small Object Heap (SOH)** - куча маленьких объектов или просто куча. Используется для хранения экземпляров ссылочных типов, которые не хранятся в других кучах.
- **Large Object Heap (LOH)** - куча больших объектов. Используется для хранения экземпляров ссылочных типов размером более 85000 байт и массивов `double` размером больше 1000 элементов. 
- **Pined Object Heap (POH)** - куча закрепленных объектов. Используется для хранения экзепляров закрепленных объектов. Нужна для повышения производительности при работе с ними. Для собеседования достаточно знать этого. 

## Что есть у объекта при хранении в памяти кроме его полей и свойств?

Экземпляр ссылочного тип помимо основных данных имеет дополнительную информацию: указатель на таблицу виртуальных методов (Type Handle) и индекс блока синхронизации (Sync Block Index). Каждый из них занимает дополнительную память равную размеру указателя ([IntPtr](https://learn.microsoft.com/ru-ru/dotnet/api/system.intptr)) в операционной системе: 4 байта в x32 и 8 байт в x64. 

Указатель на таблицу виртуальных методов позволяет определить, экземпляром какого типа является объект хранящийся в куче. Благодаря ему можно наследовать ссылочные типы. 

Индекс блока синхронизации используется для синхронизации во время блокировок. Именно он позволяет вызывать `lock` на экземплярах ссылочного типа. 

[DotNetBook: Структура объектов в памяти](https://dotnetbook.readthedocs.io/ru/latest/ObjectsStructure/)


## Сколько поколений существует в Small Object Heap (SOH)

Существует три поколения второе, первое и нулевое. В памяти они располагаются слева направо от большего к меньшему с начала сегмента. 

|| 2 поколение | 1 поколение | 0 поколение | зарезервированная память, доступная для расширения 0 поколения ||

Поколения нужны для оптимизации сборки мусора: выполнить сборку в части кучи быстрее, чем во всей куче. После выполнения цикла сборки несобранные объекты перемещаются в следующее поколение. Движение всегда идет влево, т. е. из 0 в 1, из 1 во 2.  

В 0 поколении всегда находятся объекты не пережившие сборку мусора. Во втором поколении находятся долгоживущие объекты, которые пережили две сборки мусора. С большой вероятностью они будут существовать всё время жизни приложения.

## Как выделяется память в Small Object Heap

Экземпляры новых объектов создаются в нулевом поколении. Если памяти недостаточно в нулевом поколении недостаточно, выполняются следующие шаги. При успешном выполнении шага, следующий не выполняется.
1. Выполняется поиск доступного сегмента памяти. Если его нет, то CLR пытается расширить нулевое поколение за счет зарезервированной памяти.
2. Начинается сборка мусора. 
3. Если доступной памяти нет, бросается `OutOfMemoryException`, и приложение завершается.

## Зачем нужен Large Object Heap (LOH)? 

**LOH** - это специальная область памяти для хранения больших объектов более 85000 байт и массивы `double` размером больше 1000 элементов.  
LOH нужна для повышения производительности при работе с большим объектами в памяти.

### Чем Large Object Heap отличается от Small Object Heap (SOH)?

- В LOH нет поколений.  
- LOH не сжимается автоматически для повышения производительности. Т. е. отсутствует стратегия Compact при сборке мусора: двигать большие объекты дорого. Можно выполнить сжатие вручную.
- Отсутствует фаза планирования. В общем случае используется стратегия Sweep, а при ручном запуске Compact явно указывается, что нужно сделать сжатие, фаза планирования не нужна.

# Что такое сборщик мусора?

**Сборщик мусора** - это инструмент для автоматического управления памятью, управляемый CLR.  

## Как работает сборщик мусора?

1. Запускается сборка мусора
2. Все управляемые потоки встают на паузу (для non-concurrent GC)
3. Поток, который вызвал GC запускает процедуру сборки
4. Выбирается поколение: на основе различных условий выбирается поколение для очистки
5. Маркирока: GC помечает все достижимые объекты текущего и всех более "молодых" поколений
6. Планирование: GC вычисляет, хватит ли Sweep, или нужен Compact.
7. Восстанавливается работа всех потоков

После выбора поколения на шаге 4 работают следующие правила:
1. При срабатывании триггера 0 поколения будет собрано только оно. Нулевое поколение собирается быстрее всего, так как в нем находятся только новые объекты.
2. При срабатывании триггера 1 поколения, будут собраны 0 и 1 поколения. Подразумевается, что 1 поколение более тяжеловесное и будет собираться дольше, чем 0. Поэтому влияние сборки 0 поколения будет незначительным.
3. При срабатывании триггера 2 поколения, будут собраны все поколения.

### Какие причины сборки мусора существуют?

Основные причины запуски сборка мусора:
- Закончилась свободная память в Small Object Heap
- Закончилась свободная память в Large Object Heap
- Ручной вызов GC

Существуют и другие причины, но для прохождения собеседования их знать не обязательно.

### Какие фазы сборки мусора существуют?

Существует три фазы сборки мусора
- Маркировка
- Планирование
- Сборка

#### Маркировка

Сборщик мусора проверяет наличие в куче неиспользуемых приложе­нием объектов, чтобы освободить занятую ими память. Неиспользуемым считается объект, на который не осталось ссылок из корней (roots) приложения. 

**Корнем** называется адрес указателя на объект ссылочного типа: 
- Локальные переменные метода
- Параметры метода
- Статические поля типов
- Корни из очереди финализации (Finalization roots)
  
Корнем могут быть переменные только ссылочного типа.

При запуске сборщик мусора считает, что все объекты в куче - мусор. То есть на них нет никаких ссылок из корней. 

Сборщик проходит по стеку потока и проверяет все корни. Если существует корень, который ссылается на объект, в Type Handle этого объекта проставляется бит. Это и есть признак маркировки объекта.  
Если в маркированный объект ссылается на другой объект в куче, он тоже маркируется. GC узнает о ссылках на другие объекты через таблицу виртуальных методов, на которую ссылается Type Handle объекта в куче.  
Сборщик пропускает объект, если он уже промаркирован. 

После выполнения маркировки сборщик "знает", на какие объекты есть ссылки из корней приложения.

#### Планирование 

После фазы маркировки GC необходимо выбрать стратегию сборки мусора:
- **Sweep Collection**: все недостижимые объекты поколения считаются свободным местом. Все достижимые объекты становятся более старшим поколением путем сдвига границы поколений.
- **Compact Collection**: все достижимые объекты поколения уплотняются, занимая места недостижимых объектов и становятся более старшим поколением. По сути, сдвигаются влево.

В фазу планирования выполняеся виртуальная сборка мусора, объединяющая обе стратегии. Это возможно, так как объекты промаркированы. В объекты, подлежащие сборке, записываются данные о смещении соседнего немаркированного объекта в случае Compact на основании известных размеров объектов. Таким образом в результате работы виртуальной сборки мусора известно значение фрагментации в случае Sweep, а так же данные о сжатии в случае Compact.

Если фрагментация памяти после Sweep будет высокой, то выбирается Compact. Иначе - Sweep.  

#### Сборка

На основе выбранной стратегии выполняется сборка мусора, а именно физический коммит результатов работы виртуальной сборки мусора предыдущей фазы. 

##### Очистка (Sweep collection)

Сборщик обходит кучу линейно и очищает немаркированные объекты.

##### Сжатие (Compact collection)

Сборщик проходит кучу линейно и ищет непрерывные блоки немаркированных объектов. Небольшие блоки пропускаются, а в больших непрерывных блоках маркированные объекты перемещаются влево, сжимая кучу. При этом правая граница вышестоящего поколения сдвигается вправо.  

Сдвиг объектов в памяти делает недействительными их адреса, поэтому сборщик должен проверить все корни и обновить их, чтобы они ссылались на новые. Если объект содержит поле, указывающее на другой перемещенный объект, сборщик должен исправить и их.

### Режимы работы

Достаточно знать, что существует два режима работы GC: **Workstation** и **Server**. У каждого режима есть concurrent и non-concurrent подрежимы.

В режиме Workstation уменьшается время простоя при сборке мусора, а сборщик срабатывает чаще.
В режиме Server сборщик срабатывает реже. За счет этого растет пропускная способность приложения и использование памяти.

# Что такое финализатор (Finalizer)?

**Финализатор** - это инструмент, который позволят освобождать неуправляемые ресурсы. 
**Неуправляемый ресурс** - это ресурс, который использует неуправляемую память. Например, файловый дескриптор, соединение с базой данных, tcp соединение и т. д.

Если объект содержит в себе член, который является неуправляемым ресурсом, GC не сможет собрать этот объект, пока ресурс не будет освобожден. Это приведет к утечкам памяти, потому что экземпляры объектов не будут собираться из GC.

Для того, чтобы объявить финализатор нужно использовать следующий синтаксис:
```csharp
public class MyClass
{
    ~MyClass()
    {
        // Логика по освобождению неуправляемых ресурсов
    }
}
```

Используется имя класса с ~ в начале.

## Как работает финализатор?

Финализация выполняется после сборки мусора. 

При создании объекта, реализующего финализатор, через оператор `new` указатель но объект помещается в список финализации (finalization list) - внутреннюю структуру данных, управляемую сборщиком мусора. Каждая запись из списка указывает на объект, для которой нужно вызвать метод финализации, перед освобождения занятой им памяти.

              Корни приложения 
            /                \    
        || obj1 | obj2, obj3, obj4, obj5, obj6 || - Управляемая куча
                                \        /
        Список финализации - || obj4, obj6 ||    ||   || - Очередь финализации
                                 
Сборщик мусора определяет, что объекты obj2, obj3, obj5, obj6 - это мусор, так как на них не ссылаются корни приложения. Далее выполняется поиск указателя на эти объекты в списке финализации. Найденные указатели удаляются из списка финализации и перемещается в очередь финализации (freachable queue), которая управляется CLR.

                        Корни приложения 
                        /      |              
                    || obj1 | obj4, obj6 || - Управляемая куча
                              /        \
    Список финализации - || obj4 ||  || obj6 || - Очередь финализации

obj2, obj3, obj5 были удалены, так как на них нет ссылок из корней приложения, и у них нет финализаторов. Память объекта obj6 освободить нельзя, так как его метод финализации еще не вызывался. После перемещения указателя из списка финализации в очередь финализации этот объект перестает считаться мусором и сборщик мусора не может его удалить. Как будто на него ссылается корень приложения.

После помещения объектов в очередь финализации специальный высокоприоритетный поток, выделенный CLR, активизируется и последовательно удаляет элементы из очереди, вызывая методы финализации. При пустой очереди финализации этот поток бездействует. Такой поток существует в единственном экземпляре для того чтобы предотвратить возможные проблемы синхронизации.

При следующем проходе сборщика мусора объекты, которые были финализированы из очереди, становятся мусором. Поэтому сборщик мусора просто удаляет их. **Важно понимать, что для сборки финализируемых объектов необходимо минимум 2 прохода сборщика мусора: первый для помещения в очередь финализации, второй для сборки финализированного объекта.**

                        Корни приложения 
                        /      |              
                    || obj1 | obj4 || - Управляемая куча
                              /        
    Список финализации - || obj4 ||  ||  || - Очередь финализации

# Что такое паттерн Disposable? В чем отличие от финализатора?
  
Паттерн `Disposable` служит той же цели, что и финализатор, но позволяет управлять моментом, когда неуправляемые ресурсы будут освобождены.
Для его реализации существует интерфейс `IDisposable` и его асинхронный аналог `IAsyncDisposable`:
```csharp
/// <summary>Provides a mechanism for releasing unmanaged resources.</summary>
public interface IDisposable
{
  /// <summary>Performs application-defined tasks associated with freeing, releasing, or resetting unmanaged resources.</summary>
  void Dispose();
}

/// <summary>Provides a mechanism for releasing unmanaged resources asynchronously.</summary>
public interface IAsyncDisposable
{
  /// <summary>Performs application-defined tasks associated with freeing, releasing, or resetting unmanaged resources asynchronously.</summary>
  /// <returns>A task that represents the asynchronous dispose operation.</returns>
  ValueTask DisposeAsync();
}
```

Когда тип реализует `IDisposable` в реализации метода `Dispose` должна быть реализована логика освобождения неуправляемых ресурсов.
```csharp
public class MyGreatClass : IDisposable
{
    private IDbConnection _connection;

    public Dispose()
    {
        // Закрываем соединение, если оно не закрыто
        if(_connection.State != ConnectionState.Closed)
        {
            _connection.Close();
        }        
        
        // Освобождаем ресурсы соединения с БД
        _connection.Dispose();
    }
}
```

Существует комбинированный метод освобождения ресурсов, когда используют и финалайзер и Disposable паттерн.
```csharp
public class MyGreatClass: IDisposable
{
    private bool _disposed = false;

    private IDbConnection _connection;

    public void Dispose()
    {
        // Освобождаем неуправляемые ресурсы. 
        // При вызове Dispose(bool disposing) из метода Dispose() передается аргумент true, который указывает на то, что освобождение ресурсов выполняется разработчиком.
        // Это означает, что может быть необходимость очистить управляемые ресурсы внутри класса MyGreatClass. Для этого в методе Dispose(bool disposing) есть блок if(disposing).
        Dispose(true);
        // Подавляем финализацию экземпляра. После вызова этого метода финализатор не будет вызван.
        GC.SuppressFinalize(this);
    }
 
    // В случае вызова из Dispose() метод освобождает управляемые и неуправляемые ресурсы
    // В случае вызова из ~MyGreatClass() метод освобождает только неуправляемые ресурсы
    protected virtual void Dispose(bool disposing)
    {   
        // Ничего не делаем, если уже был выполнен вызов метода Dispose();
        if (_disposed) 
            return;
        
        if (disposing)
        {
            // Освобождаем управляемые ресурсы
        }
        
        // Освобождаем неуправляемые ресурсы:
        // Закрываем соединение, если оно не закрыто
        if(_connection.State != ConnectionState.Closed)
        {
            _connection.Close();
        }        
        
        // Освобождаем ресурсы соединения с БД
        _connection.Dispose();

        // Проставляем флаг, что Dispose() уже был выполнен.
        disposed = true;
    }
 
    // Финалайзер
    ~MyGreatClass()
    {
        // При вызове Dispose(bool disposing) из финалайзера передается аргумент false, который указывает на то, что освобождение ресурсов выполняется средой выполнения.
        // Это означает, что выполнять очистку управляемых ресурсов не нужно, потому что они уже могут быть уничтожены. Например, зависимые объекты собраны сброщиком мусора. 
        // Поэтому такой метод выполняет освобождение только неуправляемых ресурсов.
        Dispose(false);
    }
}
```
Главное преимущество такого подхода заключается в том, что если разработчик забудет вызвать `Dispose()` на экземпляре объекта, очистка все равно будет выполнена, так как есть финалайзер.

## Для чего нужно ключевое слово using?

**using** - это синтаксический сахар для упрощения использования паттерна Disposable.

using разворачивается компилятором в блок try - finally. 
```csharp
using(var disposableObject = new DisposableObject())
{
    // Какая-то логика
}
``` 
Код выше эквивалентен следующему
```csharp
DisposableObject disposableObject = new DisposableObject();
try
{
    // Какая-то логика
}
finally
{
    if (disposableObject != null)
    {
        ((IDisposable)disposableObject).Dispose();
    }
}
```
