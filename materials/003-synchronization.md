# Примитивы синхронизации

# Для чего нужны примитивы синхронизации?

Примитивы синхронизации нужны для организации безопасного доступа к ресурсам в многопоточных приложениях. Например, когда доступ на чтение и запись к статическому полю класса осуществляется из разных потоков. Данные могут прийти в неконсистентное состояние, если не обеспечить безопасный доступ к ним.

# Что такое lock? Для чего нужен?

**lock** - это обертка над `Monitor` позволяющая работать с ним просто.  

`lock` предоставляет монопольный доступ на блок кода. Другими словами, если блокировка взята одним потоком, другой поток не начнет выполнение блока кода, помещенного в lock, пока поток взявший блокировку не отпустит ее. Отпускание блокировки - это завершение выполнения кода потоком, взявшим ее.

```csharp
private readonly object _lockObject = new object();

public void Method()
{
    // 3. Поток 2 зашел в Method. Поток 2 ждет пока поток 1 не выйдет из секции обернутой в lock, так как блокировка на `_lockObject` взята.

    // 1. Поток 1 зашел и взял блокировку
    lock(_lockObject)
    {
        // 2. Поток 1 засыпает на 10 секунд
        Thread.Sleep(10_000);
    }
}
```
Популярный кейс для использования `lock` - создание синглтона. Синглтон - это класс, экземпляр которого может существовать в единственном числе.
```csharp
csharp
public class Singleton
{
    // Приватное статическое поле для хранения единственного экземпляра
    private static Singleton _instance;
    
    // Объект для синхронизации потоков
    // readonly предотвращает случайное изменение объекта блокировки
    private static readonly object _lock = new object();
    
    // Приватный конструктор запрещает создание экземпляров извне класса
    private Singleton() 
    { 
        // Инициализация синглтона
    }
    
    public static Singleton Instance
    {
        get
        {
            // Первая проверка (без блокировки)
            // Если экземпляр уже создан, возвращаем его без затрат на блокировку
            if (_instance == null)
            {
                // Блокировка для синхронизации потоков
                // Только один поток может войти в этот блок одновременно
                lock (_lock)
                {
                    // Вторая проверка (с блокировкой)
                    // Нужна, потому что пока первый поток получал блокировку,
                    // другой поток мог уже создать экземпляр
                    if (_instance == null)
                    {
                        // Создание экземпляра
                        _instance = new Singleton();
                    }
                }
            }
            // Возвращаем единственный экземпляр
            return _instance;
        }
    }
}
```
Обрати внимание на две проверки `_instance == null`. Это часто проверяют в задачах на код-ревью: одну проверку опускают, что может привести к неконсистентности данных. Например, перетиранию уже созданного экземпляра `_instance`.


Также `lock` используется для изменения ресурсов. Например, потокобезопасного изменения члена класса:
```csharp
public class LockSample 
{
    private readonly object _lockObject = new object();
    private int _sharedResource;

    public void UpdateResource()
    {
        lock (_lockObject) 
        {
            _sharedResource++;
        }
    }
}
```
Этот код гарантирует, что в рамках экземпляра класса `LockSample` (`_lockObject` не статический), будет обновляться только одним потоком единовременно.


`lock` использует индекс блока синхронизации ссылочного типа для захвата блокировки. Поэтому если вы хотите обеспечить блокировку секции в рамках всего приложения делайте объект синхронизации статичным.

## Во что разворачивается lock

`lock` разворачивается в `Monitor` на этапе компиляции.  

```csharp
private readonly object _lockObject = new object();

public void Method()
{
    lock(_lockObject)
    {
        // Критическая секция
    }
}
``` 

Будет развернут в 
```csharp
private readonly object _lockObject = new object();

public void Method()
{
    bool lockTaken = false;
    try
    {
        Monitor.Enter(_lockObject, ref lockTaken);
        // Критическая секция
    }
    finally
    {
        if(lockTaken)
        {
            Monitor.Exit(_lockObject);
        }
    }
}
```

## Можно передать в lock значимый тип?

Нет. Будет ошибка компиляции.

Но можно передать значимый тип в `Monitor`. Это приведет к том, что экзепляр значимого типа упакуется в объект. При упаковке значимого типа всегда создается новый объект, поэтому блокировка не будет работать в виду того, что она будет каждый раз захватываться на новый объект.

Убедиться в этом можно проанализировав IL код по [ссылке](https://sharplab.io/#v2:C4LglgNgNAJiDUAfAAgBgATIIwDoAqAFgE4CmAhjGAHYDmA3ALABQzyAzOgM7kQkyYAmTFgDszAN7N06AA5EwANzLAS6UhQD2VCAE90GgEYArEgGNg6APoQNpgNYB5Y2YsBedFRIB3fc/MAKAEpGFiZpdkwAFnQAWRJgAg0YIKl0STDpdAMNDQh0G3s8MjsSKnR3ADMyCG4QzPRgIh1U6XT66RitMGANIhwAUSoVIn9UKDUSCvzbOyKSqmCWzIB6ZfRALBBAARBADhBAIRBtwHEQQFYQQEEQQC4QQAYQQHkQdBOjs8AxEG2rpYBfJYrqat0ltvawKb+AqzYqlQJLVoQzKdKjdXoDAAe3X81hmThM5kWGXq72x6Fx0lxryAA=).

## Можно ли вызвать await в lock?

Нет. Будет ошибка компиляции.  
Но можно вызвать `await` внутри блока кода захваченного в `Monitor`. Это приведет к deadlock.  
Для упрощения примера представь, что компилятор позволил нам написать `await` внутри `lock` =)  

