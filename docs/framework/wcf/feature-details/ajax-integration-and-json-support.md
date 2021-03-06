---
title: Интеграция с AJAX и поддержка JSON
ms.date: 03/30/2017
helpviewer_keywords:
- AJAX integration and JSON support [WCF]
ms.assetid: 3851a8fc-d861-4ac1-873c-96af0343d3a7
ms.openlocfilehash: bcf1cab9386d9d9503af6258c1bb39f8744c073b
ms.sourcegitcommit: fb78d8abbdb87144a3872cf154930157090dd933
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47210192"
---
# <a name="ajax-integration-and-json-support"></a>Интеграция с AJAX и поддержка JSON
Поддержка Windows Communication Foundation (WCF) для ASP.NET Asynchronous JavaScript and XML (AJAX) и формат данных JavaScript Object Notation (JSON) позволяют службам WCF предоставлять операции клиентам AJAX. Клиенты AJAX представляют собой веб-страниц, в которых выполняется код JavaScript и доступа к этим службам WCF, с помощью HTTP-запросов. В этом разделе приведены сведения о такой поддержке и способах ее реализации.  
  
 Дополнительные сведения о ASP.NET AJAX и его интеграции с ASP.NET 2.0, см. в разделе [Общие сведения о ASP.NET AJAX](https://go.microsoft.com/fwlink/?LinkId=96725).  
  
## <a name="in-this-section"></a>В этом разделе  
 [Создание служб WCF для ASP.NET AJAX](../../../../docs/framework/wcf/feature-details/creating-wcf-services-for-aspnet-ajax.md)  
 Описывает, как это служба WCF доступные клиентам AJAX, добавив соответствующую конечную точку AJAX, либо через конфигурацию или с помощью фабрики узла службы, настроенного на создание узла службы, который автоматически настраивает конечную точку AJAX.  
  
 [Создание служб WCF AJAX без использования ASP.NET](../../../../docs/framework/wcf/feature-details/creating-wcf-ajax-services-without-aspnet.md)  
 В этой статье описывается создание службы WCF без использования ASP.NET.  
  
 [Поддержка JSON и других форматов передачи данных](../../../../docs/framework/wcf/feature-details/support-for-json-and-other-data-transfer-formats.md)  
 Описывается поддержка формата JSON, который обычно используется для обмена сообщениями со службами ASP.NET AJAX (вместо XML).  
  
 [Практическое руководство. Миграция веб-служб ASP.NET с поддержкой AJAX на платформу WCF](../../../../docs/framework/wcf/feature-details/how-to-migrate-ajax-enabled-aspnet-web-services-to-wcf.md)  
 В этой статье описывается миграция службы веб-служб с поддержкой AJAX ASP.NET в WCF веб-службы.  
  
## <a name="see-also"></a>См. также  
 <xref:System.ServiceModel.Activation.WebScriptServiceHostFactory>  
 [Модель веб-программирования HTTP WCF](../../../../docs/framework/wcf/feature-details/wcf-web-http-programming-model.md)
