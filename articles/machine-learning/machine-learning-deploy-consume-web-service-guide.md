.<properties
	pageTitle="Развертывание и использование веб-служб машинного обучения Azure | Microsoft Azure"
	description="Ресурсы для развертывания и использования веб-служб."
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="raymondl"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="v-donglo"/>

# Развертывание и использование веб-служб машинного обучения Azure

Машинное обучение Azure позволяет развертывать рабочие процессы и модели машинного обучения в качестве веб-служб. Затем эти веб-службы можно использовать для вызова моделей машинного обучения из приложений в Интернете, чтобы делать прогнозы в режиме реального времени или в пакетном режиме. Так как это веб-службы RESTFul, их можно вызывать, используя различные языки программирования и платформы, например .NET и Java, а также приложения, например Microsoft Excel.

В следующих разделах описаны шаги, а также представлены ссылки на код и документация, позволяющая приступить к работе.

## Развернуть

### Использование Студии машинного обучения Azure

На портале Студии машинного обучения и веб-служб Машинного обучения Microsoft Azure можно развернуть веб-службы, а также управлять ими без написания кода.

В статьях по следующим ссылкам содержатся общие сведения о процессе развертывания новой веб-службы:

* Обзор развертывания новой веб-службы на основе Azure Resource Manager см. в статье [Развертывание новой веб-службы](machine-learning-webservice-deploy-a-web-service.md).
* Пошаговое руководство по развертыванию веб-службы см. в статье [Развертывание веб-службы машинного обучения Azure](machine-learning-publish-a-machine-learning-web-service.md).

Полное пошаговое руководство по созданию и развертыванию веб-службы см. в статье [Шаг 1. Создание рабочей области машинного обучения](machine-learning-walkthrough-1-create-ml-workspace.md).

Конкретные примеры развертывания веб-службы см. в статьях ниже:

* [Шаг 5. Развертывание веб-службы машинного обучения Azure](machine-learning-walkthrough-5-publish-web-service.md)
* [Развертывание веб-службы в нескольких регионах](machine-learning-how-to-deploy-to-multiple-regions.md)

### Использование интерфейсов API поставщика ресурсов веб-служб (интерфейсов API Azure Resource Manager)

Поставщик ресурсов Машинного обучения Azure для веб-служб позволяет развертывать веб-службы и управлять ими с помощью вызовов REST API. Дополнительные сведения см. в следующих статьях и примерах кода.

* Справочная статья [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) (Веб-служба машинного обучения (REST)) на сайте MSDN.


### Использование командлетов PowerShell

Поставщик ресурсов Машинного обучения Azure для веб-служб позволяет развертывать веб-службы и управлять ими с помощью командлетов PowerShell.

Для использования командлетов необходимо сначала войти в учетную запись Azure в среде PowerShell с помощью командлета [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx). Если вы не знакомы с вызовом команд PowerShell с помощью Resource Manger, см. статью [Использование Azure PowerShell с Azure Resource Manager](../powershell-azure-resource-manager.md#login-to-your-azure-account).

Чтобы экспортировать прогнозный эксперимент, используйте этот [пример кода](https://github.com/ritwik20/AzureML-WebServices). После создания EXE-файла из кода можно ввести следующее:

	C:<folder>\GetWSD <experiment-url> <workspace-auth-token>

При запуске приложения создается шаблон JSON веб-службы. Чтобы использовать шаблон для развертывания веб-службы, необходимо добавить следующие сведения:

* Имя и ключ учетной записи хранения.
	* Их можно получить на новом или классическом портале Azure.
* Идентификатор плана предложения.
	* Идентификатор плана можно получить на портале [веб-служб Машинного обучения Azure](https://services.azureml.net). Для этого нужно войти на портал и щелкнуть имя плана.

Добавьте их в шаблон JSON в качестве дочерних элементов узла *Properties* на том же уровне, где находится узел *MachineLearningWorkspace*.

См. пример ниже.

	"StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
	},
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Дополнительные сведения см. в следующих статьях и примерах кода.

* Справочная статья [Azure Machine Learning Cmdlets](https://msdn.microsoft.com/library/azure/mt767952.aspx) (Командлеты Машинного обучения Azure) на сайте MSDN.
* Пример [пошагового руководства](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) на сайте GitHub.

## Использование веб-служб

### Из пользовательского интерфейса веб-служб Машинного обучения Azure (тестирование)

Веб-службу можно проверить на портале веб-служб Машинного обучения Azure. Вы можете проверить интерфейсы RRS и BES.

* [Развертывание новой веб-службы](machine-learning-webservice-deploy-a-web-service.md)
* [Развертывание веб-службы машинного обучения Azure](machine-learning-publish-a-machine-learning-web-service.md)
* [Шаг 5. Развертывание веб-службы машинного обучения Azure](machine-learning-walkthrough-5-publish-web-service.md)

### Из Excel

Вы можете скачать шаблон Excel, который упрощает использование веб-службы:

* [Использование веб-службы Машинного обучения Azure в Excel](machine-learning-consuming-from-excel.md)
* [Надстройка Excel для веб-служб машинного обучения Azure](machine-learning-excel-add-in-for-web-services.md)


### Использование клиента на базе REST

Веб-службы Машинного обучения Azure представляют собой интерфейсы API RESTful. Их можно использовать на различных платформах, например .NET, Python, R, Java и т. д. На странице "Использование" для веб-службы на [портале веб-служб Машинного обучения Microsoft Azure](https://services.azureml.net) есть пример кода, позволяющий приступить к работе.

* [Как использовать веб-службу машинного обучения Azure, развернутую из эксперимента машинного обучения](machine-learning-consume-web-services.md)

<!---HONumber=AcomDC_0824_2016-->