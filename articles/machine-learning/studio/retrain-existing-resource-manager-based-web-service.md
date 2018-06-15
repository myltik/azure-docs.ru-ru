---
title: Переобучение имеющейся веб-службы прогнозной аналитики | Документация Майкрософт
description: Узнайте, как переобучить модель и обновить веб-службу так, чтобы она использовала заново обученную модель в службе машинного обучения Azure.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.openlocfilehash: d399c8c3a47d374549d7ea7815567d7b879b49c8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835306"
---
# <a name="retrain-an-existing-predictive-web-service"></a>Переобучение прогнозной веб-службы
В этом документе описан процесс переобучения для следующего сценария:

* Есть обучающий и прогнозный эксперименты, развернутые в качестве веб-службы, готовой к эксплуатации.
* Есть новые данные, которые нужно использовать в прогнозной веб-службе, чтобы оценить их.

> [!NOTE]
> Для развертывания новой веб-службы у вас должен быть достаточный уровень разрешений в подписке, в которую выполняется развертывание веб-службы. Дополнительные сведения см. в статье [Управление веб-службой с помощью портала веб-служб машинного обучения Azure](manage-new-webservice.md).

Вам необходимо выполнить шаги ниже, начав с существующих веб-службы и экспериментов:

1. Обновление модели.
   1. Измените обучающий эксперимент, разрешив использование входных и выходных данных веб-службы.
   2. Разверните обучающий эксперимент в качестве переобучаемой веб-службы.
   3. Используйте службу выполнения пакетов из обучающего эксперимента, чтобы переобучить модель.
2. Используйте командлеты PowerShell Машинного обучения Azure, чтобы обновить прогнозный эксперимент.
   1. Войти в учетную запись Azure Resource Manager.
   2. Получите определение веб-службы.
   3. Экспортируйте определение веб-службы в формате JSON.
   4. Обновить ссылку на большой двоичный объект ilearner в JSON.
   5. Импортируйте JSON-файл в определение веб-службы.
   6. Обновите веб-службу, используя новое определение веб-службы.

## <a name="deploy-the-training-experiment"></a>Развертывание обучающего эксперимента
Чтобы развернуть обучающий эксперимент в качестве переобучаемой веб-службы, необходимо добавить ее входные и выходные данные в модель. Подключившись к модулю *Web Service Output* (Выходные данные веб-службы) в модуле *[Обучение модели][train-model]*, включите обучающий эксперимент, чтобы создать обученную модель, которую можно использовать в прогнозном эксперименте. Если у вас есть модуль *Evaluate Model* (Модель оценки), вы также можете присоединить выходные данные веб-службы, чтобы получить результаты оценки в качестве выходных данных.

Чтобы обновить обучающий эксперимент:

1. Подключите модуль *Web Service Input* (Входные данные веб-службы) к порту ввода данных (например, модуля *Clean Missing Data* (Очистка недостающих данных)). Вы, естественно, захотите убедиться, что входные данные будут обработаны так же, как исходный набор обучающих данных.
2. Подключите модуль *Web Service Output* (Выходные данные веб-службы) к порту вывода модуля *Train Model* (Обучение модели).
3. При наличии модуля *Evaluate Model* (Оценка модели) и необходимости передать результаты оценки подключите модуль *Web Service Output* (Выходные данные веб-службы) к модулю *Evaluate Model* (Оценка модели).

Запустите эксперимент.

Затем необходимо развернуть обучающий эксперимент в качестве веб-службы, которая создает обученную модель и результаты ее оценки.

В нижней части холста эксперимента щелкните **Set Up Web Service** (Настроить веб-службу), а затем **Deploy Web Service [New]** (Развернуть веб-службу [новую]). Портал веб-служб Машинного обучения Azure откроется на странице **Deploy Web service** (Развертывание веб-службы). Введите имя веб-службы, выберите план платежей, а затем щелкните **Развернуть**. Чтобы создать обученную модель, можно использовать только метод пакетного выполнения.

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Переобучение модели с использованием новых данных с помощью службы выполнения пакетов
В этом примере для создания приложения переобучения используется код на языке C#. Кроме того, для этого можно использовать образцы кода на языке R или Python.

Чтобы вызвать интерфейсы API переобучения:

1. Создайте консольное приложение C# в Visual Studio (**Создать** > **Проект** > **Visual C#** > **Классический рабочий стол Windows** > **Консольное приложение (.NET Framework**).
2. Войдите на портал веб-служб Машинного обучения.
3. Щелкните веб-службу, с которой работаете.
4. Щелкните **Consume**(Использование).
5. В нижней части страницы **Consume** (Использование) в разделе **Sample Code** (Пример кода) щелкните **Batch** (Пакет).
6. Скопируйте пример кода на C# для пакетного выполнения и вставьте его в файл Program.cs. Убедитесь, что пространство имен не изменено.

