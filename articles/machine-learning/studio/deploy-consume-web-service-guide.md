---
title: Развертывание и использование веб-служб машинного обучения Azure | Документация Майкрософт
description: Ресурсы для развертывания и использования веб-служб.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 47635376-d1f4-4ea4-a6af-bd1f99f69a69
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: cc5de000f4a76498654ef85efd818eece70090fc
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834181"
---
# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Развертывание и использование веб-служб Машинного обучения Azure
Машинное обучение Azure позволяет развертывать рабочие процессы и модели машинного обучения в качестве веб-служб. Затем эти веб-службы можно использовать для вызова моделей машинного обучения из приложений в Интернете, чтобы делать прогнозы в режиме реального времени или в пакетном режиме. Так как это веб-службы RESTful, их можно вызывать, используя различные языки программирования и платформы, например .NET и Java, а также приложения, например Microsoft Excel.

В следующих разделах представлены ссылки на пошаговые инструкции, код и документацию, которые помогут вам приступить к работе.

## <a name="deploy-a-web-service"></a>Развертывание веб-службы

### <a name="with-azure-machine-learning-studio"></a>С помощью Студии машинного обучения Azure
На портале Студии машинного обучения и веб-служб Машинного обучения Microsoft Azure можно развернуть веб-службы, а также управлять ими без написания кода.

В статьях по следующим ссылкам содержатся общие сведения о процессе развертывания новой веб-службы:

* Общие сведения о развертывании новой веб-службы на основе Azure Resource Manager см. в статье [Развертывание новой веб-службы](publish-a-machine-learning-web-service.md).
* Пошаговое руководство по развертыванию веб-службы см. в статье [Развертывание веб-службы машинного обучения Azure](publish-a-machine-learning-web-service.md).
* Полное пошаговое руководство по созданию и развертыванию веб-службы см. в статье [Шаг 1. Создание рабочей области машинного обучения](walkthrough-1-create-ml-workspace.md).
* Конкретные примеры развертывания веб-службы см. в следующих статьях:

  * [Шаг 5. Развертывание веб-службы машинного обучения Azure](walkthrough-5-publish-web-service.md)
  * [Развертывание веб-службы в нескольких регионах](how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>С помощью интерфейсов API поставщика ресурсов веб-служб (интерфейсов API Azure Resource Manager)
Поставщик ресурсов Машинного обучения Azure для веб-служб позволяет развертывать веб-службы и управлять ими с помощью вызовов REST API. Дополнительные сведения см. в статье [Machine Learning Web Service (REST)](/rest/api/machinelearning/index) (Веб-служба машинного обучения (REST)).

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->


### <a name="with-powershell-cmdlets"></a>С помощью командлетов PowerShell
Поставщик ресурсов Машинного обучения Azure для веб-служб позволяет развертывать веб-службы и управлять ими с помощью командлетов PowerShell.

Чтобы использовать командлеты, необходимо сначала войти в учетную запись Azure в среде PowerShell с помощью командлета [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). Если вы не знакомы с вызовом команд PowerShell на основе Resource Manager, см. статью [Использование Azure PowerShell с Azure Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md).

Чтобы экспортировать прогнозный эксперимент, используйте [этот пример кода](https://github.com/ritwik20/AzureML-WebServices). После создания EXE-файла из кода можно ввести следующую команду:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

При запуске приложения создается шаблон JSON веб-службы. Чтобы использовать шаблон для развертывания веб-службы, необходимо добавить следующие сведения:

* Имя и ключ учетной записи хранения.

    Их можно получить на [портале Azure](https://portal.azure.com/).
* Идентификатор плана предложения.

    Идентификатор плана можно получить на портале [веб-служб машинного обучения Azure](https://services.azureml.net). Для этого необходимо войти на портал и щелкнуть имя плана.

Добавьте их в шаблон JSON в качестве дочерних элементов узла *Properties* на том же уровне, где находится узел *MachineLearningWorkspace*.

Ниже приведен пример:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Дополнительные сведения см. в следующих статьях и примерах кода:

* [Azure Machine Learning Cmdlets](https://msdn.microsoft.com/library/azure/mt767952.aspx) (Командлеты Машинного обучения Azure) на сайте MSDN.
* Пример [пошагового руководства](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) на сайте GitHub.

## <a name="consume-the-web-services"></a>Использование веб-служб
### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Через пользовательский интерфейс веб-служб Машинного обучения Azure (тестирование)
Веб-службу можно проверить на портале веб-служб Машинного обучения Azure. Эта проверка включает в себя тестирование интерфейсов службы запрос-ответ (RRS) и службы пакетного выполнения (BES).

* [Развертывание новой веб-службы](publish-a-machine-learning-web-service.md)
* [Развертывание веб-службы машинного обучения Azure](publish-a-machine-learning-web-service.md)
* [Шаг 5. Развертывание веб-службы машинного обучения Azure](walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>Из Excel
Вы можете скачать шаблон Excel, который использует веб-службу:

* [Использование веб-службы Машинного обучения Azure в Excel](consuming-from-excel.md)
* [Надстройка Excel для веб-служб машинного обучения Azure](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Из клиента на основе REST
Веб-службы Машинного обучения Azure представляют собой интерфейсы API RESTful. Их можно использовать на различных платформах, например .NET, Python, R, Java и т. д. На странице **Consume** (Использование) для веб-службы на [портале веб-служб машинного обучения Microsoft Azure](https://services.azureml.net) размещен пример кода, позволяющий приступить к работе. Дополнительные сведения см. в статье [Как использовать веб-службу машинного обучения Azure, развернутую из эксперимента машинного обучения](consume-web-services.md).
