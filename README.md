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
- When reaching the line " await CallDependencyAsync() " The DoSomethingAsync method is temporarily suspended at this point, and control is returned to the caller of DoSomethingAsync (e.g main).
- Once the asynchronous operation initiated by CallDependencyAsync completes, the method resumes execution and returns the result to the caller.
# Async void
- Using async void in ASP.NET Core applications is like sending a message without expecting a reply. It's risky because if something goes wrong (like an exception), there's no way to handle it properly, and it can crash the whole process.

- Imagine you're sending a paper airplane with a message written on it. You throw it and hope it reaches its destination. But if the airplane gets lost or crashes, there's no way to know what happened or fix it.

- Similarly, with async void, you're starting an operation (like sending a message) without waiting for it to finish or handling any errors that might occur along the way. If something goes wrong, it's like the paper airplane crashing – the program crashes, and you're left without any way to recover or fix the problem.
- Even if you tried to handle exceptions, The error can occur in the subthread without notifying the main thread, which causes the program to terminate.
- it's best to avoid using async void in ASP.NET Core applications because it's risky and can lead to unexpected crashes. It's better to use "async Task" instead, Task-returning methods are better since unhandled exceptions trigger the TaskScheduler.UnobservedTaskException.
- Typically, Async void is used when developers are trying to implement "fire-and-forget" patterns triggered by a controller action.
   ## Fire-and-forget
   - it's like sending a message or starting a task without caring about what happens afterward. You "fire" off the task, and then you "forget" about it – you don't wait for it to finish or check if it was successful.
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


