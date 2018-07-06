---
title: Что нового в C# 7.3
description: Обзор новых возможностей в C# 7.3
ms.date: 05/16/2018
ms.openlocfilehash: 135351fa06a498e4aa90cb4d9372880b8119de0f
ms.sourcegitcommit: 979597cd8055534b63d2c6ee8322938a27d0c87b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37106779"
---
# <a name="whats-new-in-c-73"></a><span data-ttu-id="59a96-103">Что нового в C# 7.3</span><span class="sxs-lookup"><span data-stu-id="59a96-103">What's new in C# 7.3</span></span>

<span data-ttu-id="59a96-104">Новые возможности в выпуске C# 7.3 можно разделить на две основные группы.</span><span class="sxs-lookup"><span data-stu-id="59a96-104">There are two main themes to the C# 7.3 release.</span></span> <span data-ttu-id="59a96-105">Одна из них — набор функций для повышения эффективности безопасного кода до уровня небезопасного кода.</span><span class="sxs-lookup"><span data-stu-id="59a96-105">One theme provides features that enable safe code to be as performant as unsafe code.</span></span> <span data-ttu-id="59a96-106">Вторая — постепенные улучшения существующих функций.</span><span class="sxs-lookup"><span data-stu-id="59a96-106">The second theme provides incremental improvements to existing features.</span></span> <span data-ttu-id="59a96-107">Кроме того, в этом выпуске добавлены новые параметры компилятора.</span><span class="sxs-lookup"><span data-stu-id="59a96-107">In addition, new compiler options were added in this release.</span></span>

<span data-ttu-id="59a96-108">В ту группу, которая отвечает за повышение производительности безопасного кода, входят следующие новые возможности:</span><span class="sxs-lookup"><span data-stu-id="59a96-108">The following new features support the theme of better performance for safe code:</span></span>

- <span data-ttu-id="59a96-109">доступ к полям фиксированной ширины без закрепления;</span><span class="sxs-lookup"><span data-stu-id="59a96-109">You can access fixed fields without pinning.</span></span>
- <span data-ttu-id="59a96-110">возможность переназначать локальные переменные `ref`;</span><span class="sxs-lookup"><span data-stu-id="59a96-110">You can reassign `ref` local variables.</span></span>
- <span data-ttu-id="59a96-111">возможность использовать инициализаторы для массивов `stackalloc`;</span><span class="sxs-lookup"><span data-stu-id="59a96-111">You can use initializers on `stackalloc` arrays.</span></span>
- <span data-ttu-id="59a96-112">возможность использовать инструкции `fixed` с любым типом, который поддерживает шаблон;</span><span class="sxs-lookup"><span data-stu-id="59a96-112">You can use `fixed` statements with any type that supports a pattern.</span></span>
- <span data-ttu-id="59a96-113">возможность использовать дополнительные универсальные ограничения.</span><span class="sxs-lookup"><span data-stu-id="59a96-113">You can use additional generic constraints.</span></span>

<span data-ttu-id="59a96-114">Для существующих функций предоставлены следующие улучшения:</span><span class="sxs-lookup"><span data-stu-id="59a96-114">The following enhancements were made to existing features:</span></span>

- <span data-ttu-id="59a96-115">возможность проверить `==` и `!=` с типами кортежа;</span><span class="sxs-lookup"><span data-stu-id="59a96-115">You can test `==` and `!=` with tuple types.</span></span>
- <span data-ttu-id="59a96-116">больше мест для использование переменных выражений;</span><span class="sxs-lookup"><span data-stu-id="59a96-116">You can use expression variables in more locations.</span></span>
- <span data-ttu-id="59a96-117">возможность подключить атрибуты к резервному полю автоматически реализуемых свойств;</span><span class="sxs-lookup"><span data-stu-id="59a96-117">You may attach attributes to the backing field of auto-implemented properties.</span></span>
- <span data-ttu-id="59a96-118">улучшенное разрешение методов, аргументы которых отличаются модификатором `in`;</span><span class="sxs-lookup"><span data-stu-id="59a96-118">Method resolution when arguments differ by `in` has been improved.</span></span>
- <span data-ttu-id="59a96-119">стало меньше неоднозначных вариантов при разрешении перегрузок.</span><span class="sxs-lookup"><span data-stu-id="59a96-119">Overload resolution now has fewer ambiguous cases.</span></span>

