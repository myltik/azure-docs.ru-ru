---
title: "Разработка крупномасштабных решений для параллельной обработки с помощью API-интерфейсов и средств пакетной службы Azure | Документация Майкрософт"
description: "Сведения об API-интерфейсах и средствах, доступных для разработки решений с помощью пакетной службы Azure."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 405cb202e2b6da0dd24e6f7b8ed55ce8c56e0fe1
ms.lasthandoff: 03/31/2017

---


# <a name="overview-of-batch-apis-and-tools"></a>Общие сведения об API-интерфейсах и средствах пакетной службы

Обычно обработка параллельных рабочих нагрузок, использующих пакетную службу Azure, выполняется программным способом с помощью [API-интерфейсов пакетной службы](#batch-development-apis). Ваше клиентское приложение или служба могут использовать API-интерфейсы пакетной службы для взаимодействия с пакетной службой. С помощью API-интерфейсов пакетной службы можно создавать пулы вычислительных узлов виртуальные машины или облачные службы, а также управлять этими ресурсами. Вы можете запланировать выполнение заданий и задач на этих узлах. 

Вы можете эффективно обрабатывать крупномасштабные рабочие нагрузки в своей организации или предоставлять внешние интерфейсы служб клиентам, чтобы они могли выполнять задания и задачи (по требованию или по расписанию) на одном, сотнях или тысячах узлов. Кроме того, пакетную службу Azure можно использовать как часть более крупного рабочего процесса под управлением таких средств, как [фабрика данных Azure](../data-factory/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Дополнительные сведения о возможностях, которые предоставляет API пакетной службы, см. в статье [Обзор функций пакетной службы Azure](batch-api-basics.md).
> 
> 

## <a name="azure-accounts-youll-need"></a>Учетные записи Azure, которые вам понадобятся
При разработке решений с использованием пакетной службы требуются следующие учетные записи в Microsoft Azure.

* **Учетная запись и подписка Azure.** Если у вас еще нет подписки Azure, вы можете активировать [преимущества для подписчиков MSDN][msdn_benefits] или зарегистрироваться для получения [бесплатной учетной записи Azure][free_account]. При создании учетной записи автоматически создается подписка по умолчанию.
* **Учетная запись пакетной службы** — это ресурсы пакетной службы Azure, в том числе пулы, вычислительные узлы, задания и задачи, связанные с учетной записью пакетной службы Azure. Когда приложение отправляет запрос к пакетной службе, выполняется проверка подлинности запроса с использованием имени учетной записи пакетной службы Azure, URL-адреса учетной записи и ключа доступа. Вы можете создать [учетную запись пакетной службы](batch-account-create-portal.md) на портале Azure.
* **Учетная запись хранения.** В пакетную службу встроена поддержка работы с файлами в [службе хранилища Azure][azure_storage]. При работе с пакетной службой хранилище BLOB-объектов Azure используется преимущественно не только для промежуточного хранения файлов программ и данных (запускаются и обрабатываются задачами соответственно), но и для хранения выходных данных (результаты выполнения задач). Чтобы создать учетную запись хранения, см. инструкции в статье [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md).

## <a name="batch-development-apis"></a>API-интерфейсы для разработки в пакетной службе
Приложения и службы могут напрямую вызывать REST API или использовать следующие клиентские библиотеки для выполнения рабочих нагрузок пакетной службы Azure и управления ими.

| API | Справочник по API | Загрузить | Учебник | Примеры кода | Подробнее |
| --- | --- | --- | --- | --- | --- |
| **Пакетная служба (REST)** |[MSDN][batch_rest] |Недоступно |- |- | [Поддерживаемые версии](https://docs.microsoft.com/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Пакетная служба (.NET)** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[Руководство](batch-dotnet-get-started.md) |[GitHub][api_sample_net] | [Заметки о выпуске](https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/Client/changelog.md) |
| **Пакетная служба Python** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[Руководство](batch-python-tutorial.md)|[GitHub][api_sample_python] | [Файл сведений](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Node.js для пакетной службы** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- | [Файл сведений](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Java для пакетной службы** (предварительная версия) |[github.io][api_java] |[Maven][api_java_jar] |- |[Файл сведений][api_sample_java] | [Файл сведений](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-command-line-tools"></a>Программы командной строки пакетной службы

Функциональные возможности API-интерфейсов разработки также доступны в программах командной строки. 

* [Командлеты PowerShell для пакетной службы.][batch_ps] Командлеты пакетной службы Azure в модуле [Azure PowerShell](/powershell/azureps-cmdlets-docs) позволяют управлять ресурсами пакетной службы с помощью PowerShell.
* [Azure CLI](/cli/azure/overview). Интерфейс командной строки Azure (Azure CLI) — это кроссплатформенный набор средств, который обеспечивает взаимодействие с разными службами Azure, включая пакетную службу, с помощью команд оболочки.

## <a name="batch-resource-management"></a>Управление ресурсами пакетной службы

API-интерфейсы Azure Resource Manager для пакетной службы предоставляют программный доступ к учетным записям пакетной службы. С помощью этих API можно программно управлять учетными записями, квотами и пакетами приложений.  

| API | Справочник по API | Загрузить | Учебник | Примеры кода |
| --- | --- | --- | --- | --- |
| **REST диспетчера ресурсов пакетной службы** |[docs.microsoft.com][api_rest_mgmt] |Недоступно |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **.NET диспетчера ресурсов пакетной службы** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [Руководство](batch-management-dotnet.md) |[GitHub][api_sample_net] |


## <a name="batch-tools"></a>Средства пакетной службы
Хотя решения не обязательно создавать с помощью пакетной службы, приведенные ниже средства полезны при создании и отладке приложений и служб пакетной службы.

* [Портал Azure.][portal] В колонках пакетной службы на портале Azure можно создавать, отслеживать и удалять пулы, задания и задачи пакетной службы. Во время выполнения заданий можно просмотреть сведения о состоянии этих и других ресурсов и даже скачать файлы из вычислительных узлов в пулах (например, скачать файл `stderr.txt` задачи, завершившейся сбоем, при устранении неполадок). Кроме того, можно скачать файлы удаленного рабочего стола, которые можно использовать для входа на вычислительные узлы.
* [Обозреватель пакетной службы Azure.][batch_explorer] Обозреватель пакетной службы предоставляет такие же функции управления ресурсами пакетной службы, что и портал Azure, но в отдельном клиентском приложении Windows Presentation Foundation. Один из примеров приложений .NET пакетной службы, доступных на [GitHub][github_samples], можно создать с помощью Visual Studio 2015 или более поздней версии. Его можно использовать для просмотра ресурсов и управления ими в вашей учетной записи пакетной службы во время разработки и отладки соответствующих решений. Просматривайте сведения о заданиях, пулах и задачах, скачивайте файлы из вычислительных узлов и удаленно подключайтесь к узлам с помощью файлов удаленного рабочего стола, которые можно скачать с помощью обозревателя пакетной службы.
* [Обозреватель службы хранилища Microsoft Azure.][storage_explorer] Строго говоря, этот обозреватель не является средством пакетной службы Azure, но это полезный инструмент для разработки и отладки соответствующих решений.

## <a name="next-steps"></a>Дальнейшие действия

* Информация, необходимая для тех, кто готовится использовать пакетную службу, доступна в статье [Обзор функций пакетной службы для разработчиков](batch-api-basics.md). Эта статья содержит дополнительные подробные сведения о таких ресурсах пакетной службы, как пулы, узлы, задания, задачи и многие функции API, которые можно использовать при создании приложения пакетной службы.
* Сведения об использовании C# и библиотеки .NET для пакетной службы при обработке простой рабочей нагрузки с помощью стандартного рабочего процесса пакетной службы см. в статье [Начало работы с библиотекой пакетной службы Azure для .NET](batch-dotnet-get-started.md). Это один из основных ресурсов, с которым нужно ознакомиться, приступая к работе с пакетной службой. Есть также версия руководства для [Python](batch-python-tutorial.md).
* Скачайте [примеры кода с GitHub][github_samples], чтобы увидеть, как C# и Python взаимодействуют с пакетной службой для планирования и обработки примеров рабочих нагрузок.
* Список ресурсов для работы с пакетной службой см. на схеме обучения [Пакетная служба][learning_path].


[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/\rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/resourcemanager/azurerm.batch/v2.7.0/azurerm.batch
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

