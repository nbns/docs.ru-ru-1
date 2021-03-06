---
title: Практическое руководство. Преобразование строки (String) в массив символов в Visual Basic
ms.date: 07/20/2015
helpviewer_keywords:
- character arrays [Visual Basic], converting strings
- arrays [Visual Basic], converting strings to
- examples [Visual Basic], string conversion
- strings [Visual Basic], converting to arrays
- string conversion [Visual Basic], arrays
ms.assetid: 1b54b686-ab29-413b-adce-6bd5422376eb
ms.openlocfilehash: cc12b70cddcb93a72b4421a8ddd93542ef84f55b
ms.sourcegitcommit: 8c28ab17c26bf08abbd004cc37651985c68841b8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48845194"
---
# <a name="how-to-convert-a-string-to-an-array-of-characters-in-visual-basic"></a>Практическое руководство. Преобразование строки (String) в массив символов в Visual Basic
Иногда бывает удобно иметь данные о символов в строки и позиции этих символов, например при анализе строки. В этом примере показано, как можно получить массив символов в строку путем вызова строки <xref:System.String.ToCharArray%2A> метод.  
  
## <a name="example"></a>Пример  
 В этом примере показано, как разбить строку в `Char` массива и как разбить строку на `String` массив знаков Юникода. Это различие связано что Юникода может состоять из двух или более `Char` символов (например, суррогатная пара или несамостоятельных последовательность символов). Дополнительные сведения см. в разделе <xref:System.Globalization.TextElementEnumerator> и [The Unicode Standard](https://www.unicode.org/standard/standard.html).  
  
 [!code-vb[VbVbalrStrings#75](../../../../visual-basic/language-reference/functions/codesnippet/VisualBasic/how-to-convert-a-string-to-an-array-of-characters_1.vb)]  
  
## <a name="example"></a>Пример  
 Сложнее для разбиения строки на знаков Юникода, но это необходимо, если вам нужна информация об визуальное представление строки. В этом примере используется <xref:System.Globalization.StringInfo.SubstringByTextElements%2A> метод для получения сведений о текстовых символов Юникода, составляющих строку.  
  
 [!code-vb[VbVbalrStrings#76](../../../../visual-basic/language-reference/functions/codesnippet/VisualBasic/how-to-convert-a-string-to-an-array-of-characters_2.vb)]  
  
## <a name="see-also"></a>См. также  
 <xref:System.String.Chars%2A>  
 <xref:System.Globalization.StringInfo?displayProperty=nameWithType>  
 [Практическое руководство. Доступ к символам в строках](../../../../visual-basic/programming-guide/language-features/strings/how-to-access-characters-in-strings.md)  
 [Преобразование между строками и другими типами данных в Visual Basic](../../../../visual-basic/programming-guide/language-features/strings/converting-between-strings-and-other-data-types.md)  
 [Строки](../../../../visual-basic/programming-guide/language-features/strings/index.md)
