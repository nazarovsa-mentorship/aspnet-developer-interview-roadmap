# Асинхронное программирование

# Для чего нужно асинхронное программирование?

Асинхронное программирование позволяет эффективно использовать потоки при работе с IO bound операциями: запись на диск, взаимодействие по сети, обращение к БД и т. д. IO bound операции в отличии от CPU bound зависят от "производительности" (скорость чтения с диска, скорость взаимодействия по сети и т. д.) внешнего ресурса, поэтому нельзя контроллировать время, которое понадобится на их выполнение. 
При обращении к таким ресурсам в синхронном режиме вызывающий поток ожидает возвращения управления, а значит простаивает. Асинхронные вызовы позволяют вернуть ожидающий поток в пул потоков, чтобы он мог быть использован для выполнения полезной работы.

# Для чего нужны ключевые слова async/await?

Ключевое слово async нужно чтобы пометить метод как асинхронный. 
Ключевое слово await нужно чтобы ожидать выполнения асинхронного метода в асинхронном режиме. Может быть использовано только внутри метода помеченного async.

# Во что разворачивается async?

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

# Для чего нужен класс Task?

`Task` абстрагирует асинхронную задачу и её выполнение. `Task` позволяет проще и эффективнее работать с потоками по сравнению с ручным управлением. `Task` выполняется на потоке из `ThreadPool`. У `Task` есть статус, по которому можно определить ее состояние. 

# Что происходит при создании Task? Откуда берутся потоки?

При создании таски берется свободный поток из пула потоков, на который она себя планирует. Если свободного потока нет, то CLR попробует создать его. Если лимит доступных потоков исчерпан, то таска будет ждать пока не появится свободный поток.

# Что происходит с вызывающим потоком, когда он доходит до await?

Он возвращается в пул потоков. При этом таска выполняется на одном из потоков, взятых из пула потоков. После выполнения асинхронной задачи управление возвращается коду, который вызвал её. Он продолжит свое выполнение в случайном потоке, взятом из пула потоков.

# Можно ли гарантировать, что код продолжит выполнение в том же потоке, в котором начал, после того, как успешно отработал await?

Нет. Но можно использовать метод `ConfigureAwait()` с параметром `true`, которые сигнализирует о том, что выполнение стоит продолжить в том же потоке.

```csharp
var result = await DoAsync().ConfigureAwait(true);
```

В ASP.NET приложениях `ConfigureAwait(true)` не работает, так как там нет контекста синхронизации.

# Для чего нужен метод Task.WhenAll? Можно ли ожидать выполнение нескольких Task, выполняющихся параллельно?

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

# Можно ли выполнить/запустить синхронный код асинхронно?

Да. Это можно сделать с помощью `Task.Run` или `Task.Factory.StartNew`.  
`Task.Factory.StartNew` предоставляет расширенные возможности по запуску асинхронных задач за счет перегрузок. Например, можно указать тип таски.
```csharp
Task.Run(() => Console.WriteLine("Hello"));  
 
Task.Factory.StartNew(() => Console.WriteLine("World"));
```

# Как отменить запущенную Task

Для отмены `Task` следует использовать `CancellationToken`.

# Практическое задание

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