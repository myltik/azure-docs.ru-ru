---
title: "Создание, сборка и развертывание приложений логики в Visual Studio с помощью Azure Logic Apps | Документация Майкрософт"
description: "Узнайте, как создавать проекты Visual Studio для проектирования, создания и развертывания приложений логики с помощью Azure Logic Apps."
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 2/14/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 614e07ad9fcbe9ad3684ed977f7ffa36e727dddc
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="design-build-and-deploy-azure-logic-apps-in-visual-studio"></a>Проектирование, создание и развертывание приложений логики Azure в Visual Studio

Хотя [портал Azure](https://portal.azure.com/) позволяет легко создавать и администрировать приложения логики с помощью службы Azure Logic Apps, вы также можете использовать Visual Studio для проектирования, создания и развертывания приложений логики. Расширенные средства Visual Studio, такие как конструктор приложений логики, позволяют создавать приложения логики, настраивать шаблоны развертывания и автоматизации, а также развертывать приложения в любой среде. 

Если вы еще не знакомы с Azure Logic Apps, то см. статью [Создание нового приложения логики, подключающего службы SaaS](quickstart-create-first-logic-app-workflow.md).

## <a name="installation-steps"></a>Шаги установки

Ниже описаны действия по установке и настройке средств Visual Studio для Azure Logic Apps.

### <a name="prerequisites"></a>предварительным требованиям

* [Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) или Visual Studio 2015.
* [пакет Azure SDK последней версии](https://azure.microsoft.com/downloads/) (версия 2.9.1 или выше);
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* доступ к Интернету при использовании внедренного конструктора.

### <a name="install-visual-studio-tools-for-azure-logic-apps"></a>Установка средств Visual Studio для Azure Logic Apps

После установки необходимых компонентов выполните следующие действия.

1. Откройте Visual Studio. Откройте меню **Средства** и выберите пункт **Расширения и обновления**.
2. Разверните категорию **В сети**, чтобы выполнить поиск в Интернете.
3. Найдите **средства Azure Logic Apps Tools для Visual Studio**. Если потребуется, выполните поиск по названию **Logic Apps**.
4. Нажмите кнопку **Скачать**, чтобы скачать и установить это расширение.
5. После установки перезапустите Visual Studio.

> [!NOTE]
> Кроме того, [инструменты Azure Logic Apps для Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551) и [инструменты Azure Logic Apps для Visual Studio 2015](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio) можно скачать непосредственно в Visual Studio Marketplace.

Установив расширение, вы можете использовать проект группы ресурсов Azure в конструкторе Logic App.

## <a name="create-your-project"></a>Создание проекта

1. В меню **Файл** выберите пункт **Создать**, затем **Проект**. Чтобы добавить проект к существующему решению, выберите **Добавить**, а затем **Новый проект**.

    ![Меню «Файл»](./media/logic-apps-deploy-from-vs/filemenu.png)

2. В окне **Новый проект** найдите пункт **Облако** и выберите **Группа ресурсов Azure**. Присвойте проекту имя и нажмите кнопку **ОК**.

    ![Добавление нового проекта](./media/logic-apps-deploy-from-vs/addnewproject.png)

3. Выберите шаблон **Logic App**, который создаст для вас пустое развертывание приложения логики. Выбрав нужный шаблон, нажмите **ОК**.

    ![Выбор шаблона Logic App](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)

    Итак, вы добавили в решение проект приложения логики. 
    Теперь в обозревателе решений должен появиться файл развертывания.

    ![Файл развертывания](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="create-your-logic-app-with-logic-app-designer"></a>Создание приложения логики с помощью конструктора приложений логики

Когда у вас будет проект группы ресурсов Azure, который содержит приложение логики, вы можете открыть в Visual Studio конструктор Logic App для создания рабочего процесса. 

> [!NOTE]
> Конструктору требуется подключение к Интернету, чтобы получать информацию о доступных свойствах и их значениях от соединителей запроса. Например, если вы используете соединитель Dynamics CRM Online, конструктор запрашивает экземпляр CRM и получает информацию для отображения доступных свойств по умолчанию и пользовательских свойств.

1. Щелкните правой кнопкой мыши файл `<template>.json` и выберите пункт **Открыть в конструкторе Logic App**. (`Ctrl+L`)

2. Выберите подписку Azure, группу ресурсов и расположение шаблона развертывания.

    > [!NOTE]
    > При проектировании приложения логики будут создаваться ресурсы для подключения к API, которые используются для получения свойств. На этапе проектирования Visual Studio создает эти подключения в выбранной группе ресурсов. Чтобы просмотреть или изменить подключения API, выполните поиск по запросу **Подключения API** на портале Azure.

    ![Средство выбора подписки](./media/logic-apps-deploy-from-vs/designer_picker.png)

    Конструктор использует для подготовки к просмотру определение из файла `<template>.json`.

4. Создайте и подготовьте приложение логики. Шаблон развертывания обновляется в соответствии со всеми вносимыми изменениями.

    ![Конструктор Logic App в Visual Studio](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

Visual Studio добавляет в файл ресурсов ресурс `Microsoft.Web/connections` для каждого подключения, которое необходимо для работы приложения логики. Эти свойства подключения можно задать во время развертывания. Затем вы сможете управлять ими с помощью элемента **Подключения API** на портале Azure.

### <a name="switch-to-json-code-view"></a>Переключение к представлению кода JSON

Чтобы перейти к представлению кода JSON для приложения логики, выберите вкладку **Представление кода** в нижней части конструктора.

Чтобы вернуться к полному представлению ресурса JSON, щелкните правой кнопкой мыши файл `<template>.json` и выберите пункт **Открыть**.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Добавление ссылок на зависимые ресурсы в шаблоны развертывания Visual Studio

Если нужно, чтобы приложение логики ссылалось на зависимые ресурсы, вы можете использовать [функции шаблонов Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) в шаблоне развертывания приложения логики. Например, приложение логики может ссылаться на определенную функцию Azure или учетную запись интеграции, которая должна автоматически развертываться вместе с приложением логики. Здесь приводятся рекомендации по использованию параметров в шаблоне развертывания, которые позволят конструктору Logic App правильно их отображать. 

Параметры приложений логики можно использовать в следующих типах триггеров и действий.

*   Дочерний рабочий процесс
*   Приложение-функция
*   Вызов APIM
*   URL-адрес API среды выполнения
*   Путь для подключения к API.

Вы также можете использовать функции шаблонов: parameters, variables, resourceId, concat и т. п. Например, этот пример кода может заменить идентификатор ресурса функции Azure:

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

А так вы можете использовать параметры:

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
В качестве другого примера можно параметризовать операцию отправки сообщений в служебной шине:

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> Параметр host.runtimeUrl является необязательным, его можно удалить из шаблона.
> 


> [!NOTE] 
> Чтобы конструктор Logic App мог работать с этими параметрами, ему нужно предоставить значения по умолчанию, например так:
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

### <a name="save-your-logic-app"></a>Сохранение приложения логики

Вы можете в любой момент сохранить приложение логики, выбрав пункт меню **Файл** > **Сохранить**. (`Ctrl+S`) 

Если на момент сохранения приложение логики содержит какие-либо ошибки, они отобразятся в окне **Вывод** Visual Studio.

## <a name="deploy-your-logic-app-from-visual-studio"></a>Развертывание приложения логики из Visual Studio

Когда настройка приложения логики будет завершена, вы можете развернуть его, выполнив несколько простых действий напрямую из Visual Studio. 

1. В обозревателе решений щелкните проект правой кнопкой мыши и выберите **Развернуть** > **Новое развертывание**.

    ![Новое развертывание](./media/logic-apps-deploy-from-vs/newdeployment.png)

2. Войдите в подписку Azure, когда появится соответствующее предложение. 

3. Теперь вам нужно выбрать сведения о группе ресурсов, в которой вы хотите развернуть приложение логики. Закончив, щелкните **Развернуть**.

    > [!NOTE]
    > Убедитесь, что выбраны правильные шаблон и файл параметров для группы ресурсов. Например, если развертывание выполняется в рабочей среде, выберите файл параметров для рабочей среды.

    ![Развертывание в группе ресурсов](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)

    Состояние развертывания отображается в окне **Вывод**. 
    Возможно, в списке **Показать вывод из** вам потребуется выбрать **Подготовка Azure**.

    ![Вывод информации о состоянии развертывания](./media/logic-apps-deploy-from-vs/output.png)

После развертывания вы всегда можете изменить приложение логики в системе управления версиями и развернуть новую версию с помощью Visual Studio.

> [!NOTE]
> Если вы измените определение непосредственно на портале Azure, при следующем развертывании из Visual Studio эти изменения будут перезаписаны. 

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>Добавление приложения логики в существующий проект группы ресурсов

Если у вас уже есть проект группы ресурсов, вы можете добавить в него приложение логики в окне "Структура JSON". Рядом с созданным ранее приложением логики можно добавить новые приложения.

1. Откройте файл `<template>.json` .

2. Откройте окно "Структура JSON", открыв меню **Вид** > **Другие окна** > **Структура JSON**.

3. Чтобы добавить ресурс в файл шаблона, щелкните **добавить ресурс** в верхней части окна "Структура JSON". Также можно щелкнуть правой кнопкой мыши **ресурсы** в окне "Структура JSON", а затем выбрать пункт **Добавить новый ресурс**.

    ![Окно "Структура JSON"](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. В диалоговом окне **Добавление ресурса** найдите и выберите **Приложение логики**. Присвойте имя приложению логики и выберите **Добавить**.

    ![Добавление ресурса](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Управление Logic Apps с помощью Visual Studio Cloud Explorer](logic-apps-manage-from-vs.md)
* [Примеры приложений логики и распространенные сценарии](logic-apps-examples-and-scenarios.md)
* Узнайте, как можно [автоматизировать бизнес-процессы с помощью Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694).
* Изучите вопросы [интеграции систем с Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462).