1. Начальное состояние
```csharp
private readonly object _lock = new object();

// Этот код может привести к deadlock
public async Task DoWorkAsync()
{
    lock (_lock)  // Поток 1 получает блокировку
    {
        await Task.Delay(1000);  // Поток 1 освобождается, но блокировка остается
        // Этот код должен выполниться в Потоке 1 (из-за контекста синхронизации)
        // Но Поток 1 не может получить блокировку, так как она не была освобождена
    }
}
```
2. При встрече `await`:
```csharp
await Task.Delay(1000);
// 1. Поток 1 освобождается для других задач
// 2. НО блокировка Monitor не освобождается
// 3. Запоминается контекст синхронизации (Thread 1)
```
3. После завершения `await`:
```csharp
// 1. Код должен продолжиться в исходном контексте (Поток 1)
// 2. Поток 1 пытается получить блокировку
// 3. Но блокировка все еще удерживается исходным вызовом
// 4. DEADLOCK: Поток не может получить блокировку, которую он сам удерживает
```

Для того, чтобы избежать этой ситуации нужно получать результат выполнения `Task` синхронно. Но предпочтительным решением будет использовать `SemaphoreSlim` для более эффективного использования потоков.
```csharp
private readonly SemaphoreSlim _semaphore = new SemaphoreSlim(1);

public async Task DoWorkAsync()
{
    await _semaphore.WaitAsync();
    try
    {
        await Task.Delay(1000);
    }
    finally
    {
        _semaphore.Release();
    }
}
```


# Что такое Mutex? Для чего нужен?

`Mutex` - аналогичен `Monitor` за исключением того, что он создает блокировку на уровне ядра операционной системы. То есть позволяет использовать блокировки между процессами.  
Самый популярный кейс использования `Mutex` - ограничение количества одновременно запущенных экземпляров приложения. 

```csharp
// Пример ограничения одного экземпляра приложения
static class Program 
{
    static Mutex _mutex;
    
    static void Main()
    {
        // Проверяем существование мьютекса с таким именем
        bool createdNew;
        _mutex = new Mutex(true, "MyAppUniqueName", out createdNew);
        
        if (!createdNew)
        {
            MessageBox.Show("Приложение уже запущено");
            return;
        }
        
        // Запуск приложения
        Application.Run(new Form1());
    }
}
```

Так как `Mutex` - блокировка блокировка уровня операционной системы, она требует больше ресурсов по сравнению с `lock`.

# Что такое Semaphore? Для чего нужен?

**Semaphore (семафор)** - это примитив синхронизации, который используется для ограничения количества одновременных доступов к ресурсу. Другими словами, `Semaphore` позволяет указать количество потоков, которые смогут иметь доступ к блоку кода одновременно. Следует использовать облегченную версию `SemaphoreSlim`, так как она более производительна.

Например, с помощью семафора можно ограничить количество одновременных запросов во внешний сервис
```csharp
public class ApiClient
{
    // Ограничиваем до 10 одновременных запросов
    private static SemaphoreSlim _throttler = new SemaphoreSlim(10);

    public async Task MakeApiRequestAsync()
    {
        // Если в следующем за WaitAsync() коде уже 10 исполняемых потоков, остальные встанут в очередь
        await _throttler.WaitAsync();
        try
        {
            await httpClient.GetAsync("api/endpoint");
        }
        finally
        {
            _throttler.Release();
        }
    }
}
```

# Что такое ReaderWriterLock? Для чего нужен?

**ReaderWriterLock** - это примитив синхронизации, который предназначен для использования в сценариях, когда много операций чтения и мало операций записи. Он позволяет разделять блокировки на чтение и запись. При этом чтение могут выполнять несколько потоков, а запись только один.

Существует облегченная версия `ReaderWriterLockSlim`.

```csharp
public class ThreadSafeCache
{
    private readonly ReaderWriterLockSlim _lock = new ReaderWriterLockSlim();
    private readonly Dictionary<string, string> _cache = new Dictionary<string, string>();

    // Чтение (много потоков одновременно)
    public string Read(string key)
    {
        _lock.EnterReadLock();
        try
        {
            return _cache.TryGetValue(key, out var value) ? value : null;
        }
        finally
        {
            _lock.ExitReadLock();
        }
    }

    // Запись (только один поток)
    public void Write(string key, string value)
    {
        _lock.EnterWriteLock();
        try
        {
            _cache[key] = value;
        }
        finally
        {
            _lock.ExitWriteLock();
        }
    }

    public void Dispose()
    {
        _lock.Dispose();
    }
}
```

# Что такое Interlocked? Для чего нужен?

**Interlocked** - это примитив синхронизации, который позволяет выполнять атомарные операции над примитивными типами без использования блокировок. Он имеет высокую производительность за счет использования атомарных инструкций процессора.

**Может быть использован для увеличение или уменьшения целочисленного значения**
```csharp
public class AtomicCounter
{
    private long _value;

    public void Increment()
    {
        Interlocked.Increment(ref _value); // Атомарно +1
    }

    public void Decrement()
    {
        Interlocked.Decrement(ref _value); // Атомарно -1
    }
}
```

**Для замены значения**
```csharp
public class AtomicValue
{
    private int _value;

    public void SetValue(int newValue)
    {
        // Атомарно заменяет значение и возвращает старое
        int oldValue = Interlocked.Exchange(ref _value, newValue);
    }
}
```

**Для условной замены значения**
```csharp
public class LazyInit
{
    private object _instance;

    public object GetInstance()
    {
        if (_instance == null)
        {
            object newInstance = new object();
            // Замена только если _instance == null
            Interlocked.CompareExchange(ref _instance, newInstance, null);
        }
        return _instance;
    }
}
```

**Для сложения**
```csharp
public class AtomicSum
{
    private long _sum;

    public void AddValue(long value)
    {
        Interlocked.Add(ref _sum, value);
    }
}
```