<span data-ttu-id="59a96-120">Новые параметры компилятора:</span><span class="sxs-lookup"><span data-stu-id="59a96-120">The new compiler options are:</span></span>

- <span data-ttu-id="59a96-121">`-publicsign` позволяет включить подписывание сборок как программного обеспечения с открытым кодом;</span><span class="sxs-lookup"><span data-stu-id="59a96-121">`-publicsign` to enable Open Source Software (OSS) signing of assemblies.</span></span>
- <span data-ttu-id="59a96-122">`-pathmap` позволяет предоставить сопоставление для исходных каталогов.</span><span class="sxs-lookup"><span data-stu-id="59a96-122">`-pathmap` to provide a mapping for source directories.</span></span>

<span data-ttu-id="59a96-123">В оставшейся части этой статьи представлены дополнительные сведения и ссылки по каждому из перечисленных улучшений.</span><span class="sxs-lookup"><span data-stu-id="59a96-123">The remainder of this article provides details and links to learn more about each of the improvements.</span></span>

## <a name="enabling-more-performant-safe-code"></a><span data-ttu-id="59a96-124">Повышение производительности безопасного кода</span><span class="sxs-lookup"><span data-stu-id="59a96-124">Enabling more performant safe code</span></span>

<span data-ttu-id="59a96-125">Теперь вы сможете создавать безопасный код C#, который выполняется не хуже небезопасного кода.</span><span class="sxs-lookup"><span data-stu-id="59a96-125">You should be able to write C# code safely that performs as well as unsafe code.</span></span> <span data-ttu-id="59a96-126">Безопасный код позволяет избежать ошибок некоторых типов, таких как переполнение буфера, свободные указатели и другие ошибки доступа к памяти.</span><span class="sxs-lookup"><span data-stu-id="59a96-126">Safe code avoids classes of errors, such as buffer overruns, stray pointers, and other memory access errors.</span></span> <span data-ttu-id="59a96-127">Новые функции расширяют возможности гарантированно безопасного кода.</span><span class="sxs-lookup"><span data-stu-id="59a96-127">These new features expand the capabilities of verifiable safe code.</span></span> <span data-ttu-id="59a96-128">Старайтесь как можно большую часть кода создавать из безопасных конструкций.</span><span class="sxs-lookup"><span data-stu-id="59a96-128">Strive to write more of your code using safe constructs.</span></span> <span data-ttu-id="59a96-129">Благодаря новым возможностям это станет проще.</span><span class="sxs-lookup"><span data-stu-id="59a96-129">These features make that easier.</span></span>

### <a name="indexing-fixed-fields-does-not-require-pinning"></a><span data-ttu-id="59a96-130">Индексирование полей `fixed` не требует закрепления</span><span class="sxs-lookup"><span data-stu-id="59a96-130">Indexing `fixed` fields does not require pinning</span></span>

<span data-ttu-id="59a96-131">Давайте рассмотрим такую структуру:</span><span class="sxs-lookup"><span data-stu-id="59a96-131">Consider this struct:</span></span>

```csharp
unsafe struct S
{
    public fixed int myFixedField[10];
}
```

<span data-ttu-id="59a96-132">В более ранних версиях C# переменную необходимо закрепить, чтобы получить доступ к целым числам, входящим в `myFixedField`.</span><span class="sxs-lookup"><span data-stu-id="59a96-132">In earlier versions of C#, you needed to pin a variable to access one of the integers that are part of `myFixedField`.</span></span> <span data-ttu-id="59a96-133">Теперь такой код компилируется в безопасном контексте:</span><span class="sxs-lookup"><span data-stu-id="59a96-133">Now, the following code compiles in a safe context:</span></span>

```csharp
class C
{
    static S s = new S();

    unsafe public void M()
    {
        int p = s.myFixedField[5];
    }
}
```

