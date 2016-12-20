---
title: "Переобучение новой веб-службы с помощью командлетов управления PowerShell для машинного обучения | Документация Майкрософт"
description: "Узнайте, как с помощью командлетов управления PowerShell для машинного обучения осуществить программное переобучение модели и обновить веб-службу так, чтобы она использовала заново обученную модель в машинном обучении Azure."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondlaghaeian
editor: 
ms.assetid: 3953a398-6174-4d2d-8bbd-e55cf1639415
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f6747a80773cd76b0821edfd2ebcd6af84bed233


---
# <a name="retrain-a-new-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Переобучение новой веб-службы с помощью командлетов управления PowerShell для машинного обучения
При переобучении новой веб-службы следует обновить определение прогнозной веб-службы, чтобы оно ссылалось на новую обученную модель.  

## <a name="prerequisites"></a>Предварительные требования
Вы уже настроили обучающий и прогнозный эксперименты, как показано в статье [Программное переобучение моделей машинного обучения](machine-learning-retrain-models-programmatically.md). 

> [!IMPORTANT]
> Прогнозный эксперимент развернут в виде веб-службы машинного обучения на основе Azure Resource Manager (новой веб-службы). 
> 
> 

Дополнительную информацию о развертывании веб-служб см. в статье [Развертывание веб-службы машинного обучения Azure](machine-learning-publish-a-machine-learning-web-service.md).

Для этого процесса требуется установить командлеты Машинного обучения Azure. Сведения по установке командлетов службы машинного обучения Azure см. по [этой ссылке](https://msdn.microsoft.com/library/azure/mt767952.aspx) на сайте MSDN.

Вы скопировали из выходных данных переобучения следующие сведения:

* BaseLocation
* RelativeLocation

Теперь необходимо выполнить следующие шаги:

1. Войти в учетную запись Azure Resource Manager.
2. Получить определение веб-службы.
3. Экспортировать определение веб-службы в формате JSON.
4. Обновить ссылку на большой двоичный объект ilearner в JSON.
5. Импортировать JSON в определение веб-службы.
6. Обновить веб-службу с помощью нового определения веб-службы.

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Вход в учетную запись Azure Resource Manager
Сначала войдите в учетную запись Azure в среде PowerShell с помощью командлета [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition"></a>Получить определение веб-службы.
Затем получите сведения о веб-службе, вызвав командлет [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) . Определение веб-службы — это внутреннее представление обученной модели веб-службы, которое нельзя изменить напрямую. Убедитесь, что извлекается определение веб-службы для прогнозного, а не обучающего эксперимента.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Для определения имени группы ресурсов существующей веб-службы выполните командлет Get-AzureRmMlWebService без каких-либо параметров, чтобы отобразились веб-службы в подписке. Найдите необходимую веб-службу и посмотрите ее идентификатор. Имя группы ресурсов — это четвертый элемент в идентификаторе, который следует сразу за элементом *resourceGroups* . В следующем примере имя группы ресурсов — Default-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Кроме того, для определения имени группы ресурсов существующей веб-службы можно войти на портал веб-служб Машинного обучения Microsoft Azure. Затем выбрать веб-службу. Имя группы ресурсов — это пятый элемент в URL-адресе веб-службы, который следует сразу за элементом *resourceGroups* . В следующем примере имя группы ресурсов — Default-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Экспортировать определение веб-службы в формате JSON.
Чтобы изменить определение обученной модели для использования новой обученной модели, необходимо сначала воспользоваться командлетом [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) и экспортировать его в файл в формате JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Обновить ссылку на большой двоичный объект ilearner в JSON.
В ресурсах-контейнерах найдите элемент [trained model], обновите значение *uri* в узле *locationInfo*, заменив его универсальным кодом ресурса (URI) BLOB-объекта ilearner. URI формируется в результате объединения параметров *BaseLocation* и *RelativeLocation* из выходных данных вызова переобучения BES.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition"></a>Импортировать JSON в определение веб-службы.
Воспользуйтесь командлетом [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) для преобразования измененного JSON-файла обратно в определение веб-службы, которое можно использовать для обновления прогнозного эксперимента.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Обновить веб-службу с помощью нового определения веб-службы.
Наконец, воспользуйтесь командлетом [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) для обновления прогнозного эксперимента.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Сводка
С помощью командлетов управления PowerShell Машинного обучения можно обновить обученную модель прогнозной веб-службы, что позволяет выполнять следующие сценарии:

* Периодическое переобучение модели с использованием новых данных.
* Распределение модели клиентам, чтобы они могли переобучить ее с использованием собственных данных.




<!--HONumber=Nov16_HO3-->


