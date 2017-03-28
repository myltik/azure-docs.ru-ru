---
title: "Метаданные приложений API службы приложений для обнаружения API и создания кода | Документация Майкрософт"
description: "Узнайте, как приложения API в службе приложений Azure используют метаданные Swagger для упрощения обнаружения API и создания кода."
services: app-service\api
documentationcenter: .net
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: c7f8e33a-61cc-486f-89df-4a97dc3c71d4
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: alkarche
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 92057ef0c40bfc79a66265d2d369d7abf72adc8e
ms.lasthandoff: 03/21/2017


---
# <a name="app-service-api-apps-metadata-for-api-discovery-and-code-generation"></a>Метаданные приложений API службы приложений для обнаружения API и создания кода
Поддержка метаданных API [Swagger 2.0](http://swagger.io/) встроена в приложения API службы приложений. Применение платформы Swagger не является обязательным, но оно дает возможность воспользоваться функциями приложений API, облегчающими ознакомление и использование.   

## <a name="swagger-endpoint"></a>Конечная точка Swagger
Можно указать конечную точку, которая передает метаданные JSON Swagger 2.0 для приложения API в свойствах приложения API. Конечная точка может соотноситься с базовым URL-адресом приложения API или с абсолютным URL-адресом. Абсолютные URL-адреса могут направлять на объекты за пределами приложения API. 

У нисходящих клиентов (например, создание кода в Visual Studio и поток «Добавление API» PowerApps) URL-адрес должен быть общедоступным, т. е. не защищен проверкой подлинности пользователя или службы. Это означает, что если вы используете проверку подлинности службы приложений и хотите предоставить определение API из самого приложения, необходимо использовать параметр проверки подлинности, который разрешает анонимный трафик к API. Дополнительные сведения см. в статье [Проверка подлинности и авторизация для приложений API службы приложений](app-service-api-authentication.md).

### <a name="portal-blade"></a>Колонка портала
На [портале Azure](https://portal.azure.com/) в колонке **Определение API** можно посмотреть и изменить URL-адрес конечной точки.

![](./media/app-service-api-metadata/apidefblade.png)

### <a name="azure-resource-manager-property"></a>Свойство диспетчера ресурсов Azure
URL-адрес определения API для приложения API можно также настроить с помощью [обозревателя ресурсов](https://resources.azure.com/) или [шаблонов диспетчера Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md), используя такие средства командной строки, как [Azure PowerShell](/powershell/azureps-cmdlets-docs) и [интерфейс командной строки Azure](../cli-install-nodejs.md). 

В **обозревателе ресурсов** выберите пункты **subscriptions > {ваша подписка} > resourceGroups > {ваша группа ресурсов} > providers > Microsoft.Web > sites > {ваш сайт} > config > web**. Отобразится свойство `apiDefinition`:

        "apiDefinition": {
          "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
        }

Чтобы просмотреть пример шаблона Azure Resource Manager, который задает свойство `apiDefinition` , откройте [в образце приложения списка дел файл azuredeploy.json](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Найдите раздел шаблона, который выглядит, как показанный выше образец JSON.

### <a name="default-value"></a>Значение по умолчанию
Если вы используете Visual Studio для создания приложений API, для конечной точки определения API автоматически задается URL-адрес приложения API в сочетании с `/swagger/docs/v1`. Это URL-адрес по умолчанию, используемый в пакете NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) для передачи динамически создаваемых метаданных Swagger в проект веб-API ASP.NET. 

## <a name="code-generation"></a>Создание кода
Одним из преимуществ интеграции платформы Swagger в приложения API Azure является возможность автоматического создания кода. Созданные классы клиента упрощают написание кода, который вызывает приложение API.

Код клиента для приложения API можно создать с помощью Visual Studio или из командной строки. Дополнительные сведения о том, как создавать классы клиента для проекта веб-API ASP.NET в Visual Studio, см. в статье [Приступая к работе с приложениями API и ASP.NET](app-service-api-dotnet-get-started.md#codegen). Дополнительные сведения о том, как делать это с помощью командной строки на всех поддерживаемых языках, см. в файле сведений репозитория [Azure/autorest](https://github.com/azure/autorest) на сайте GitHub.com.

## <a name="next-steps"></a>Дальнейшие действия
Пошаговое руководство по созданию, развертыванию и использованию приложений API см. в статье [Приступая к работе с приложениями API в службе приложений Azure](app-service-api-dotnet-get-started.md).

Если вы используете управление API Azure с приложениями API, вы можете использовать метаданные Swagger для импорта API в управление API. Дополнительные сведения см. в статье [Как импортировать определение API с операциями в Azure API Management](../api-management/api-management-howto-import-api.md). 