<span data-ttu-id="59a96-134">Переменная `p` обращается к одному элементу в `myFixedField`.</span><span class="sxs-lookup"><span data-stu-id="59a96-134">The variable `p` accesses one element in `myFixedField`.</span></span> <span data-ttu-id="59a96-135">Для этого не нужно объявлять отдельную переменную `int*`.</span><span class="sxs-lookup"><span data-stu-id="59a96-135">You don't need to declare a separate `int*` variable.</span></span> <span data-ttu-id="59a96-136">Обратите внимание, что контекст `unsafe` по-прежнему является обязательным.</span><span class="sxs-lookup"><span data-stu-id="59a96-136">Note that you still need an `unsafe` context.</span></span> <span data-ttu-id="59a96-137">В более ранних версиях C# необходимо объявить второй фиксированный указатель:</span><span class="sxs-lookup"><span data-stu-id="59a96-137">In earlier versions of C#, you need to declare a second fixed pointer:</span></span>

```csharp
class C
{
    static S s = new S();

    unsafe public void M()
    {
        fixed (int* ptr = s.myFixedField)
        {
            int p = ptr[5];
        }
    }
}
```

<span data-ttu-id="59a96-138">Дополнительные сведения см. в статье, посвященной [инструкции `fixed`](../language-reference/keywords/fixed-statement.md).</span><span class="sxs-lookup"><span data-stu-id="59a96-138">Fore more information, see the article on the [`fixed` statement](../language-reference/keywords/fixed-statement.md).</span></span>

### <a name="ref-local-variables-may-be-reassigned"></a><span data-ttu-id="59a96-139">Локальные переменные `ref` могут быть переназначены</span><span class="sxs-lookup"><span data-stu-id="59a96-139">`ref` local variables may be reassigned</span></span>

<span data-ttu-id="59a96-140">Теперь локальные переменные `ref` можно переназначить другим экземплярам после инициализации.</span><span class="sxs-lookup"><span data-stu-id="59a96-140">Now, `ref` locals may be reassigned to refer to different instances after being initialized.</span></span> <span data-ttu-id="59a96-141">Следующая команда теперь успешно компилируется:</span><span class="sxs-lookup"><span data-stu-id="59a96-141">The following code now compiles:</span></span>

```csharp
ref VeryLargeStruct refLocal = ref veryLargeStruct; // initialization
refLocal = ref anotherVeryLargeStruct; // reassigned, refLocal refers to different storage.
```

<span data-ttu-id="59a96-142">Дополнительные сведения см. в статье о возвращаемых значениях [`ref` и локальных переменных `ref`](../programming-guide/classes-and-structs/ref-returns.md), а также в статье [`foreach`](../language-reference/keywords/foreach-in.md).</span><span class="sxs-lookup"><span data-stu-id="59a96-142">For more information, see the article on [`ref` returns and `ref` locals](../programming-guide/classes-and-structs/ref-returns.md), and the article on [`foreach`](../language-reference/keywords/foreach-in.md).</span></span>

### <a name="stackalloc-arrays-support-initializers"></a><span data-ttu-id="59a96-143">Массивы `stackalloc` поддерживают инициализаторы</span><span class="sxs-lookup"><span data-stu-id="59a96-143">`stackalloc` arrays support initializers</span></span>

<span data-ttu-id="59a96-144">Раньше вы могли задавать значения для элементов массива при его инициализации:</span><span class="sxs-lookup"><span data-stu-id="59a96-144">You've been able to specify the values for elements in an array when you initialize it:</span></span>

```csharp
var arr = new int[3] {1, 2, 3};
var arr2 = new int[] {1, 2, 3};
```

<span data-ttu-id="59a96-145">Теперь такой же синтаксис можно применять к массивам, в объявлении которых есть `stackalloc`:</span><span class="sxs-lookup"><span data-stu-id="59a96-145">Now, that same syntax can be applied to arrays that are declared with `stackalloc`:</span></span>

```csharp
int* pArr = stackalloc int[3] {1, 2, 3};
int* pArr2 = stackalloc int[] {1, 2, 3};
Span<int> arr = stackalloc [] {1, 2, 3};
```

<span data-ttu-id="59a96-146">Дополнительные сведения см. в статье [об инструкции `stackalloc`](../language-reference/keywords/stackalloc.md) в справочнике по языку.</span><span class="sxs-lookup"><span data-stu-id="59a96-146">For more information, see the [`stackalloc` statement](../language-reference/keywords/stackalloc.md) article in the language reference.</span></span>

