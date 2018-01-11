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
ms.openlocfilehash: 3386e3bab7a92b080276c4f03f0b006bd5f68e98
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>Создание множества моделей машинного обучения и конечных точек веб-службы из одного эксперимента с помощью PowerShell
С машинным обучением часто возникает следующая проблема: требуется создать множество моделей, имеющих одинаковый рабочий процесс обучения и одинаковый алгоритм, но использующих разные наборы данных для обучения на входе. В этой статье показано, как это сделать с требуемым масштабом в Студии машинного обучения Azure, используя всего один эксперимент.

Предположим, что вы являетесь владельцем международного бизнеса по предоставлению франшиз на аренду велосипедов. Вы хотите создать модель регрессии, чтобы прогнозировать спрос, основываясь на исторических данных. У вас есть 1000 аренды расположениях по всему миру. Собранные набора данных для каждого расположение, которое включает в себя важные компоненты, например даты, времени, погоды, а также трафик, характерные для каждого расположения.

Вы можете обучить модель один раз, используя совокупную версию всех наборов данных для всех расположений. Однако, так как каждая точка работает в уникальной среде, модель регрессии лучше обучить, используя отдельные наборы данных для каждого расположения. Таким образом, каждый обученной модели может учитывать размеры другое хранилище, тома, geography, заполнение, понятных велосипеда трафика среды и многое другое.

Возможно, это и лучший подход, но вы не захотите создавать 1000 экспериментов по машинному обучению Azure, каждый из которых представляет отдельную точку. Помимо возможности использования задачу затруднительным, кроме того, он кажется неэффективным, поскольку каждый эксперимента будет иметь те же компоненты, за исключением обучающий набор данных.

К счастью, это можно сделать с помощью [переподготовки API машинного обучения Azure](retrain-models-programmatically.md) и автоматизации задач с [Azure Machine Learning PowerShell](powershell-module.md).

> [!NOTE]
> Чтобы ваш образца, уменьшите количество расположений от 1000 до 10. Однако в случае с 1000 расположений применяются все те же принципы и процедуры. Единственное отличие заключается в том, что при обучении по 1000 наборам данных следует рассмотреть возможность параллельного выполнения приведенных ниже сценариев PowerShell. Этот вопрос выходит за рамки данной статьи, но примеры многопоточности PowerShell можно найти в Интернете.  
> 
> 

