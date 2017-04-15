---
title: "Создание множества моделей из одного эксперимента | Документация Майкрософт"
description: "Используйте PowerShell для создания нескольких моделей машинного обучения и конечных точек веб-службы с одним алгоритмом, но разными наборами данных для обучения."
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 1076b8eb-5a0d-4ac5-8601-8654d9be229f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: garye;haining
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 069e662ce70f1ec78d796c29d8b5331fc8a5a3e7
ms.lasthandoff: 11/17/2016


---
# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>Создание множества моделей машинного обучения и конечных точек веб-службы из одного эксперимента с помощью PowerShell
С машинным обучением часто возникает следующая проблема: требуется создать множество моделей, имеющих одинаковый рабочий процесс обучения и одинаковый алгоритм, но использующих разные наборы данных для обучения на входе. В этой статье показано, как это сделать с требуемым масштабом в Студии машинного обучения Azure, используя всего один эксперимент.

Предположим, что вы являетесь владельцем международного бизнеса по предоставлению франшиз на аренду велосипедов. Вы хотите создать модель регрессии, чтобы прогнозировать спрос, основываясь на исторических данных. Вы управляете 1000 пунктов аренды точек по всему миру и собрали для каждой из них набор данных, включающий важные показатели по каждому расположению, например даты, время, погоду и дорожное движение.

Вы можете обучить модель один раз, используя совокупную версию всех наборов данных для всех расположений. Однако, так как каждая точка работает в уникальной среде, модель регрессии лучше обучить, используя отдельные наборы данных для каждого расположения. При этом каждая обученная модель может учитывать различные показатели размера, объема, географического положения, населения, наличия условий для езды на велосипеде *и т. д.*у точек.

Возможно, это и лучший подход, но вы не захотите создавать 1000 экспериментов по машинному обучению Azure, каждый из которых представляет отдельную точку. Кроме перегруженности, такой подход довольно неэффективен, так как каждый эксперимент будет иметь одинаковые компоненты, за исключением набора данных для обучения.

К счастью, этой цели можно достичь, используя [API переобучения Машинного обучения Azure](machine-learning-retrain-models-programmatically.md) и автоматизировав задачу с помощью [PowerShell для Машинного обучения Microsoft Azure](machine-learning-powershell-module.md).

> [!NOTE]
> Чтобы ускорить выполнение , мы уменьшаем количество расположений с 1000 до 10. Однако в случае с 1000 расположений применяются все те же принципы и процедуры. Единственное отличие заключается в том, что при обучении по 1000 наборам данных следует рассмотреть возможность параллельного выполнения приведенных ниже сценариев PowerShell. Этот вопрос выходит за рамки данной статьи, но примеры многопоточности PowerShell можно найти в Интернете.  
> 
> 

