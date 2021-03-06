---
title: Интеграция с приложениями COM
ms.date: 03/30/2017
helpviewer_keywords:
- Windows Communication Foundation, COM integration
- COM [WCF], Windows Communication Foundation integration
- WCF, reusing code
- Windows Communication Foundation, reusing code
- COM [WCF]
- WCF, COM integration
ms.assetid: c98bda3e-6779-419e-8e6d-9aa94053026d
ms.openlocfilehash: 292892ef572bd63fff055aeed88073573fadb934
ms.sourcegitcommit: 3d5d33f384eeba41b2dff79d096f47ccc8d8f03d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33491846"
---
# <a name="integrating-with-com-applications"></a>Интеграция с приложениями COM
Службы Windows Communication Foundation (WCF) могут интегрироваться прямо в существующий код с помощью моникера службы WCF. Моникер служб можно использовать в широком наборе сред разработки на базе модели COM, например в Office VBA, Visual Basic 6.0 и Visual C++ 6.0.  
  
## <a name="in-this-section"></a>В этом разделе  
 [Общие сведения об интеграции с приложениями COM](../../../../docs/framework/wcf/feature-details/integrating-with-com-applications-overview.md)  
 Предоставляет общие сведения об основных компонентах процесса объединения.  
  
 [Практическое руководство. Регистрация и настройка моникера службы](../../../../docs/framework/wcf/feature-details/how-to-register-and-configure-a-service-moniker.md)  
 Чтобы использовать моникер службы WCF в приложении COM, зарегистрируйте необходимые типы с атрибутами с помощью COM и настроить приложение COM и моникер в соответствии с необходимой конфигурацией привязки.  
  
 [Практическое руководство. Использование моникера службы Windows Communication Foundation без регистрации](../../../../docs/framework/wcf/feature-details/use-the-wcf-service-moniker-without-registration.md)  
 Содержит объяснения, как получить определение контракта в форме документа языка описания веб-служб (WSDL) или из конечной точки WS-MetadataExchange.  
  
 [Практическое руководство. Использование моникера службы с контрактами WSDL](../../../../docs/framework/wcf/feature-details/how-to-use-a-service-moniker-with-wsdl-contracts.md)  
 Описывает, как вызвать образец WCF, с помощью моникера WSDL для службы WCF.  
  
 [Практическое руководство. Использование моникера службы с контрактами обмена метаданными](../../../../docs/framework/wcf/feature-details/how-to-use-a-service-moniker-with-metadata-exchange-contracts.md)  
 Описывает, как вызвать образец WCF, с помощью моникера WCF, указывающего конечную точку обмена метаданными.  
  
 [Практическое руководство. Задание учетных данных безопасности канала](../../../../docs/framework/wcf/feature-details/how-to-specify-channel-security-credentials.md)  
 Моникер службы WCF поддерживает `IChannelCredentials` интерфейс, который обеспечивает ряд альтернативных методов указания учетных данных канала.  
  
## <a name="reference"></a>Ссылка  
 <xref:System.ServiceModel>  
  
## <a name="see-also"></a>См. также  
 [Интеграция с приложениями COM+](../../../../docs/framework/wcf/feature-details/integrating-with-com-plus-applications.md)
