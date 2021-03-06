---
title: Практическое руководство. Неактивная инициализация объектов
ms.date: 03/30/2017
dev_langs:
- csharp
- vb
helpviewer_keywords:
- lazy initialization in .NET, how to perform
ms.assetid: 8cd68620-dcc3-4f20-8835-c728a6820e71
author: rpetrusha
ms.author: ronpet
ms.openlocfilehash: 9d33dcb2b060d1d453ae17a48d2765d489de0038
ms.sourcegitcommit: 3d5d33f384eeba41b2dff79d096f47ccc8d8f03d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33394479"
---
# <a name="how-to-perform-lazy-initialization-of-objects"></a>Практическое руководство. Неактивная инициализация объектов
Класс <xref:System.Lazy%601?displayProperty=nameWithType> упрощает выполнение отложенной инициализации и создание экземпляров объектов. Если объекты не требуются, то при отложенной инициализации их можно не создавать или отложить их инициализацию до первого обращения к ним. Дополнительные сведения см. в статье [Отложенная инициализация](../../../docs/framework/performance/lazy-initialization.md).  
  
## <a name="example"></a>Пример  
 В следующем примере показано, как инициализировать значение с <xref:System.Lazy%601>. Предположим, что отложенная переменная может не потребоваться, в зависимости от какого-либо другого кода, задающего переменной `someCondition` значение true или false.  
  
```vb  
Dim someCondition As Boolean = False  
  
Sub Main()  
    'Initializing a value with a big computation, computed in parallel  
    Dim _data As Lazy(Of Integer) = New Lazy(Of Integer)(Function()  
                                                             Dim result =  
                                                                 ParallelEnumerable.Range(0, 1000).  
                                                                 Aggregate(Function(x, y)  
                                                                               Return x + y  
                                                                           End Function)  
                                                             Return result  
                                                         End Function)  
  
    '  do work that may or may not set someCondition to True  
    ' ...  
    '  Initialize the data only if needed  
    If someCondition = True Then  
  
        If (_data.Value > 100) Then  
  
            Console.WriteLine("Good data")  
        End If  
    End If  
End Sub  
```  
  
```csharp  
  static bool someCondition = false;    
  //Initializing a value with a big computation, computed in parallel  
  Lazy<int> _data = new Lazy<int>(delegate  
  {  
      return ParallelEnumerable.Range(0, 1000).  
          Select(i => Compute(i)).Aggregate((x,y) => x + y);  
  }, LazyExecutionMode.EnsureSingleThreadSafeExecution);  
  
  // Do some work that may or may not set someCondition to true.  
  //  ...  
  // Initialize the data only if necessary  
  if (someCondition)  
{  
    if (_data.Value > 100)  
      {  
          Console.WriteLine("Good data");  
      }  
}  
```  
  
## <a name="example"></a>Пример  
 В следующем примере показано, как использовать класс <xref:System.Threading.ThreadLocal%601?displayProperty=nameWithType> для инициализации типа, видимого только текущему экземпляру объекта в текущем потоке.  
  
 [!code-csharp[CDS#13](../../../samples/snippets/csharp/VS_Snippets_Misc/cds/cs/cds2.cs#13)]
 [!code-vb[CDS#13](../../../samples/snippets/visualbasic/VS_Snippets_Misc/cds/vb/lazyhowto.vb#13)]  
  
## <a name="see-also"></a>См. также  
 <xref:System.Threading.LazyInitializer?displayProperty=nameWithType>  
 [Отложенная инициализация](../../../docs/framework/performance/lazy-initialization.md)