## <a name="set-up-the-training-experiment"></a>настройка обучающего эксперимента
Мы будем использовать уже готовый пример [обучающего эксперимента](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) из [коллекции Cortana Intelligence](http://gallery.cortanaintelligence.com). Откройте этот эксперимент в рабочей области [Студии машинного обучения Azure](https://studio.azureml.net) .

> [!NOTE]
> Для работы с этим примером рекомендуется использовать стандартную рабочую область, а не бесплатную. Мы создадим одну конечную точку для каждого заказчика общим числом 10, и для этого потребуется стандартная рабочая область, так как бесплатная рабочая область ограничена 3 конечными точками. Если у вас имеется только бесплатная рабочая область, просто измените приведенные ниже сценарии, чтобы разрешить только 3 расположения.
> 
> 

Эксперимент использует модуль **Импорт данных** , чтобы импортировать набор данных для обучения *customer001.csv* из учетной записи хранения Azure. Предположим, что мы собрали наборы данных для обучения из всех пунктов аренды велосипедов и сохранили их в одном хранилище BLOB-объектов с именами от *rentalloc001.csv* до *rentalloc10.csv*.

![изображение](./media/machine-learning-create-models-and-endpoints-with-powershell/reader-module.png)

Обратите внимание, что в модуль **Обучение модели** добавлен модуль **Web Service Output** (Выходные данные веб-службы).
При развертывании эксперимента в виде веб-службы конечная точка, связанная с этими выходными данными, будет возвращать обученную модель в формате ILEARNER-файла.

Обратите внимание и на то, что мы устанавливаем для параметра веб-службы URL-адрес, используемый модулем **Импорт данных** . Это позволяет использовать такой параметр, чтобы указать отдельные наборы данных в целях обучения модели для каждого расположения.
Есть и другие способы выполнения этой задачи, например с помощью SQL-запроса с параметром веб-службы для получения данных из базы данных SQL Azure или простое использование модуля **Web Service Input** (Входные данные веб-службы) для передачи набора данных в веб-службу.

![изображение](./media/machine-learning-create-models-and-endpoints-with-powershell/web-service-output.png)

Теперь давайте запустим этот обучающий эксперимент, используя значение по умолчанию *rental001.csv* в качестве набора данных для обучения. Если просмотреть выходные данные модуля **Оценка** (щелкните выходные данные и выберите **Визуализировать**), вы увидите, что мы получаем неплохую производительность с *AUC* = 0,91. На этом этапе мы готовы развернуть веб-службу из этого обучающего эксперимента.

## <a name="deploy-the-training-and-scoring-web-services"></a>Развертывание обучающей и оценивающей веб-служб
Чтобы развернуть обучающую веб-службу, нажмите кнопку **Set Up Web Service** (Настройка веб-службы) ниже холста эксперимента и выберите **Deploy Web Service** (Развертывание веб-службы). Назовите эту веб-службу Bike Rental Training (Обучение аренды велосипедов).

Теперь нам требуется развернуть оценивающую веб-службу.
Для этого можно нажать кнопку **Set Up Web Service** (Настройка веб-службы) ниже холста и выбрать **Predictive Web Service** (Прогнозная веб-служба). При этом создается оценивающий эксперимент.
Необходимо внести несколько небольших корректировок, чтобы он заработал как веб-служба, например удалить столбец метки cnt из входных данных и ограничить выходные данные лишь идентификатором экземпляра и соответствующим прогнозируемым значением.

Чтобы не выполнять все это самостоятельно, можно просто открыть уже подготовленный [прогнозный эксперимент](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) из коллекции.

Чтобы развернуть веб-службы, запустите прогнозный эксперимент, а затем щелкните **Deploy Web Service** (Развертывание веб-службы) внизу холста. Назовите оценивающую веб-службу Bike Rental Scoring (Оценка аренды велосипедов).

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Создание 10 идентичных конечных точек веб-службы с помощью PowerShell
Эта веб-служба предоставляется с конечной точкой по умолчанию. Однако нас эта конечная точка не интересует, так как ее нельзя обновить. Нам нужно создать 10 дополнительных конечных точек — по одной для каждого расположения. Для этого мы воспользуемся PowerShell.

Сначала настроим среду PowerShell:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Затем выполним следующую команду PowerShell:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Итак, мы создали 10 конечных точек, содержащих одинаковую модель, обученную по *customer001.csv*. Их можно просмотреть на портале управления Azure.

![изображение](./media/machine-learning-create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Обновление конечных точек для использования отдельных наборов данных для обучения с помощью PowerShell
Следующим шагом является обновление конечных точек с использованием моделей, обученных по отдельным данным каждого из клиентов. Но сначала необходимо создать эти модели из веб-службы **Bike Rental Training** . Вернемся к веб-службе **Bike Rental Training** . Необходимо вызвать ее конечную точку BES 10 раз с 10 разными наборами данных для обучения, чтобы создать 10 разных моделей. Для этого мы используем командлет **InovkeAmlWebServiceBESEndpoint** PowerShell.

Также необходимо указать учетные данные для учетной записи хранения BLOB-объектов в `$configContent`, то есть в полях `AccountName`, `AccountKey` и `RelativeLocation`. В поле `AccountName` можно указать одно из имен учетной записи, как показано на **классическом портале управления Azure** (вкладка *Хранилище*). Выберите учетную запись хранения. Чтобы найти ее `AccountKey`, нажмите кнопку **Управление ключами доступа** и скопируйте значение поля *Первичный ключ доступа*. `RelativeLocation` — это путь к хранилищу, в котором будет храниться новая модель. Например, путь `hai/retrain/bike_rental/` в сценарии ниже указывает на контейнер с именем `hai`, а `/retrain/bike_rental/` — это вложенные папки. Сейчас невозможно создать вложенные папки с помощью пользовательского интерфейса портала. Но это можно сделать с помощью [нескольких обозревателей службы хранилища Azure](../storage/storage-explorers.md). Рекомендуется создать контейнер в хранилище для хранения новых обученных моделей (ILEARNER-файлы) следующим образом. На странице хранилища нажмите кнопку **Добавить** внизу и назовите контейнер `retrain`. Таким образом, все необходимые изменения в сценарии ниже относятся к `AccountName`, `AccountKey` и `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

> [!NOTE]
> Конечная точка BES является единственным поддерживаемым режимом для этой операции. RRS использовать для создания обученных моделей нельзя.
> 
> 

Как можно заметить, вместо создания 10 разных JSON-файлов конфигурации заданий BES мы динамически создали строку конфигурации и передали ее в параметр *jobConfigString* командлета **InvokeAmlWebServceBESEndpoint** , так как в этом случае нет необходимости сохранять копию на диске.

Если все работает правильно, через некоторое время в вашей учетной записи хранения Azure появятся 10 ILEARNER-файлов: от *model001.ilearner* до *model010.ilearner*. Теперь все готово для того, чтобы обновить 10 конечных точек оценивающей веб-службы с помощью этих моделей, используя командлет PowerShell **Patch-AmlWebServiceEndpoint** . Не забывайте, что изменить можно только нестандартные конечные точки, созданные ранее программным образом.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

Процесс должен выполняться довольно быстро. По завершении выполнения создается 10 конечных точек прогнозной веб-службы, каждая из которых содержит уникальную модель, обученную по набору данных для конкретного пункта аренды, и все это — из одного обучающего эксперимента. Чтобы проверить это, попробуйте вызвать эти конечные точки с помощью командлета **InvokeAmlWebServiceRRSEndpoint**, предоставляя им одинаковые входные данные. Результаты прогноза должны отличаться, так как модели обучались с использованием разных наборов данных.

## <a name="full-powershell-script"></a>Полный сценарий PowerShell
Ниже приведен полный исходный код.

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

