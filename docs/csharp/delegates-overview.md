---
title: Общие сведения о делегатах
description: Общие сведения о делегатах, обзор связанных с ними основных понятий и обсуждение целей разработки языка для делегатов.
ms.date: 06/20/2016
ms.assetid: 59b61d77-84e5-457b-8da5-fb5f24ca6ed6
ms.openlocfilehash: d42d9d10aeaa153f12933fa3a59e58719f7741e7
ms.sourcegitcommit: 3d5d33f384eeba41b2dff79d096f47ccc8d8f03d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33212191"
---
# <a name="introduction-to-delegates"></a>Общие сведения о делегатах

[Назад](delegates-events.md)

Делегаты предоставляют механизм *позднего связывания* в .NET. Позднее связывание означает, что создается алгоритм, где вызывающий объект также предоставляет по крайней мере один метод, который реализует часть алгоритма.

Например, рассмотрим сортировку списка звезд в астрономическом приложении.
Можно отсортировать звезды по расстоянию от Земли, по величине или по воспринимаемой яркости.

Во всех этих случаях метод Sort() выполняет, по сути, одно и то же: упорядочивает элементы в списке на основе некоего сравнения. Для каждого порядка сортировки используется разный код, сравнивающий две звезды.

Такого рода решения использовались в программном обеспечении в течение полувека.
Концепция использования делегатов в языке C# обеспечивает первоклассную поддержку языка и безопасность типов.

Как вы увидите далее в этой серии статей, код C#, создаваемый для подобных алгоритмов, является строго типизированным и использует язык и компилятор для соответствия типов аргументам и возвращаемым типам.

## <a name="language-design-goals-for-delegates"></a>Цели разработки языка для делегатов

Разработчики, использующие язык, определили несколько целей для функций, которые в итоге стали делегатами.

Группе разработчиков требовалась общая языковая конструкция, которую можно было бы использовать для любых алгоритмов позднего связывания. Разработчикам удалось изучить одну концепцию и применить ее для решения многих самых разных проблем программного обеспечения.

Во-вторых, группе разработчиков была нужна поддержка одно- и многоадресных вызовов методов. (Многоадресные делегаты — это делегаты, где несколько методов связаны друг с другом.) Вы увидите примеры [далее в этой серии материалов](delegate-class.md). 

Группа разработчиков хотела, чтобы делегаты поддерживали ту же безопасность типа, ожидаемую от всех конструкций C#. 

И, наконец, группа пришла к выводу, что шаблон событий является определенным шаблоном, где использование делегатов (или любого алгоритма позднего связывания) очень эффективно. Группе разработчиков требовалось, чтобы код для делегатов служил основой для шаблона событий .NET.

Результатом всей этой работы стала поддержка делегатов и событий в C# и .NET. В оставшихся статьях в этом разделе будут рассматриваться возможности языка, поддержка библиотек и стандартные практики, которые применяются при работе с делегатами.

Вы узнаете о ключевом слове `delegate` и коде, который он создает. Вы узнаете о функциях в классе `System.Delegate` и их использовании. Вы научитесь создавать типобезопасные делегаты и ознакомитесь со способами создания методов, которые можно вызывать с помощью делегатов. Вы также узнаете, как работать с делегатами и событиями с помощью лямбда-выражения. Вы увидите, каким образом делегаты становятся одними из стандартных блоков для LINQ. Вы узнаете, что делегаты являются основой для шаблона событий .NET, и определите их отличия.

В целом вы получите представление о том, что делегаты являются неотъемлемой частью программирования в .NET и работы с API платформы.

Итак, начнем.

[Вперед](delegate-class.md)