### <a name="more-types-support-the-fixed-statement"></a><span data-ttu-id="59a96-147">Больше типов поддерживают инструкцию `fixed`</span><span class="sxs-lookup"><span data-stu-id="59a96-147">More types support the `fixed` statement</span></span>

<span data-ttu-id="59a96-148">Инструкция `fixed` ранее поддерживала лишь ограниченный набор типов.</span><span class="sxs-lookup"><span data-stu-id="59a96-148">The `fixed` statement supported a limited set of types.</span></span> <span data-ttu-id="59a96-149">Начиная с C# 7.3 любой тип, содержащий метод `GetPinnableReference()`, который возвращает `ref T` или `ref readonly T`, может иметь инструкцию `fixed`.</span><span class="sxs-lookup"><span data-stu-id="59a96-149">Starting with C# 7.3, any type that contains a `GetPinnableReference()` method that returns a `ref T` or `ref readonly T` may be `fixed`.</span></span> <span data-ttu-id="59a96-150">Добавление этой возможности означает, что `fixed` можно применять для <xref:System.Span%601?displayProperty=nameWithType> и связанных типов.</span><span class="sxs-lookup"><span data-stu-id="59a96-150">Adding this feature means that `fixed` can be used with <xref:System.Span%601?displayProperty=nameWithType> and related types.</span></span>

<span data-ttu-id="59a96-151">Дополнительные сведения см. в статье [об инструкции `fixed`](../language-reference/keywords/fixed-statement.md) в справочнике по языку.</span><span class="sxs-lookup"><span data-stu-id="59a96-151">For more information, see the [`fixed` statement](../language-reference/keywords/fixed-statement.md) article in the language reference.</span></span>

### <a name="enhanced-generic-constraints"></a><span data-ttu-id="59a96-152">Расширенные универсальные ограничения</span><span class="sxs-lookup"><span data-stu-id="59a96-152">Enhanced generic constraints</span></span>

<span data-ttu-id="59a96-153">Теперь вы можете указать тип <xref:System.Enum?displayProperty=nameWithType> или <xref:System.Delegate?displayProperty=nameWithType> в качестве ограничения базового класса для параметра типа.</span><span class="sxs-lookup"><span data-stu-id="59a96-153">You can now specify the type <xref:System.Enum?displayProperty=nameWithType> or <xref:System.Delegate?displayProperty=nameWithType> as base class constraints for a type parameter.</span></span>

<span data-ttu-id="59a96-154">Вы также можете использовать новое ограничение `unmanaged`, чтобы указать, что параметр типа должен быть **неуправляемым типом**.</span><span class="sxs-lookup"><span data-stu-id="59a96-154">You can also use the new `unmanaged` constraint, to specify that a type parameter must be an **unmanaged type**.</span></span> <span data-ttu-id="59a96-155">**Неуправляемый тип** — это тип, который не является ссылочным и не содержит ни одного ссылочного типа на любом уровне вложения.</span><span class="sxs-lookup"><span data-stu-id="59a96-155">An **unmanaged type** is a type that isn't a reference type and doesn't contain any reference type at any level of nesting.</span></span>

<span data-ttu-id="59a96-156">Дополнительные сведения см. в статьях [об универсальных ограничениях `where`](../language-reference/keywords/where-generic-type-constraint.md) и [ограничениях параметров типа](../programming-guide/generics/constraints-on-type-parameters.md).</span><span class="sxs-lookup"><span data-stu-id="59a96-156">For more information, see the articles on [`where` generic constraints](../language-reference/keywords/where-generic-type-constraint.md) and [constraints on type parameters](../programming-guide/generics/constraints-on-type-parameters.md).</span></span>

## <a name="make-existing-features-better"></a><span data-ttu-id="59a96-157">Улучшение существующих функций</span><span class="sxs-lookup"><span data-stu-id="59a96-157">Make existing features better</span></span>

<span data-ttu-id="59a96-158">Во второй группе представлены улучшения существующих возможностей языка.</span><span class="sxs-lookup"><span data-stu-id="59a96-158">The second theme provides improvements to features in the language.</span></span> <span data-ttu-id="59a96-159">Эти возможности повышают производительность при создании кода на C#.</span><span class="sxs-lookup"><span data-stu-id="59a96-159">These features improve productivity when writing C#.</span></span>

