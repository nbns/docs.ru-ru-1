---
title: Практическое руководство. Сортировка данных в представлении
ms.date: 03/30/2017
dev_langs:
- csharp
- vb
helpviewer_keywords:
- data binding [WPF], sorting data in views
- data binding [WPF], grouping data in views
- grouping data in views [WPF]
- views [WPF], sorting data
- views [WPF], grouping data
- sorting data in views [WPF]
ms.assetid: f4c43578-01b7-4774-a953-acb95a13b94a
ms.openlocfilehash: 5c79261983fcf6200120bcfbf180d155aca3c3c9
ms.sourcegitcommit: 3d5d33f384eeba41b2dff79d096f47ccc8d8f03d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33556761"
---
# <a name="how-to-sort-data-in-a-view"></a>Практическое руководство. Сортировка данных в представлении
В этом примере описывается сортировка данных в представлении.  
  
## <a name="example"></a>Пример  
 В следующем примере создается простой <xref:System.Windows.Controls.ListBox> и <xref:System.Windows.Controls.Button>:  
  
 [!code-xaml[ListBoxSort_snip#HowTo](../../../../samples/snippets/csharp/VS_Snippets_Wpf/ListBoxSort_snip/CSharp/Window1.xaml#howto)]  
  
 <xref:System.Windows.Controls.Primitives.ButtonBase.Click> Обработчика событий кнопки содержит логику для сортировки элементов в <xref:System.Windows.Controls.ListBox> в порядке убывания. Это можно сделать, так как добавление элементов в <xref:System.Windows.Controls.ListBox> таким образом они добавляются в <xref:System.Windows.Controls.ItemCollection> из <xref:System.Windows.Controls.ListBox>, и <xref:System.Windows.Controls.ItemCollection> является производным от <xref:System.Windows.Data.CollectionView> класса. При привязке к <xref:System.Windows.Controls.ListBox> коллекцию с помощью <xref:System.Windows.Controls.ItemsControl.ItemsSource%2A> свойства, можно использовать ту же методику для сортировки.  
  
 [!code-csharp[ListBoxSort_snip#HowToCode](../../../../samples/snippets/csharp/VS_Snippets_Wpf/ListBoxSort_snip/CSharp/Window1.xaml.cs#howtocode)]
 [!code-vb[ListBoxSort_snip#HowToCode](../../../../samples/snippets/visualbasic/VS_Snippets_Wpf/ListBoxSort_snip/visualbasic/window1.xaml.vb#howtocode)]  
  
 Пока имеется ссылка на объект представления можно использовать ту же методику для сортировки содержимого других представлений коллекции. Пример получения представления см. в разделе [просмотреть представление по умолчанию сбор данных](../../../../docs/framework/wpf/data/how-to-get-the-default-view-of-a-data-collection.md). Еще один пример см. в разделе [сортировки GridView столбца по щелчку заголовка](../../../../docs/framework/wpf/controls/how-to-sort-a-gridview-column-when-a-header-is-clicked.md). Дополнительные сведения о представлениях см [Общие сведения о привязке данных](../../../../docs/framework/wpf/data/data-binding-overview.md).  
  
 Пример применения логики сортировки в [!INCLUDE[TLA#tla_xaml](../../../../includes/tlasharptla-xaml-md.md)], в разделе [сортировки и группы с помощью представления в XAML](../../../../docs/framework/wpf/data/how-to-sort-and-group-data-using-a-view-in-xaml.md).  
  
## <a name="see-also"></a>См. также  
 <xref:System.Windows.Data.ListCollectionView.CustomSort%2A>  
 [Сортировка столбцов GridView при нажатии на заголовок](../../../../docs/framework/wpf/controls/how-to-sort-a-gridview-column-when-a-header-is-clicked.md)  
 [Общие сведения о привязке данных](../../../../docs/framework/wpf/data/data-binding-overview.md)  
 [Фильтрация данных в представлении](../../../../docs/framework/wpf/data/how-to-filter-data-in-a-view.md)  
 [Разделы практического руководства](../../../../docs/framework/wpf/data/data-binding-how-to-topics.md)
