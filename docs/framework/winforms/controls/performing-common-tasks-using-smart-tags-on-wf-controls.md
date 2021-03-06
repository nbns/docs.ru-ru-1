---
title: Пошаговое руководство. Выполнение типичных задач с помощью смарт-тегов в элементах управления Windows Forms
ms.date: 03/30/2017
helpviewer_keywords:
- DesignerAction object model
- smart tags
- designer actions
ms.assetid: cac337e6-00f6-4584-80f4-75728f5ea113
ms.openlocfilehash: d1c69d2e9e89e0a4fed767216e8743a0ac9ac81d
ms.sourcegitcommit: c7f3e2e9d6ead6cc3acd0d66b10a251d0c66e59d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/08/2018
ms.locfileid: "44201807"
---
# <a name="walkthrough-performing-common-tasks-using-smart-tags-on-windows-forms-controls"></a>Пошаговое руководство. Выполнение типичных задач с помощью смарт-тегов в элементах управления Windows Forms
При создании форм и элементов управления для приложения Windows Forms есть множество задач, которые выполняются несколько раз. Ниже перечислены некоторые наиболее типичных задач, которыми вы столкнетесь:  
  
-   Добавление или удаление вкладки в <xref:System.Windows.Forms.TabControl>.  
  
-   Закрепление элемента управления своему родителю.  
  
-   Изменение ориентации элемента <xref:System.Windows.Forms.SplitContainer> элемента управления.  
  
 Для ускорения разработки, многие элементы управления обеспечивают смарт-тегов, которые являются контекстные меню, позволяющие выполнять общие задачи, как они в одном жесте во время разработки. Эти задачи называются *командами смарт тегов*.  
  
 Смарт-теги остаются прикрепленными к экземпляра элемента управления для существования в конструкторе и всегда доступны.  
  
 В данном пошаговом руководстве представлены следующие задачи.  
  
-   Создание проекта Windows Forms  
  
-   С помощью смарт-тегов  
  
-   Включение и отключение смарт-тегов  
  
 После завершения вы будете понимать роль, которую играют эти важные функции макета.  
  
> [!NOTE]
>  Отображаемые диалоговые окна и команды меню могут отличаться от описанных в справке в зависимости от текущих параметров или выпуска. Чтобы изменить параметры, выберите в меню **Сервис** пункт **Импорт и экспорт параметров** . Дополнительные сведения см. в разделе [Персонализация интегрированной среды разработки Visual Studio](/visualstudio/ide/personalizing-the-visual-studio-ide).  
  
## <a name="creating-the-project"></a>Создание проекта  
 Первым шагом является создание проекта и настройка формы.  
  
#### <a name="to-create-the-project"></a>Создание проекта  
  
1.  Создайте проект приложения на основе Windows с именем «SmartTagsExample» (**файл** > **New** > **проекта**  >   **Visual C#** или **Visual Basic** > **классический рабочий стол** > **Windows Forms Application**).  
  
2.  Выберите форму в **конструктор Windows Forms**.  
  
## <a name="using-smart-tags"></a>С помощью смарт-тегов  
 Смарт-тегов постоянную доступность во время разработки для элементов управления, которые содержат их.  
  
#### <a name="to-use-smart-tags"></a>Использование смарт-тегов  
  
1.  Перетащите <xref:System.Windows.Forms.TabControl> из **элементов** на форму. Обратите внимание, глиф смарт тега (![глиф смарт-тега](../../../../docs/framework/winforms/controls/media/vs-winformsmttagglyph.gif "VS_WinFormSmtTagGlyph")), отображаемый на краю <xref:System.Windows.Forms.TabControl>.  
  
2.  Щелкните глиф смарт тега. Выберите в контекстном меню, отображаемый рядом с глифом, **добавить вкладку** элемента. Обратите внимание, что новую страницу вкладки добавляется <xref:System.Windows.Forms.TabControl>.  
  
3.  Перетащите <xref:System.Windows.Forms.TableLayoutPanel> управления из **элементов** на форму.  
  
4.  Щелкните глиф смарт тега. Выберите в контекстном меню, отображаемый рядом с глифом, **добавить столбец** элемента. Обратите внимание, что новый столбец будет добавлен <xref:System.Windows.Forms.TableLayoutPanel> элемента управления.  
  
5.  Перетащите <xref:System.Windows.Forms.SplitContainer> управления из **элементов** на форму.  
  
6.  Щелкните глиф смарт тега. Выберите в контекстном меню, отображаемый рядом с глифом, **Ориентация горизонтального разделителя** элемента. Обратите внимание, что <xref:System.Windows.Forms.SplitContainer> разделителя элемента управления — теперь горизонтальную ориентацию.  
  
## <a name="see-also"></a>См. также  
 <xref:System.Windows.Forms.TextBox>  
 <xref:System.Windows.Forms.TabControl>  
 <xref:System.Windows.Forms.SplitContainer>  
 <xref:System.ComponentModel.Design.DesignerActionList>  
 [Пошаговое руководство: Добавление смарт-тегов в компонент Windows Forms](https://msdn.microsoft.com/library/a6814169-fa7d-4527-808c-637ca5c95f63)