Добавьте пакет NuGet Microsoft.AspNet.WebApi.Client, как указано в комментариях. Чтобы добавить ссылку на файл Microsoft.WindowsAzure.Storage.dll, может потребоваться сначала установить [клиентскую библиотеку для служб хранилища Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

На следующем снимке экрана показана станица **Consume** (Использование) на портале веб-служб Машинного обучения Azure.

![Страница Consume (Использование)][1]

### <a name="update-the-apikey-declaration"></a>Обновление объявления apiKey
Найдите объявление **apiKey**:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

В разделе **Basic consumption info** (Основные сведения об использовании) на странице **Consume** (Использование) найдите первичный ключ и скопируйте его в объявление **apiKey**.

### <a name="update-the-azure-storage-information"></a>Обновление сведений о службе хранилища Azure
Пример кода службы выполнения пакетов отправляет файл с локального диска (например, C:\temp\CensusIpnput.csv) в службу хранилища Azure, обрабатывает его и записывает результаты обратно в службу хранилища Azure.

После запуска эксперимента итоговый рабочий процесс должен быть аналогичен следующему:

![Итоговый рабочий процесс после запуска][4]

1. Войдите на портал Azure.
2. В столбце слева щелкните **More services** (Дополнительные службы), найдите параметр **Учетные записи хранения** и выберите его.
3. Выберите в списке учетную запись хранения, которая будет использоваться для хранения переобученной модели.
4. В левой области навигации щелкните **Ключи доступа**.
5. Скопируйте и сохраните **первичный ключ доступа**.
6. В левой области навигации щелкните **Контейнеры**.
7. Выберите существующий контейнер или создайте другой, а затем сохраните его имя.

Найдите объявления *StorageAccountName*, *StorageAccountKey* и *StorageContainerName*, а затем обновите их, используя значения с портала.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Кроме того, необходимо обеспечить доступность файла входных данных в расположении, указанном в коде.

### <a name="specify-the-output-location"></a>Указание расположения выходных данных
Указывая расположение выходных данных для полезных данных запроса, измените расширение файла, заданное в *RelativeLocation*, на `ilearner`. Как в этом примере:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Ниже приведен пример выходных данных переобучения:

![Выходные данные переобучения][6]

## <a name="evaluate-the-retraining-results"></a>Оценка результатов переобучения
При выполнении приложения выходные данные содержат URL-адрес и подписанные URL-адреса, необходимые для доступа к результатам оценки.

Чтобы увидеть результаты работы переобученной модели, введите в адресной строке браузера полный URL-адрес, составленный из значений параметров *BaseLocation*, *RelativeLocaiton* и *SasBlobToken*, содержащихся в выходных данных *output2* (как показано на изображении выше).

Проанализируйте результаты и определите, достаточно ли хорошо работает только что обученная модель, чтобы заменить имеющуюся.

В полученных выходных данных скопируйте значения параметров *BaseLocation*, *RelativeLocation* и *SasBlobToken*.

## <a name="retrain-the-web-service"></a>Переобучение веб-службы
При переобучении новой веб-службы следует обновить определение прогнозной веб-службы, чтобы оно ссылалось на новую обученную модель. Определение веб-службы — это внутреннее представление обученной модели веб-службы, которое нельзя изменить напрямую. Убедитесь, что извлекается определение веб-службы для прогнозного, а не обучающего эксперимента.

## <a name="sign-in-to-azure-resource-manager"></a>Вход в Azure Resource Manager
Сначала войдите в учетную запись Azure в среде PowerShell, используя командлет [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount).

## <a name="get-the-web-service-definition-object"></a>Получение объекта определения веб-службы
Теперь получите объект определения веб-службы, вызвав командлет [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx).

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Для определения имени группы ресурсов существующей веб-службы выполните командлет Get-AzureRmMlWebService без каких-либо параметров, чтобы отобразились веб-службы в подписке. Найдите необходимую веб-службу и посмотрите ее идентификатор. Имя группы ресурсов — это четвертый элемент в идентификаторе, который следует сразу за элементом *resourceGroups* . В следующем примере имя группы ресурсов — Default-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Кроме того, чтобы определить имя группы ресурсов существующей веб-службы, можно войти на портал веб-служб Машинного обучения Azure. Затем выбрать веб-службу. Имя группы ресурсов — это пятый элемент в URL-адресе веб-службы, который следует сразу за элементом *resourceGroups* . В следующем примере имя группы ресурсов — Default-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Экспорт объекта определения веб-службы в формате JSON
Чтобы изменить определение обученной модели для использования новой соответствующей модели, необходимо сначала выполнить командлет [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx). Это позволит экспортировать определение в JSON-файл.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Обновление ссылки на большой двоичный объект ilearner
В ресурсах-контейнерах найдите элемент [trained model], обновите значение *uri* в узле *locationInfo*, заменив его универсальным кодом ресурса (URI) BLOB-объекта ilearner. URI формируется в результате объединения параметров *BaseLocation* и *RelativeLocation* из выходных данных вызова переобучения BES.

     "asset3": {
        "name": "Retrain Sample [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition-object"></a>Импорт JSON-файла в объект определения веб-службы
Необходимо использовать командлет [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx), чтобы преобразовать измененный JSON-файл обратно в объект определения веб-службы, который можно использовать для обновления прогнозного эксперимента.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Обновление веб-службы
Наконец, используйте командлет [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx), чтобы обновить прогнозный эксперимент.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