### <a name="tuples-support--and-"></a><span data-ttu-id="59a96-160">Поддержка `==` и `!=` для кортежей</span><span class="sxs-lookup"><span data-stu-id="59a96-160">Tuples support `==` and `!=`</span></span>

<span data-ttu-id="59a96-161">Типы кортежей в C# теперь поддерживают `==` и `!=`.</span><span class="sxs-lookup"><span data-stu-id="59a96-161">The C# tuple types now support `==` and `!=`.</span></span> <span data-ttu-id="59a96-162">Дополнительные сведения см. в разделе о [равенстве](../tuples.md#equality-and-tuples) в статье про [кортежи](../tuples.md).</span><span class="sxs-lookup"><span data-stu-id="59a96-162">Fore more information, see the section covering [equality](../tuples.md#equality-and-tuples) in the article on [tuples](../tuples.md).</span></span>

### <a name="attach-attributes-to-the-backing-fields-for-auto-implemented-properties"></a><span data-ttu-id="59a96-163">Подключение атрибутов к резервным полям для автоматически реализуемых свойств</span><span class="sxs-lookup"><span data-stu-id="59a96-163">Attach attributes to the backing fields for auto-implemented properties</span></span>

<span data-ttu-id="59a96-164">Теперь поддерживается такой синтаксис:</span><span class="sxs-lookup"><span data-stu-id="59a96-164">This syntax is now supported:</span></span>

```csharp
[field: SomeThingAboutFieldAttribute]
public int SomeProperty { get; set; }
```

<span data-ttu-id="59a96-165">Атрибут `SomeThingAboutFieldAttribute` применяется к резервному полю, созданному компилятором для `SomeProperty`.</span><span class="sxs-lookup"><span data-stu-id="59a96-165">The attribute `SomeThingAboutFieldAttribute` is applied to the compiler generated backing field for `SomeProperty`.</span></span> <span data-ttu-id="59a96-166">Дополнительные сведения см. в статье об [атрибутах](../programming-guide/concepts/attributes/index.md) в руководстве по программированию на C#.</span><span class="sxs-lookup"><span data-stu-id="59a96-166">For more information, see [attributes](../programming-guide/concepts/attributes/index.md) in the C# programming guide.</span></span>

### <a name="in-method-overload-resolution-tiebreaker"></a><span data-ttu-id="59a96-167">Критерии для разрешения перегрузки метода `in`</span><span class="sxs-lookup"><span data-stu-id="59a96-167">`in` method overload resolution tiebreaker</span></span>

<span data-ttu-id="59a96-168">При добавлении модификатора аргумента `in` следующие два метода создавали неоднозначность:</span><span class="sxs-lookup"><span data-stu-id="59a96-168">When the `in` argument modifier was added, these two methods would cause an ambiguity:</span></span>

```csharp
static void M(S arg);
static void M(in S arg);
```

<span data-ttu-id="59a96-169">Теперь перегрузка по значению (первая в предыдущем примере) считается лучше, чем перегрузка по атрибуту "только для чтения".</span><span class="sxs-lookup"><span data-stu-id="59a96-169">Now, the by value (first in the preceding example) overload is better than the by readonly reference version.</span></span> <span data-ttu-id="59a96-170">Чтобы вызвать версию со ссылочным аргументом "только для чтения", необходимо при вызове метода указать модификатор `in`.</span><span class="sxs-lookup"><span data-stu-id="59a96-170">To call the version with the readonly reference argument, you must include the `in` modifier when calling the method.</span></span>

> [!NOTE]
> <span data-ttu-id="59a96-171">Эта возможность реализована как исправление ошибки.</span><span class="sxs-lookup"><span data-stu-id="59a96-171">This was implemented as a bug fix.</span></span> <span data-ttu-id="59a96-172">Теперь эта ситуация не создает неоднозначности, даже если установлена версия языка 7.2.</span><span class="sxs-lookup"><span data-stu-id="59a96-172">This no longer is ambiguous even with the language version set to "7.2".</span></span>

<span data-ttu-id="59a96-173">Дополнительные сведения см. в статье, посвященной [модификатору параметра `in`](../language-reference/keywords/in-parameter-modifier.md).</span><span class="sxs-lookup"><span data-stu-id="59a96-173">For more information, see the article on the [`in` parameter modifier](../language-reference/keywords/in-parameter-modifier.md).</span></span>

