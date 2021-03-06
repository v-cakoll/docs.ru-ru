---
title: Безопасность обработки исключений
description: См. статью как обеспечить безопасность обработки исключений в коде .NET. Просмотрите порядок, в котором выполняется код при наличии инструкций try, EXCEPT, catch и finally.
ms.date: 03/30/2017
dev_langs:
- cpp
helpviewer_keywords:
- code security, exception handling
- security [.NET Framework], exception handling
- secure coding, exception handling
- exception handling, security
ms.assetid: 1f3da743-9742-47ff-96e6-d0dd1e9e1c19
ms.openlocfilehash: 73597f83d7236cd48a18a891c987b4f5d7e1723d
ms.sourcegitcommit: 0fa2b7b658bf137e813a7f4d09589d64c148ebf5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2020
ms.locfileid: "86309044"
---
# <a name="securing-exception-handling"></a>Безопасность обработки исключений
В Visual C++ и Visual Basic выражение фильтра, расположенное дальше вверх по стеку, выполняется перед любой `finally` инструкцией. Блок **catch** , связанный с этим фильтром, выполняется после `finally` инструкции. Дополнительные сведения см. [в разделе Использование исключений с пользовательской фильтрацией](../../standard/exceptions/using-user-filtered-exception-handlers.md). В этом разделе рассматриваются аспекты безопасности в этом порядке. Рассмотрим следующий пример псевдокода, демонстрирующий порядок выполнения инструкций и инструкций фильтра `finally` .  
  
```cpp  
void Main()
{  
    try
    {  
        Sub();  
    }
    except (Filter())
    {  
        Console.WriteLine("catch");  
    }  
}  
bool Filter () {  
    Console.WriteLine("filter");  
    return true;  
}  
void Sub()
{  
    try
    {  
        Console.WriteLine("throw");  
        throw new Exception();  
    }
    finally
    {  
        Console.WriteLine("finally");  
    }  
}
```  
  
 Этот код выводит следующие фрагменты кода.  
  
```output
Throw  
Filter  
Finally  
Catch  
```  
  
 Фильтр выполняется перед `finally` инструкцией, поэтому проблемы безопасности могут быть введены любым, что вносит изменения в состояние, когда выполнение другого кода может воспользоваться преимуществами. Пример:  
  
```cpp  
try
{  
    Alter_Security_State();  
    // This means changing anything (state variables,  
    // switching unmanaged context, impersonation, and
    // so on) that could be exploited if malicious
    // code ran before state is restored.  
    Do_some_work();  
}
finally
{  
    Restore_Security_State();  
    // This simply restores the state change above.  
}  
```  
  
 Этот псевдокод позволяет использовать фильтр выше в стеке для выполнения произвольного кода. Другие примеры операций, которые будут иметь подобный результат, — это временное олицетворение другого удостоверения, установка внутреннего флага, который обходит некоторую проверку безопасности, или изменение языка и региональных параметров, связанных с потоком. Рекомендуемым решением является ввод обработчика исключений для изоляции изменений кода от блоков фильтра вызывающих объектов. Однако важно правильно ввести обработчик исключений, иначе эта проблема не будет исправлена. В следующем примере переключается язык и региональные параметры пользовательского интерфейса, но любой тип изменения состояния потока может быть аналогичным.  
  
```cpp  
YourObject.YourMethod()  
{  
   CultureInfo saveCulture = Thread.CurrentThread.CurrentUICulture;  
   try {  
      Thread.CurrentThread.CurrentUICulture = new CultureInfo("de-DE");  
      // Do something that throws an exception.  
}  
   finally {  
      Thread.CurrentThread.CurrentUICulture = saveCulture;  
   }  
}  
```  
  
```vb  
Public Class UserCode  
   Public Shared Sub Main()  
      Try  
         Dim obj As YourObject = new YourObject  
         obj.YourMethod()  
      Catch e As Exception When FilterFunc  
         Console.WriteLine("An error occurred: '{0}'", e)  
         Console.WriteLine("Current Culture: {0}",
Thread.CurrentThread.CurrentUICulture)  
      End Try  
   End Sub  
  
   Public Function FilterFunc As Boolean  
      Console.WriteLine("Current Culture: {0}", Thread.CurrentThread.CurrentUICulture)  
      Return True  
   End Sub  
  
End Class  
```  
  
 Правильным исправлением в этом случае является заключение существующего блока **try** / **finally** в блок **try** / **catch** . Простое внедрение предложения **catch-throw** в существующий блок **try** / **finally** не устраняет проблему, как показано в следующем примере.  
  
```cpp  
YourObject.YourMethod()  
{  
    CultureInfo saveCulture = Thread.CurrentThread.CurrentUICulture;  
  
    try
    {  
        Thread.CurrentThread.CurrentUICulture = new CultureInfo("de-DE");  
        // Do something that throws an exception.  
    }  
    catch { throw; }  
    finally
    {  
        Thread.CurrentThread.CurrentUICulture = saveCulture;  
    }  
}  
```  
  
 Это не решает проблему, так как `finally` инструкция не выполнялась до `FilterFunc` управления доступом.  
  
 Следующий пример устраняет проблему, гарантируя, что `finally` предложение выполнялось до того, как будет предложено исключение в блоках фильтра исключений вызывающих объектов.  
  
```cpp  
YourObject.YourMethod()  
{  
    CultureInfo saveCulture = Thread.CurrentThread.CurrentUICulture;  
    try
    {  
        try
        {  
            Thread.CurrentThread.CurrentUICulture = new CultureInfo("de-DE");  
            // Do something that throws an exception.  
        }  
        finally
        {  
            Thread.CurrentThread.CurrentUICulture = saveCulture;  
        }  
    }  
    catch { throw; }  
}  
```  
  
## <a name="see-also"></a>См. также раздел

- [Правила написания безопасного кода](../../standard/security/secure-coding-guidelines.md)
