---
title: набор сущностей
ms.date: 03/30/2017
ms.assetid: 59ec6ab0-88e5-4d25-b112-7a4eccbe61f0
ms.openlocfilehash: 5a8b4bdac2d0cb2065438bb390c002fcb690b1f6
ms.sourcegitcommit: 11f11ca6cefe555972b3a5c99729d1a7523d8f50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32764142"
---
# <a name="entity-set"></a>набор сущностей
*Набора сущностей* — это логический контейнер для экземпляров [тип сущности](../../../../docs/framework/data/adonet/entity-type.md) и экземпляров любого типа, производного от этого типа сущности. (Сведения о производных типов см. в разделе [модель EDM: наследование](../../../../docs/framework/data/adonet/entity-data-model-inheritance.md).) Связь между типом сущности и набором сущностей схожа со связью между строкой и таблицей в реляционной базе данных. Как и строка, тип сущности описывает структуру данных, и, как и таблица, набор сущностей содержит экземпляры данной структуры. Набор сущностей не является конструктом моделирования данных; он не описывает структуру данных. Вместо этого набор сущностей обеспечивает конструкт для среды размещения или хранения (например, для среды CLR или базы данных сервера SQL), позволяя группировать экземпляры типа сущности так, чтобы они были сопоставлены хранилищу данных.  
  
 Набор сущностей, определенных в [контейнер сущностей](../../../../docs/framework/data/adonet/entity-container.md), который является логической группой наборов сущностей и [наборов ассоциаций](../../../../docs/framework/data/adonet/association-set.md).  
  
 Чтобы экземпляр типа сущности существовал в наборе сущности, должны быть выполнены следующие условия.  
  
-   Тип экземпляра является либо тем же, что и тип сущности, в котором находится набор сущностей, либо подтипом типа сущности.  
  
-   [Ключ сущности](../../../../docs/framework/data/adonet/entity-key.md) для экземпляра является уникальным в пределах набора сущностей.  
  
-   Экземпляр не существует ни в каком другом наборе сущностей.  
  
    > [!NOTE]
    >  Несколько наборов сущностей могут быть определены при помощи одного и того же типа сущности, однако экземпляр одного типа сущности может существовать только в одном наборе сущностей.  
  
 Нет необходимости определять набор сущностей для каждого типа сущности в концептуальной модели.  
  
## <a name="example"></a>Пример  
 На приведенной ниже схеме показана концептуальная модель с тремя типами сущностей: `Book`, `Publisher` и `Author`.  
  
 ![Пример модели](../../../../docs/framework/data/adonet/media/examplemodel.gif "ExampleModel")  
  
 На следующей схеме показаны два набора сущностей (`Books` и `Publishers`) и набор ассоциаций (`PublishedBy`), основанный на приведенной выше концептуальной модели. Бизнес-аналитики в `Books` набор сущностей представляет экземпляр `Book` тип сущности во время выполнения. Подобным образом представляют собой Pj `Publisher` экземпляра в `Publishers` набора сущностей. BiPj представляет экземпляр `PublishedBy` ассоциации в `PublishedBy` набора ассоциаций.  
  
 ![Задает пример](../../../../docs/framework/data/adonet/media/setsexample.gif "SetsExample")  
  
 [ADO.NET Entity Framework](../../../../docs/framework/data/adonet/ef/index.md) использует доменный язык (DSL), называемый языком определения концептуальной схемы ([CSDL](../../../../docs/framework/data/adonet/ef/language-reference/csdl-specification.md)) для определения концептуальных моделей. Далее язык CSDL определяет контейнер сущностей с одним набором сущностей для каждого типа сущностей в приведенной выше концептуальной модели. Обратите внимание, что имя и тип сущности для каждого набора сущностей определены при помощи атрибутов XML.  
  
 [!code-xml[EDM_Example_Model#EntityContainerExample](../../../../samples/snippets/xml/VS_Snippets_Data/edm_example_model/xml/books.edmx#entitycontainerexample)]  
  
 Предусмотрена возможность определять несколько наборов сущностей на тип (модель MEST). Далее язык CSDL определяет контейнер сущностей с двумя наборами сущностей для типа сущности `Book`.  
  
 [!code-xml[EDM_Example_Model#MESTExample](../../../../samples/snippets/xml/VS_Snippets_Data/edm_example_model/xml/books2.edmx#mestexample)]  
  
## <a name="see-also"></a>См. также  
 [Основные понятия модели EDM](../../../../docs/framework/data/adonet/entity-data-model-key-concepts.md)  
 [Сущностная модель данных](../../../../docs/framework/data/adonet/entity-data-model.md)
