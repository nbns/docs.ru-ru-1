---
title: Практическое руководство. Выгрузка домена приложения
ms.date: 03/30/2017
dev_langs:
- csharp
- vb
- cpp
helpviewer_keywords:
- Unload method
- application domains, unloading
- unloading application domains
ms.assetid: f356116d-e415-4f7c-a332-6e6a60227192
author: rpetrusha
ms.author: ronpet
ms.openlocfilehash: 0a9e5ee865b5e0ac9ec0214a4a0b5194bbcd9f30
ms.sourcegitcommit: 11f11ca6cefe555972b3a5c99729d1a7523d8f50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32742092"
---
# <a name="how-to-unload-an-application-domain"></a>Практическое руководство. Выгрузка домена приложения
После завершения использования домена приложения выгрузите его с помощью метода <xref:System.AppDomain.Unload%2A?displayProperty=nameWithType>. Метод **Unload** безопасно завершает работу указанного домена приложения. В процессе выгрузки новые потоки не могут получить доступ к домену приложения, и освобождаются все структуры данных, определяемые доменом приложения.  
  
 Сборки, загруженные в домен приложения, удаляются и становятся недоступными. Если сборка в домене приложения не зависит от домена, данные для сборки остаются в памяти, пока не будет завершен весь процесс. Не существует механизма для выгрузки независящей от домена сборки, кроме закрытия всего процесса. Существуют ситуации, когда запрос на выгрузку домена приложения не работает и возникает исключение <xref:System.CannotUnloadAppDomainException>.  
  
 В следующем примере создается новый домен приложения с именем `MyDomain`, выполняется вывод некоторых данных на консоль, а затем выгрузка домена приложения. Обратите внимание, что код пытается вывести на консоль понятное имя выгруженного домена приложения. Это действие создает исключение, которое обрабатывается операторами try/catch в конце программы.  
  
## <a name="example"></a>Пример  
 [!code-cpp[System.AppDomain.Load#3](../../../samples/snippets/cpp/VS_Snippets_CLR_System/system.appdomain.load/cpp/source3.cpp#3)]
 [!code-csharp[System.AppDomain.Load#3](../../../samples/snippets/csharp/VS_Snippets_CLR_System/system.appdomain.load/cs/source3.cs#3)]
 [!code-vb[System.AppDomain.Load#3](../../../samples/snippets/visualbasic/VS_Snippets_CLR_System/system.appdomain.load/vb/source3.vb#3)]  
  
## <a name="see-also"></a>См. также  
 [Программирование с использованием доменов приложений](application-domains.md#programming-with-application-domains)  
 [Практическое руководство. Создание домена приложения](../../../docs/framework/app-domains/how-to-create-an-application-domain.md)  
 [Использование доменов приложений](../../../docs/framework/app-domains/use.md)
