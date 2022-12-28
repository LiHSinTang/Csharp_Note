# C# 資源釋放Dispose實現
## 快捷code
Dispose格式：
```csharp=
class DisposeExampleClass : IDisposable
{
    bool _disposedValue = false;
    
    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this);
    }
    
    protected virtual void Dispose(bool disposing)
    {
        if (!_disposedValue)
        {
            if (disposing)
            {
                // TODO: dispose managed state (managed objects)
                managed.Dispose();
            }

            // TODO: free unmanaged resources (unmanaged objects) and override finalizer
            
            // TODO: set large fields to null

            _disposedValue = true;
        }
    }
    ~DisposeExampleClass()=>Dispose(fales);
}
```
例如：
```csharp=
class DisposeExampleClass : IDisposable
{
    
    managetype managed = new managetype(); // 託管的資源
    unmanagetype unmanaged = new unmanaged(); //非託管的資源
    string[] largeString = new string[10000]; // 超大型資源
    
    bool _disposedValue = false;
    
    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this);
    }
    
    protected virtual void Dispose(bool disposing)
    {
        if (!_disposedValue)
        {
            if (disposing)
            {
                // TODO: dispose managed state (managed objects)
                managed.Dispose();
            }

            // TODO: free unmanaged resources (unmanaged objects) and override finalizer
            SomeFreeSourceFunction(unmanageed);
            // TODO: set large fields to null
            largeString = null;
            _disposedValue = true;
        }
    }
    ~DisposeExampleClass()=>Dispose(fales);
}
```
using使用：
```csharp=
(using SomeImplementIDisposableClass x = new SomeImplementIDisposableClass())
```
等同：
```csharp=
try
{
    SomeImplementIDisposableClass x = new SomeImplementIDisposableClass()
        
        //Do something
}
finally
{
    x.Dispose();
}
```
藉由實作`IDisposable`的介面(interface)，除了可以讓該`class`在被呼叫`Dispose`方法時，清除該`class`希望被清除的引用(對，清除的是引用而不是內存，C# 清除內存和資源是靠`GC`來去管理的)，還可以藉由`using`的語法糖來去做到將範圍內的程式結束後自動呼叫`Dispose`，例如：寫入檔案的`StreamWriter`在使用完後需要呼叫close來關閉釋放資源，如下：

```csharp=
//Pass the filepath and filename to the StreamWriter Constructor
StreamWriter sw = new StreamWriter("C:\\Test.txt");
//Write a line of text
sw.WriteLine("Hello World!!");
//Close the file
sw.Close();
```
使用`using`語法：
```csharp=
using (StreamWriter sw = new StreamWriter("C:\\Test.txt"))
{
    sw.WriteLine("Hello World!!");
}
```
可以代換
```csharp=
try
{
    //Pass the filepath and filename to the StreamWriter Constructor
    StreamWriter sw = new StreamWriter("C:\\Test.txt");
    //Write a line of text
    sw.WriteLine("Hello World!!");
}
finally
{
    sw.Close();
}
```
C# 的資源分成兩種，託管(managed)和非託管(unmanaged)資源,
* 託管資源：由CLR管理分配和释放的资源，即由CLR里new出来的对象(簡單來說就是C#自己管理的資源如int、string、創建的class、有建立良好的dispose的class等等。
* 非託管資源：不受CLR管理的对象，windows内核对象，如文件、数据库连接、套接字、COM对象等(簡單來說就是會需要用到其他的IO或API的資源)。

若使用到了非託管的資源，或想要主動釋放掉託管的資源時就要實作`IDisposable`的介面實現Dispose模式。若使用了非託管的資源後沒有實作`
Dispose`,就會造成程式關閉後調用的外部資源卻沒有關掉，造成資源洩漏。

實作Dispose模式的格式分成三個區塊`Dispose()`、`Dispose(bool)`、`Finalize`。
### 首先繼承`IDisposable`
要使用dispose首先要繼承`IDisposable`介面,假設創建一個`DisposeExampleClass`為例：
```csharp=
class DisposeExampleClass : IDisposable
```
### Dispose()
## 待續
## 參考
* [*改善C#程序的建议4：C#中标准Dispose模式的实现*](https://www.cnblogs.com/luminji/archive/2011/03/29/1997812.html)
* [*从GC的SuppressFinalize方法带你深刻认识Finalize底层运行机制*](https://www.cnblogs.com/huangxincheng/p/12811291.html)
* [*C# 里的 dispose 的作用是什么？*](https://www.zhihu.com/question/329169960)
* [*Implement a Dispose method*](https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/implementing-dispose)
* [*C#中Dispose和Close的區別*](https://sites.google.com/site/willsnote/Home/c%E4%B8%ADdispose%E5%92%8Cclose%E7%9A%84%E5%8D%80%E5%88%A5)
* [改善C#程序的建议5：引用类型赋值为null与加速垃圾回收](https://www.cnblogs.com/luminji/archive/2011/04/07/2007205.html)
* 
