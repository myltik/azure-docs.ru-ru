---
title: Создание множества моделей из одного эксперимента | Документация Майкрософт
description: Используйте PowerShell для создания нескольких моделей машинного обучения и конечных точек веб-службы с одним алгоритмом, но разными наборами данных для обучения.
services: machine-learning
documentationcenter: ''
author: hning86
ms.author: haining
manager: mwinkle
editor: cgronlun
ms.assetid: 1076b8eb-5a0d-4ac5-8601-8654d9be229f
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.openlocfilehash: 17354891b50138911f36314620f0c826db4b5dac
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833671"
---
# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>Создание множества моделей машинного обучения и конечных точек веб-службы из одного эксперимента с помощью PowerShell
При машинном обучении часто возникает следующая задача: требуется создать множество моделей с одинаковым рабочим процессом обучения и одинаковым алгоритмом. Но для их обучения нужно использовать разные входные наборы данных. В этой статье показано, как это сделать с требуемым масштабом в Студии машинного обучения Azure, используя всего один эксперимент.

Предположим, что вы являетесь владельцем международного бизнеса по предоставлению франшиз на аренду велосипедов. Вы хотите создать модель регрессии, чтобы прогнозировать спрос, основываясь на исторических данных. У вас есть 1000 расположений в разных частях мира, в которых доступна аренда, и вы собрали набор данных по каждому из них. В этот набор входят такие сведения, как даты, время, данные погоды и трафика, характерные для конкретного расположения.

Вы можете обучить модель один раз, используя совокупную версию всех наборов данных для всех расположений. Но у каждого расположения есть уникальные характеристики среды. Поэтому лучше использовать отдельные наборы данных, чтобы обучить модель регрессии для каждого расположения. В этом случае каждая обученная модель будет учитывать характеристики размера, объема, географического положения, населения, удобства среды для велосипедистов и многое другое.

Возможно, это и лучший подход, но вы не захотите создавать 1000 экспериментов по машинному обучению Azure, каждый из которых представляет отдельную точку. Такая задача не только трудоемка, но и малоэффективна, поскольку каждый эксперимент имеет одинаковые компоненты, за исключением набора данных для обучения.

К счастью, для решения этой задачи вы можете применить [API переобучения службы "Машинное обучение Azure"](retrain-models-programmatically.md) и автоматизацию с помощью [PowerShell для службы "Машинное обучение Azure"](powershell-module.md).

> [!NOTE]
> Чтобы ускорить выполнение, мы создадим пример для 10 расположений вместо 1000. Однако в случае с 1000 расположений применяются все те же принципы и процедуры. Но если вы решите обучить модель по 1000 наборов данных, постарайтесь применить параллельное выполнение скриптов PowerShell. Этот вопрос выходит за рамки данной статьи, но примеры многопоточности PowerShell можно найти в Интернете.  
> 
> 

