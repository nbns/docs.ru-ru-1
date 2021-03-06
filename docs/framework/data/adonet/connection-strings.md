---
title: Строки подключения в ADO.NET
ms.date: 03/30/2017
ms.assetid: 745c5f95-2f02-4674-b378-6d51a7ec2490
ms.openlocfilehash: 1e6e2b6870195c99279639e1f4576a30b7126d4d
ms.sourcegitcommit: 69229651598b427c550223d3c58aba82e47b3f82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48583699"
---
# <a name="connection-strings-in-adonet"></a>Строки подключения в ADO.NET
Платформа .NET Framework 2.0 предоставляет новые возможности для работы со строками подключения, включая представление новых ключевых слов для классов построителей строк подключения, упрощающих создание допустимых строк подключения во время выполнения.  
  
Строка подключения содержит сведения для инициализации, передаваемые в виде параметра от поставщика данных в источник данных. Синтаксис зависит от поставщика данных, и при попытке открыть соединение строка соединения анализируется. Синтаксические ошибки формируют исключение во время выполнения, а другие ошибки происходят после получения источником данных сведений о соединении. После проверки источник данных применяет параметры, указанные в строке соединения, и открывает соединение.
  
Формат строки соединения является списком разделенных точкой с запятой пар параметров «ключ-значение»:
  
    keyword1=value; keyword2=value;
  
Ключевые слова не учитывают регистр. Значения, тем не менее, могут быть с учетом регистра, в зависимости от источника данных. Ключевые слова и значения могут содержать [пробельных символов](https://en.wikipedia.org/wiki/Whitespace_character#Unicode), несмотря на то, что начальные и конечные пробелы игнорируются в ключевые слова и не заключаться в кавычки значения.

Если значение содержит точку с запятой, [управляющие символы Юникода](https://en.wikipedia.org/wiki/Unicode_control_characters), начальные или конечные пробелы, он должен быть заключен в одинарные или двойные кавычки, например:

    Keyword=" whitespace  ";
    Keyword='special;character';

Внешний символ может не произойти в значении, он помещает. Таким образом значение, содержащее одинарные кавычки могут быть заключены только в двойные кавычки и наоборот:

    Keyword='double"quotation;mark';
    Keyword="single'quotation;mark";

Сами кавычки, а также знак равенства не требуют escape-преобразование, поэтому в следующей строке подключения являются допустимыми:

    Keyword=no "escaping" 'required';
    Keyword=a=b=c

Так как каждое значение считывается до следующей точки с запятой или конца строки, в последнем примере значение `a=b=c`, и окончательный точка с запятой является необязательным.

Синтаксис допустимой строки соединения зависит от поставщика и развивается со временем от ранних API-интерфейсов, таких как ODBC. Поставщик данных .NET Framework для SQL Server (SqlClient) содержит многие элементы старого синтаксиса и, как правило, более гибок с основным синтаксисом строки соединения. Для элементов синтаксиса строки соединения существуют допустимые синонимы, но некоторые ошибки синтаксиса и написания могут вызвать проблемы. Например, «`Integrated Security=true`» - достоверно, в то время как «`IntegratedSecurity=true`» вызывает ошибку. Кроме того, строки соединения, создаваемые из непроверенных пользовательских входных данных во время выполнения, могут привести к атакам путем внедрения данных в строку, подвергающим риску безопасность источника данных.
  
Для решения этих проблем ADO.NET версии 2.0 предоставляет новые построители строк подключений для каждого поставщика данных .NET Framework. Ключевые слова представляются в виде свойств, позволяя проверять синтаксис строки соединения перед передачей источника данных.
  
## <a name="in-this-section"></a>В этом разделе  
 [Построители строк подключения](../../../../docs/framework/data/adonet/connection-string-builders.md)  
 Демонстрирует использование классов `ConnectionStringBuilder` для создания достоверных строк соединения во время выполнения.
  
 [Строки подключения и файлы конфигурации](../../../../docs/framework/data/adonet/connection-strings-and-configuration-files.md)  
 Демонстрирует хранение и получение строк соединения в файлах конфигурации.
  
 [Синтаксис строки подключения](../../../../docs/framework/data/adonet/connection-string-syntax.md)  
 Описывает настройку строк соединения, зависящих от поставщика, для `SqlClient`, `OracleClient`, `OleDb` и `Odbc`.
  
 [Защита сведений о подключении](../../../../docs/framework/data/adonet/protecting-connection-information.md)  
 Демонстрирует методы защиты сведений, используемых для подключения к источнику данных.
  
## <a name="see-also"></a>См. также  
 [Подключение к источнику данных](/cpp/data/odbc/connecting-to-a-data-source)  
 [Центр разработчиков наборов данных и управляемых поставщиков ADO.NET](https://go.microsoft.com/fwlink/?LinkId=217917)
