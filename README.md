# Async-Programming
 - It allows multiple tasks to run at the same time, and the programmer can often manage these tasks directly. It allows programs to continue to run even after you start a specific action
# Async Method
<pre>

public async Task<int> DoSomethingAsync()
{
    var result = await CallDependencyAsync();
    return result + 1;
}
</pre>  
- async : The async keyword indicates that the method contains asynchronous operations (CallDependencyAsync()) and can use the await keyword inside its body. It allows the method to be suspended asynchronously, enabling the calling thread to continue executing other tasks while waiting for asynchronous operations to complete.

- Task<int>: This is the return type of the method. It indicates that the method returns a Task<int> object, which represents an asynchronous operation that produces a result of type int. The Task<T> type is a .NET framework class that represents a single operation that produces a value of type T. In this case, Task<int> represents an asynchronous operation that produces an integer result.
# Async void
- Method would execute asynchronously and without return type because there is no return value.
- it is bad because errors occur in the subthread without notifying the main thread, which causes the program to terminate.
- you should use “async task” instead of “async void” to avoid the above problems.
- Refer to this article : https://medium.com/c-sharp-progarmming/the-problem-with-async-void-9f40b613cba
# Prefer Task.FromResult over Task.Run for pre-computed or trivially computed data
- trivially computed data : refers to a result that can be easily and quickly calculated without requiring significant computational effort or external dependencies. In other words, it's a value that can be computed almost instantaneously using basic arithmetic operations or simple logic.
<pre>
  public class MyLibrary
{
   public Task<int> AddAsync(int a, int b)
   {
       return Task.FromResult(a + b);
   }
}
</pre>

 This example uses a ValueTask<int> to return the trivially computed value. It does not use any extra threads as a result. It also does not allocate an object on the managed heap :
 <pre>
   public class MyLibrary
{
   public ValueTask<int> AddAsync(int a, int b)
   {
       return new ValueTask<int>(a + b);
   }
}
 </pre>
# Avoid using Task.Run for long-running work
- Stealing a thread-pool thread for long-running work is bad since it takes that thread away from other work that could be done (timer callbacks, task continuations, etc).
- create thread manually :
  
  <pre>
  var thread = new Thread(ProcessQueue);
  </pre>
- Use a TaskFactory with TaskCreationOptions.LongRunning instead of creating a thread manually :
  
  <pre>
   public Task StartProcessing() => Task.Factory.StartNew(ProcessQueue, TaskCreationOptions.LongRunning);
  </pre>