### <a name="extend-expression-variables-in-initializers"></a><span data-ttu-id="59a96-174">Расширение переменных выражений в инициализаторах</span><span class="sxs-lookup"><span data-stu-id="59a96-174">Extend expression variables in initializers</span></span>

<span data-ttu-id="59a96-175">Синтаксис, который с версии C# 7.0 позволяет объявлять переменные `out`, теперь также поддерживает инициализаторы полей, инициализаторы свойств, инициализаторы конструктора и предложения запроса.</span><span class="sxs-lookup"><span data-stu-id="59a96-175">The syntax added in C# 7.0 to allow `out` variable declarations has been extended to include field initializers, property initializers, constructor initializers, and query clauses.</span></span> <span data-ttu-id="59a96-176">Он позволяет создать такой код, как в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="59a96-176">It enables code such as the following example:</span></span>

```csharp
public class B
{
   public B(int i, out int j)
   {
      j = i;
   }
}

public class D : B
{
   public D(int i) : B(i, out var j)
   {
      Console.WriteLine($"The value of 'j' is {j}");
   }
}
```

### <a name="improved-overload-candidates"></a><span data-ttu-id="59a96-177">Улучшенный отбор потенциальных перегрузок</span><span class="sxs-lookup"><span data-stu-id="59a96-177">Improved overload candidates</span></span>

<span data-ttu-id="59a96-178">В каждом выпуске обновляются правила разрешения перегрузок для устранения ситуаций, где неоднозначный вызов методов можно решить "очевидным" способом.</span><span class="sxs-lookup"><span data-stu-id="59a96-178">In every release, the overload resolution rules get updated to address situations where ambiguous method invocations have an "obvious" choice.</span></span> <span data-ttu-id="59a96-179">В этот выпуск добавлены три новых правила, которые помогают компилятору выбрать очевидный вариант.</span><span class="sxs-lookup"><span data-stu-id="59a96-179">This release adds three new rules to help the compiler pick the obvious choice:</span></span>

1. <span data-ttu-id="59a96-180">Если группа методов содержит элементы экземпляра и статические элементы, компилятор отклоняет все элементы экземпляра при вызове метода без экземпляра-получателя и вне контекста экземпляра.</span><span class="sxs-lookup"><span data-stu-id="59a96-180">When a method group contains both instance and static members, the compiler discards the instance members if the method was invoked without an instance receiver or context.</span></span> <span data-ttu-id="59a96-181">Компилятор отклоняет статические элементы, если метод был вызван с экземпляром-получателем.</span><span class="sxs-lookup"><span data-stu-id="59a96-181">The compiler discards the static members if the method was invoked with an instance receiver.</span></span> <span data-ttu-id="59a96-182">Если получатель не указан, компилятор включает в статический контекст только статические элементы, а в противном случае — статические элементы и элементы экземпляра.</span><span class="sxs-lookup"><span data-stu-id="59a96-182">When there is no receiver, the compiler includes only static members in a static context, otherwise both static and instance members.</span></span> <span data-ttu-id="59a96-183">Если получатель невозможно однозначно определить как экземпляр или тип, компилятор включает и те, и другие элементы.</span><span class="sxs-lookup"><span data-stu-id="59a96-183">When the receiver is ambiguously an instance or type, the compiler includes both.</span></span> <span data-ttu-id="59a96-184">В статический контекст, в котором невозможно использовать неявный экземпляр-получатель `this`, включается текст тех элементов, для которых не определено `this`, например статические элементы, а также все места, где не может использоваться `this`, такие как инициализаторы полей и конструкторы-инициализаторы.</span><span class="sxs-lookup"><span data-stu-id="59a96-184">A static context, where an implicit `this` instance receiver cannot be used, includes the body of members where no `this` is defined, such as static members, as well as places where `this` cannot be used, such as field initializers and constructor-initializers.</span></span>
1. <span data-ttu-id="59a96-185">Если группа методов содержит некоторые универсальные методы, у которых аргументы типа не удовлетворяют ограничениям, такие элементы удаляются из набора кандидатов.</span><span class="sxs-lookup"><span data-stu-id="59a96-185">When a method group contains some generic methods whose type arguments do not satisfy their constraints, these members are removed from the candidate set.</span></span>
1. <span data-ttu-id="59a96-186">При преобразовании группы методов из набора удаляются методы-кандидаты, у которых возвращаемый тип не соответствует возвращаемому типу делегата.</span><span class="sxs-lookup"><span data-stu-id="59a96-186">For a method group conversion, candidate methods whose return type doesn't match up with the delegate's return type are removed from the set.</span></span>