## <a name="set-up-the-training-experiment"></a>настройка обучающего эксперимента
В примере [эксперимента обучения](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) , находящийся в [коллекции аналитики Cortana](http://gallery.cortanaintelligence.com). Откройте этот эксперимент в рабочей области [Студии машинного обучения Azure](https://studio.azureml.net) .

> [!NOTE]
> Для работы с этим примером рекомендуется использовать стандартную рабочую область, а не бесплатную. Создайте одну конечную точку для каждого клиента - всего 10 конечные точки — и поскольку бесплатную рабочую область ограничена 3 конечные точки, требуется Стандартная рабочая область. Если имеется только бесплатную рабочую область, достаточно измените скрипты, чтобы разрешить только трех местоположений.
> 
> 

Эксперимент использует модуль **Импорт данных** , чтобы импортировать набор данных для обучения *customer001.csv* из учетной записи хранения Azure. Предположим, что вы сбора обучающих наборах данных из всех расположений прокат велосипедов и их хранятся в том же месте хранения больших двоичных объектов с именами файлов, от *rentalloc001.csv* для *rentalloc10.csv*.

![изображение](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Обратите внимание, что в модуль **Обучение модели** добавлен модуль **Web Service Output** (Выходные данные веб-службы).
При этом эксперименте развертывается как веб-службы, конечной точки, связанный с что выходных данных возвращается в формате файла .ilearner обученной модели.

Также Обратите внимание, что настройка параметра веб-службы для URL-адрес, **импорта данных** использует модуль. Это позволяет использовать параметр для указания отдельных обучающих наборах данных для обучения модели для каждого расположения.
Существуют другие способы, это может сделано. Можно использовать SQL-запроса с помощью параметра веб-службы для получения данных из базы данных SQL Azure или используйте **входных данных для службы Web** модуля, который нужно передать в наборе данных для веб-службы.

![изображение](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Теперь давайте запустим этот обучающий эксперимент, используя значение по умолчанию *rental001.csv* в качестве набора данных для обучения. Если просмотреть выходные данные **Evaluate** модуля (щелкните выходных данных и выберите **визуализировать**), видно, вы сможете довольно производительность *AUC* = 0.91. На этом этапе вы будете готовы развернуть веб-службу из этого эксперимента обучения.

## <a name="deploy-the-training-and-scoring-web-services"></a>Развертывание обучающей и оценивающей веб-служб
Чтобы развернуть обучающую веб-службу, нажмите кнопку **Set Up Web Service** (Настройка веб-службы) ниже холста эксперимента и выберите **Deploy Web Service** (Развертывание веб-службы). Назовите эту веб-службу Bike Rental Training (Обучение аренды велосипедов).

Теперь необходимо выполнить развертывание оценки веб-службы.
Чтобы сделать это, нажмите кнопку **настройки веб-службы** ниже на холсте и выберите команду **прогнозной веб-службы**. При этом создается оценивающий эксперимент.
Необходимо внести небольшие незначительные изменения для работы веб-службы. Удалите столбец метки «cnt» из входных данных и ограничения выходных данных идентификатор экземпляра и соответствующие прогнозируемое значение.

Чтобы не выполнять все это самостоятельно, можно просто открыть уже подготовленный [прогнозный эксперимент](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) из коллекции.

Чтобы развернуть веб-службы, запустите прогнозный эксперимент, а затем щелкните **Deploy Web Service** (Развертывание веб-службы) внизу холста. Назовите оценивающую веб-службу Bike Rental Scoring (Оценка аренды велосипедов).

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Создание 10 идентичных конечных точек веб-службы с помощью PowerShell
Эта веб-служба предоставляется с конечной точкой по умолчанию. Однако вы не интересует, как конечная точка по умолчанию, так как не удается обновить. Вам нужно заключается в создании 10 дополнительных конечных точек, одно для каждого расположения. Это можно сделать с помощью PowerShell.

Во-первых Настройка среды PowerShell.

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

Теперь вы создали 10 конечные точки и все они содержат одинаковый обученной модели обучения на *customer001.csv*. Их можно просмотреть на портале Azure.

![изображение](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Обновление конечных точек для использования отдельных наборов данных для обучения с помощью PowerShell
Следующим шагом является обновление конечных точек с использованием моделей, обученных по отдельным данным каждого из клиентов. Но сначала необходимо создать эти модели из **обучения прокат велосипедов** веб-службы. Вернемся к веб-службе **Bike Rental Training** . необходимо вызвать ее конечная точка BES 10 раз с 10 разных обучающих наборах данных для создания 10 разных моделей. Для этого мы используем командлет **InovkeAmlWebServiceBESEndpoint** PowerShell.

Также необходимо указать учетные данные для учетной записи хранения BLOB-объектов в `$configContent`, то есть в полях `AccountName`, `AccountKey` и `RelativeLocation`. `AccountName` Может быть одно из имен учетной записи, как показано в **портал Azure** (*хранения* вкладке). Выберите учетную запись хранения. Чтобы найти ее `AccountKey`, нажмите кнопку **Управление ключами доступа** и скопируйте значение поля *Первичный ключ доступа*. `RelativeLocation` — это путь к хранилищу, в котором будет храниться новая модель. Например, путь `hai/retrain/bike_rental/` в сценарии ниже указывает на контейнер с именем `hai`, а `/retrain/bike_rental/` — это вложенные папки. Сейчас невозможно создать вложенные папки с помощью пользовательского интерфейса портала. Но это можно сделать с помощью [нескольких обозревателей службы хранилища Azure](../../storage/common/storage-explorers.md). Рекомендуется создать контейнер в хранилище для хранения новых обученных моделей (ILEARNER-файлы) следующим образом. На странице хранилища нажмите кнопку **Добавить** внизу и назовите контейнер `retrain`. Таким образом, все необходимые изменения в сценарии ниже относятся к `AccountName`, `AccountKey` и `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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

Как показано выше, вместо создания 10 разных BES задания json файлы конфигурации, динамически создания строка настройки вместо и добавить его к *jobConfigString* параметр  **InvokeAmlWebServceBESEndpoint** командлета, так как на самом деле не нужно сохранять копию на диске.

Если все работает правильно, через некоторое время в вашей учетной записи хранения Azure появятся 10 ILEARNER-файлов: от *model001.ilearner* до *model010.ilearner*. Теперь все готово для того, чтобы обновить 10 конечных точек оценивающей веб-службы с помощью этих моделей, используя командлет PowerShell **Patch-AmlWebServiceEndpoint** . Помните, еще раз, можно исправить только конечные точки не по умолчанию, созданный программным путем.

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
