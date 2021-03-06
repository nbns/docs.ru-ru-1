---
title: 'Параметры значения (F #)'
description: 'Дополнительные сведения о типе параметра значения F #, — версии структуры типа параметра.'
ms.date: 06/16/2018
ms.openlocfilehash: 5647ef61725401b10a6045b14eef11f5b041e3e9
ms.sourcegitcommit: a885cc8c3e444ca6471348893d5373c6e9e49a47
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44041214"
---
# <a name="value-options"></a>Параметры значения

Тип значения параметра в F # используется при проведении следующих двух случаях:

1. Сценарий подходит для [F # параметр](options.md).
2. С помощью структуры обеспечивает повышение производительности в вашем сценарии.

Не все сценарии быстродействие» разрешаются» с помощью структуры. Необходимо учитывать дополнительные издержки копирования при использовании их вместо ссылочных типов. Тем не менее больших программах F # обычно установить многие дополнительные типы, которые проходят через критических путей, так как структуры, иногда могут выдавать улучшить общую производительность в течение времени существования программы.

## <a name="definition"></a>Определение

Значение параметра определяется как [размеченные объединения](discriminated-unions.md#struct-discriminated-unions) , похож на ссылочный тип параметра:

```fsharp
[<StructuralEquality; StructuralComparison>]
[<CompiledName("FSharpValueOption`1")>]
[<Struct>]
type ValueOption<'T> =
    | ValueNone: 'T voption
    | ValueSome: 'T -> 'T voption

    member Value : 'T

and 'T voption = ValueOption<'T>
```

Значение параметра соответствует структурного равенства и сравнения. Основное различие является то, что скомпилированный имя, имя типа и регистра имен указать, что не типом значения.

## <a name="using-value-options"></a>С помощью параметров значений

Параметры значения используются так же, как [параметры](options.md). `ValueSome` Указывает, что значение будет присутствовать, и `ValueNone` используется, если значение отсутствует:

```fsharp
let tryParseDateTime (s: string) =
    match System.DateTime.TryParse(s) with
    | (true, dt) -> ValueSome dt
    | (false, _) -> ValueNone

let possibleDateString1 = "1990-12-25"
let possibleDateString2 = "This is not a date"

let result1 = tryParseDateTime possibleDateString1
let result2 = tryParseDateTime possibleDateString2

match (result1, result2) with
| ValueSome d1, ValueSome d2 -> printfn "Both are dates!"
| ValueSome d1, ValueNone -> printfn "Only the first is a date!"
| ValueNone, ValueSome d2 -> printfn "Only the second is a date!"
| ValueNone, ValueNone -> printfn "None of them are dates!"
```

Как и в [параметры](options.md), соглашение об именовании для функции, которая возвращает `ValueOption` является перед ними `try`.

## <a name="value-option-properties-and-methods"></a>Значение параметра свойства и методы

В настоящее время есть одно свойство для возможных значений: `Value`. <xref:System.InvalidOperationException> Возникает, если значение не присутствует, при вызове этого свойства.

## <a name="value-option-functions"></a>Значение параметра функции

В данный момент одного связанного с модулем функции для параметров значение `defaultValueArg`:

```fsharp
val defaultValueArg : arg:'T voption -> defaultValue:'T -> 'T 
```

Как и в `defaultArg` функции `defaultValueArg` Возвращает базовое значение данного параметра значение, если он существует; в противном случае возвращает значение указанного по умолчанию.

В настоящее время нет других функций связанного с модулем для возможных значений.

## <a name="see-also"></a>См. также

- [Параметры](options.md)