<span data-ttu-id="59a96-187">Это изменение проявится только тем, что вы реже будете встречать ошибки компилятора о неоднозначной перегрузке методов в тех ситуациях, когда вы точно уверены в выборе лучшего метода.</span><span class="sxs-lookup"><span data-stu-id="59a96-187">You'll only notice this change because you'll find fewer compiler errors for ambiguous method overloads when you are sure which method is better.</span></span>

## <a name="new-compiler-options"></a><span data-ttu-id="59a96-188">Новые параметры компилятора</span><span class="sxs-lookup"><span data-stu-id="59a96-188">New compiler options</span></span>

<span data-ttu-id="59a96-189">Новые параметры компилятора поддерживают сценарии сборки и DevOps для программ на C#.</span><span class="sxs-lookup"><span data-stu-id="59a96-189">New compiler options support new build and DevOps scenarios for C# programs.</span></span>

### <a name="public-or-open-source-signing"></a><span data-ttu-id="59a96-190">Подписывание открытым ключом или с открытым исходным кодом</span><span class="sxs-lookup"><span data-stu-id="59a96-190">Public or Open Source signing</span></span>

<span data-ttu-id="59a96-191">Параметр компилятора `-publicsign` указывает, что сборку нужно подписать открытым ключом.</span><span class="sxs-lookup"><span data-stu-id="59a96-191">The `-publicsign` compiler option instructs the compiler to sign the assembly using a public key.</span></span> <span data-ttu-id="59a96-192">Такая сборка будет помечена как подписанная, но подпись для нее берется из открытого ключа.</span><span class="sxs-lookup"><span data-stu-id="59a96-192">The assembly is marked as signed, but the signature is taken from the public key.</span></span> <span data-ttu-id="59a96-193">Этот параметр позволяет создавать подписанные сборки из проектов с открытым кодом с помощью открытого ключа.</span><span class="sxs-lookup"><span data-stu-id="59a96-193">This option enables you to build signed assemblies from open-source projects using a public key.</span></span>

<span data-ttu-id="59a96-194">Дополнительные сведения см. в статье [о параметре компилятора -publicsign](../language-reference/compiler-options/publicsign-compiler-option.md).</span><span class="sxs-lookup"><span data-stu-id="59a96-194">For more information, see the [-publicsign compiler option](../language-reference/compiler-options/publicsign-compiler-option.md) article.</span></span>

### <a name="pathmap"></a><span data-ttu-id="59a96-195">pathmap</span><span class="sxs-lookup"><span data-stu-id="59a96-195">pathmap</span></span>

<span data-ttu-id="59a96-196">Параметр компилятора `-pathmap` указывает, что исходные пути в среде создания следует заменить сопоставленными исходными путями.</span><span class="sxs-lookup"><span data-stu-id="59a96-196">The `-pathmap` compiler option instructs the compiler to replace source paths from the build environment with mapped source paths.</span></span> <span data-ttu-id="59a96-197">Параметр `-pathmap` управляет исходными путями, которые компилятор записывает в PDB-файлы или для <xref:System.Runtime.CompilerServices.CallerFilePathAttribute>.</span><span class="sxs-lookup"><span data-stu-id="59a96-197">The `-pathmap` option controls the source path written by the compiler to PDB files or for the <xref:System.Runtime.CompilerServices.CallerFilePathAttribute>.</span></span>

<span data-ttu-id="59a96-198">Дополнительные сведения см. в статье [о параметре компилятора -pathmap](../language-reference/compiler-options/pathmap-compiler-option.md).</span><span class="sxs-lookup"><span data-stu-id="59a96-198">For more information, see the [-pathmap compiler option](../language-reference/compiler-options/pathmap-compiler-option.md) article.</span></span>