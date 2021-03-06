---
title: Безопасное хранение секретов приложения во время разработки
description: Архитектура микрослужб .NET для упакованных в контейнеры приложений .NET | Безопасное хранение секретов приложения во время разработки
author: mjrousos
ms.author: wiwagn
ms.date: 05/26/2017
ms.openlocfilehash: 560120db35ae190bdef1f95d72ac1e5de697124e
ms.sourcegitcommit: 979597cd8055534b63d2c6ee8322938a27d0c87b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37105950"
---
# <a name="storing-application-secrets-safely-during-development"></a>Безопасное хранение секретов приложения во время разработки

Для подключения к защищенным ресурсам и другим службам приложения ASP.NET Core обычно используют строки подключения, пароли или другие учетные данные, содержащие конфиденциальные сведения. Эти конфиденциальные сведения называются *секреты*. Рекомендуется не включать секреты в исходный код и ни в коем случае не хранить секреты в системе управления версиями. Лучше использовать модель конфигурации ASP.NET Core для чтения секретов из более безопасных мест.

Следует отличать секреты для доступа к ресурсам разработки и промежуточных процессов от секретов для доступа к производственным ресурсам, поскольку их используют разные пользователи. Секреты, используемые на стадии разработки, как правило, хранятся в переменных среды или в менеджере секретов ASP.NET Core. Для большей безопасности в рабочих средах микрослужбы могут хранить секреты в хранилище Azure Key Vault.

## <a name="storing-secrets-in-environment-variables"></a>Хранение секретов в переменных среды

Чтобы хранить секреты отдельно от исходного кода, разработчики могут установить строки секретов в качестве [переменных среды](https://docs.microsoft.com/aspnet/core/security/app-secrets#environment-variables) на компьютерах, использующихся для разработки. При использовании переменных среды для хранения секретов с иерархическими именами (которые вложены в разделы конфигурации) создайте имя для переменных среды, включающее полную иерархию имени секрета, разделенную двоеточием (:).

Например, если установить для переменной среды Logging:LogLevel:Default значение Debug, это будет равноценно значению конфигурации из следующего файла JSON:

```json
{
    "Logging": {
        "LogLevel": {
            "Default": "Debug"
        }
    }
}
```

Чтобы обратиться к этим значениям из переменных среды, приложению достаточно вызвать AddEnvironmentVariables в ConfigurationBuilder при создании объекта IConfigurationRoot.

Как правило, переменные среды хранятся в виде обычного текста, и если компьютер или процесс с этими переменными среды будут скомпрометированы, значения переменных среды будут видны.

## <a name="storing-secrets-using-the-aspnet-core-secret-manager"></a>Хранение секретов с помощью менеджера секретов ASP.NET Core

[Менеджер секретов](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) ASP.NET Core — это еще один инструмент для хранения секретов отдельно от исходного кода. Чтобы использовать менеджер секретов, включите ссылку на инструмент (DotNetCliToolReference) в пакет Microsoft.Extensions.SecretManager.Tools в файле проекта. После назначения и восстановления этой зависимости можно установить значение секретов в командной строке с помощью команды dotnet user-secrets. Секреты будут храниться в файле JSON в каталоге профилей пользователей (в зависимости от ОС), отдельно от исходного кода.

Секреты, заданные менеджером секретов, упорядочиваются свойством UserSecretsId в проекте, использующем эти секреты. Поэтому обязательно задайте свойство UserSecretsId в файле проекта (как показано ниже во фрагменте кода). Строка, используемая в качестве идентификатора, не важна, если она уникальна для проекта.

```xml
<PropertyGroup>
    <UserSecretsId>UniqueIdentifyingString</UserSecretsId>
</PropertyGroup>
```

Секреты, хранящиеся в менеджере секретов, используются в приложении путем вызова AddUserSecrets&lt;T&gt; в экземпляре ConfigurationBuilder, чтобы включить секреты приложения в его конфигурацию. Общий параметр T должен обозначать тип в сборке, к которой применяется UserSecretId. Как правило, можно использовать AddUserSecrets&lt;Startup&gt;.


>[!div class="step-by-step"]
[Назад](authorization-net-microservices-web-applications.md)
[Вперед](azure-key-vault-protects-secrets.md)
