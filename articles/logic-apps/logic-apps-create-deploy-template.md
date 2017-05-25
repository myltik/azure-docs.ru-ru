---
title: "Создание шаблонов развертывания для Azure Logic Apps | Документация Майкрософт"
description: "Узнайте, как создавать шаблоны Azure Resource Manager для развертывания приложений логики и управлять выпусками."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: e736bf626cd379e1bffe2dbf023e86addb09539f
ms.contentlocale: ru-ru
ms.lasthandoff: 04/21/2017


---
# <a name="create-templates-for-logic-apps-deployment-and-release-management"></a>Создание шаблонов для развертывания приложений логики и управления выпусками

После создания приложения логики на его основе можно создать шаблон Azure Resource Manager.
Таким образом вы сможете легко развернуть приложение логики в любой среде или группе ресурсов, где это может потребоваться.
Дополнительные сведения о шаблонах Resource Manager см. в статьях [Создание шаблонов диспетчера ресурсов Azure](../azure-resource-manager/resource-group-authoring-templates.md) и [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Шаблон развертывания приложения логики

Приложение логики состоит из трех основных компонентов:

* **Ресурс приложения логики**: содержит такие сведения, как тарифный план, расположение и определение рабочего процесса.
* **Определение рабочего процесса**: описывает этапы рабочего процесса приложения логики и способ выполнения рабочего процесса обработчиком приложений логики.
Это определение можно просмотреть в окне **Представление кода** приложения логики.
В ресурсе приложения логики это определение можно найти в свойстве `definition`.
* **Подключения**: отдельные ресурсы для безопасного хранения метаданных, связанных со всеми подключениями соединителя, таких как строка подключения и маркер доступа.
Эти ресурсы указываются в разделе `parameters` ресурса приложения логики.

Вы можете просмотреть все эти компоненты имеющихся приложений логики с помощью таких инструментов, как [обозреватель ресурсов Azure](http://resources.azure.com).

Чтобы создать шаблон для приложения логики, который можно использовать для развертываний групп ресурсов, необходимо определить ресурсы и при необходимости параметризовать их.
Например, если развертывание осуществляется в тестовой, рабочей среде или в среде разработки, то скорее всего в каждой из этих сред потребуется использовать различные строки подключения к базе данных SQL.
Или выполнить развертывания в разных подписках или группах ресурсов.  

## <a name="create-a-logic-app-deployment-template"></a>Создание шаблона развертывания приложения логики

Самый простой способ создать допустимый шаблон развертывания приложения логики — это воспользоваться [инструментами Visual Studio для приложений логики](logic-apps-deploy-from-vs.md).
Инструменты Visual Studio создают допустимый шаблон развертывания, который можно использовать в разных подписках или расположениях.

При создании шаблона для развертывания приложения логики могут быть полезными и некоторые другие инструменты.
Его можно создать вручную, используя указанные выше ресурсы и настроив требуемые параметры.
Также можно использовать модуль PowerShell для [создания шаблона приложения логики](https://github.com/jeffhollan/LogicAppTemplateCreator) . Этот модуль с открытым исходным кодом оценивает приложение логики и все подключения, используемые в этом приложении, а затем создает ресурсы шаблона с необходимыми параметрами для развертывания.
Например, если приложение логики получает сообщение из очереди служебной шины Azure и добавляет данные в базу данных SQL Azure, то инструмент сохраняет всю логику оркестрации и параметризует строки подключения SQL и служебной шины, чтобы их можно было настроить во время развертывания.

> [!NOTE]
> Подключения должны относиться к той же группе ресурсов, что и приложение логики.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>Установка модуля PowerShell для шаблона приложения логики
Проще всего установить модуль через [коллекцию PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1) с использованием команды `Install-Module -Name LogicAppTemplate`.  

Также можно установить модуль PowerShell вручную:

1. Скачайте последний выпуск [модуля для создания шаблона приложения логики](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Извлеките содержимое папки в папку модуля PowerShell (обычно это `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Чтобы модуль поддерживал любой маркер доступа клиента или подписки, рекомендуется использовать программу командной строки [ARMClient](https://github.com/projectkudu/ARMClient).  В этой [записи блога](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) программа ARMClient рассматривается подробней.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Создание шаблона приложения логики с помощью PowerShell
После установки PowerShell можно создать шаблон, используя следующую команду:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` — это идентификатор подписки Azure. Эта строка сначала получает маркер доступа через программу ARMClient, затем передает его по конвейеру в сценарий PowerShell и создает шаблон в JSON-файл.

## <a name="add-parameters-to-a-logic-app-template"></a>Добавление параметров для шаблона приложения логики
После создания шаблона приложения логики можно добавлять или изменять любые дополнительные параметры. Например, если определение включает идентификатор ресурса в функции Azure или вложенном рабочем процессе, где планируется одно развертывание, можно добавить дополнительные ресурсы для шаблона и при необходимости параметризовать идентификаторы. То же касается и ссылок на пользовательские интерфейсы API или конечные точки Swagger, которые будут развернуты в каждой группе ресурсов.

## <a name="deploy-a-logic-app-template"></a>Развертывание шаблона приложения логики

Шаблон можно развернуть, используя любые инструменты, включая PowerShell, REST API, [Visual Studio Team Services Release Management](#team-services), а также функцию развертывания шаблона на портале Azure.
Кроме того, для сохранения значений параметров рекомендуется создать [файл параметров](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Узнайте, как [развертывать ресурсы с помощью шаблонов Azure Resource Manager и PowerShell](../azure-resource-manager/resource-group-template-deploy.md) или [развертывать ресурсы с помощью шаблонов Azure Resource Manager и портала Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

### <a name="authorize-oauth-connections"></a>Авторизация подключений OAuth

После развертывания приложение логики будет полноценно работать с допустимыми параметрами.
Но чтобы создать допустимый маркер доступа, необходимо авторизовать подключения OAuth.
Чтобы разрешить подключения OAuth, откройте приложение логики в конструкторе приложений логики и авторизуйте эти подключения. Или, если выполняется автоматизированное развертывание, можно использовать сценарий, предоставляющий разрешение на каждое подключение OAuth.
На GitHub есть пример сценария в проекте [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) .

<a name="team-services"></a>
## <a name="visual-studio-team-services-release-management"></a>Visual Studio Team Services Release Management

При развертывании среды и управлении ею часто используется инструмент Release Management в Visual Studio Team Services и шаблон развертывания приложения логики. Служба Visual Studio Team Services содержит задачу [развертывания группы ресурсов Azure](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup), которую можно добавить в любую сборку или конвейер выпуска. Для авторизации развертывания требуется [субъект-служба](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). При ее наличии можно создать определение выпуска.

1. Для создания пустого определения в Release Management выберите пункт **Пусто**.

    ![Создание пустого определения][1]

2. Выберите для этого любые ресурсы. Лучше всего подойдет шаблон приложения логики, созданный вручную или в процессе сборки.
3. Добавьте задачу **развертывания группы ресурсов Azure** .
4. Настройте использование [субъекта-службы](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)и добавьте ссылку на файлы шаблона и параметров шаблона.
5. При необходимости выполните соответствующие действия в процессе выпуска для других сред, автоматических тестов или механизмов утверждения.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png