## <a name="set-up-the-training-experiment"></a>настройка обучающего эксперимента
[Обучающий эксперимент](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) для этого примера представлен в [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com). Откройте этот эксперимент в рабочей области [Студии машинного обучения Azure](https://studio.azureml.net) .

> [!NOTE]
> Для работы с этим примером рекомендуется использовать стандартную рабочую область, а не бесплатную. Вы создадите по одной конечной точке для каждого заказчика (всего 10 точек), поэтому вам потребуется стандартная рабочая область. Бесплатная рабочая область поддерживает только 3 конечные точки. Если у вас есть только бесплатная рабочая область, уменьшите число расположений в приведенных ниже скриптах до трех.
> 
> 

Эксперимент использует модуль **Импорт данных** , чтобы импортировать набор данных для обучения *customer001.csv* из учетной записи хранения Azure. Предположим, что вы собрали наборы данных для обучения по всем расположениям аренды велосипедов и сохранили их в одном хранилище BLOB-объектов с именами от *rentalloc001.csv* до *rentalloc10.csv*.

![изображение](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Обратите внимание, что в модуль **Обучение модели** добавлен модуль **Web Service Output** (Выходные данные веб-службы).
Если эксперимент развернут в виде веб-службы, связанная с выходом конечная точка возвращает обученную модель в формате ILEARNER-файла.

Кроме того, вам потребуется определить параметр веб-службы, который содержит URL-адрес для использования в модуле **Импорт данных**. В этом параметре вы сможете указать разные наборы данных для обучения моделей по каждому расположению.
Это можно реализовать и другими способами. Например, можно использовать SQL-запрос с параметром веб-службы, чтобы получать данные из базы данных SQL Azure. Также можно применить модуль **входных данных для веб-службы**, чтобы передать в веб-службу набор данных.

![изображение](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Теперь давайте запустим этот обучающий эксперимент, используя значение по умолчанию *rental001.csv* в качестве набора данных для обучения. Просмотрев выходные данные модуля **Оценка** (для просмотра щелкните выходные данные и выберите **Визуализировать**), вы заметите неплохую производительность *AUC* = 0,91. На этом этапе вы уже можете развернуть веб-службу из нашего обучающего эксперимента.

## <a name="deploy-the-training-and-scoring-web-services"></a>Развертывание обучающей и оценивающей веб-служб
Чтобы развернуть обучающую веб-службу, нажмите кнопку **Set Up Web Service** (Настройка веб-службы) ниже холста эксперимента и выберите **Deploy Web Service** (Развертывание веб-службы). Назовите эту веб-службу Bike Rental Training (Обучение аренды велосипедов).

Теперь разверните оценивающую веб-службу.
Для этого нажмите под холстом кнопку **Set Up Web Service** (Настройка веб-службы) и выберите **Predictive Web Service** (Прогнозная веб-служба). При этом создается оценивающий эксперимент.
Чтобы он работал как веб-служба, осталось внести лишь небольшие изменения. Удалите из входных данных столбец меток "cnt" и сократите выходные данных, оставив только идентификатор экземпляра и прогнозируемое значение.

Чтобы не выполнять это самостоятельно, откройте уже подготовленный [прогнозный эксперимент](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) из коллекции.

Чтобы развернуть веб-службы, запустите прогнозный эксперимент, а затем щелкните **Deploy Web Service** (Развертывание веб-службы) внизу холста. Назовите оценивающую веб-службу Bike Rental Scoring (Оценка аренды велосипедов).

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Создание 10 идентичных конечных точек веб-службы с помощью PowerShell
Эта веб-служба предоставляется с конечной точкой по умолчанию. Но вам не нужна конечная точка по умолчанию, так как ее нельзя обновить. Вам следует создать 10 дополнительных конечных точек — по одной для каждого расположения. Для этого мы воспользуемся PowerShell.

Сначала настройте среду PowerShell:

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

Итак, вы создали 10 конечных точек, и все они содержат одну и ту же модель, обученную по набору данных *customer001.csv*. Их можно просмотреть на портале Azure.

![изображение](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Обновление конечных точек для использования отдельных наборов данных для обучения с помощью PowerShell
Следующим шагом является обновление конечных точек с использованием моделей, обученных по отдельным данным каждого из клиентов. Но сначала вам следует создать эти модели из веб-службы **Bike Rental Training**. Вернемся к веб-службе **Bike Rental Training** . Вызовите ее конечную точку BES 10 раз с 10 разными наборами данных для обучения, чтобы создать 10 разных моделей. В этом вам поможет командлет PowerShell **InovkeAmlWebServiceBESEndpoint**.

Также для `$configContent` нужно предоставить учетные данные для доступа к учетной записи хранения BLOB-объектов. Они передаются в полях `AccountName`, `AccountKey` и `RelativeLocation`. В поле `AccountName` можно указать любое из имен учетных записей в том виде, в котором они представлены на **портале Azure** (на вкладке *Хранилище*). Выберите учетную запись хранения. Чтобы найти ее `AccountKey`, нажмите кнопку **Управление ключами доступа** и скопируйте значение поля *Первичный ключ доступа*. `RelativeLocation` — это путь к хранилищу, в котором будет храниться новая модель. Например, путь `hai/retrain/bike_rental/` в скрипте ниже указывает на контейнер с именем `hai`, а `/retrain/bike_rental/` — это вложенные папки. Сейчас невозможно создать вложенные папки с помощью пользовательского интерфейса портала. Но это можно сделать с помощью [нескольких обозревателей службы хранилища Azure](../../storage/common/storage-explorers.md). Рекомендуем создать для хранения обученных моделей (ILEARNER-файлы) контейнер в хранилище. Для этого в нижней части страницы хранилища нажмите кнопку **Добавить** и введите имя `retrain` для контейнера. Таким образом, в следующем скрипте нужно изменить лишь `AccountName`, `AccountKey` и `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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

Как показано выше, вместо создания 10 разных JSON-файлов, требующихся для настройки заданий BES, можно динамически создать строку настройки. Эта строка передается в параметре *jobConfigString* для командлета **InvokeAmlWebServceBESEndpoint**. Ее даже не нужно сохранять на диске.

Если все пройдет правильно, через некоторое время в вашей учетной записи хранения Azure появятся 10 ILEARNER-файлов: от *model001.ilearner* до *model010.ilearner*. Теперь все готово к передаче этих моделей в 10 конечных точек оценивающей веб-службы с помощью командлета PowerShell **Patch-AmlWebServiceEndpoint**. Не забывайте, что вы можете изменить только конечные точки, созданные ранее программными средствами, но не конечную точку по умолчанию.

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

Процесс должен выполняться довольно быстро. Когда он завершится, вы получите 10 конечных точек прогнозной веб-службы. Каждая из них будет содержать модель, обученную по уникальному набору данных для определенного расположения аренды. Все эти модели создаются в одном эксперименте обучения. Чтобы проверить работу системы, обратитесь к этим конечным точкам с помощью командлета **InvokeAmlWebServiceRRSEndpoint**, предоставив одинаковые входные данные. Вы должны получить разные результаты прогнозирования, поскольку модели обучены по разным наборам данных.

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
