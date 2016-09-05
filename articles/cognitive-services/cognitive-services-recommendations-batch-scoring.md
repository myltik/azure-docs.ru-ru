
<properties
	pageTitle="Получение пакетов рекомендаций: API рекомендаций для машинного обучения | Microsoft Azure"
	description="Рекомендации для машинного обучения Azure. Получение пакетов рекомендаций"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="luisca"/>

# Получение пакетов рекомендаций

>[AZURE.NOTE] Процесс получения пакетов рекомендаций сложнее, чем получение рекомендаций по одной. Здесь вы можете получить информацию о том, как получить рекомендацию для одного запроса:

> [Item-to-Item recommendations](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4) (Рекомендации "элемент — элемент")<br> [User-to-Item recommendations](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd) (Рекомендации "пользователь — элемент")
>
> Операции пакетной оценки поддерживаются только сборками, созданными после 21 июля 2016 года.


Иногда необходимо получить рекомендации для нескольких элементов за раз, например, если вы хотите создать кэш рекомендаций или проанализировать типы получаемых рекомендаций.

Операции пакетной оценки являются асинхронными. Сначала вы отправляете запрос, потом ожидаете завершения операции, и после этого получаете результаты.

Ниже приведены конкретные шаги для выполнения таких операций.

1.	Создайте контейнер хранилища Azure, если у вас его еще нет.
2.	Передайте входной файл, в котором описываются все запросы рекомендаций к хранилищу BLOB-объектов Azure.
3.	Запустите пакетное задание оценки.
4.	Дождитесь завершения асинхронной операции.
5.	После завершения операции заберите результаты из хранилища BLOB-объектов.

Давайте рассмотрим каждый из этих шагов более детально.

## Создание контейнера хранилища

Перейдите на [портал Azure](https://portal.azure.com) и создайте новую учетную запись хранения, если у вас ее нет. Для этого последовательно выберите пункты **Создать** > **Данные** + **Хранение** > **Учетная запись хранения**.

Далее необходимо создать контейнеры больших двоичных объектов, в которых будут храниться входные и выходные данные пакетной операции.

Отправьте входной файл, в котором описываются все запросы рекомендаций к хранилищу BLOB-объектов Azure. В этом примере он будет называться input.json. После создания контейнера необходимо отправить из службы рекомендаций файл, содержащий сведения о каждом запросе, который необходимо выполнить.

Пакетная операция может выполнять только один тип запросов для конкретной сборки. Мы расскажем, как определить эти сведения, в следующем разделе. А пока давайте предположим, что мы собираемся выполнить рекомендации по элементам из конкретной сборки. В таком случае наш входной файл содержит входные данные (здесь это начальные элементы) для каждого запроса.

Вот как может выглядеть файл input.json:

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }

Как видно, это JSON-файл, каждый запрос в котором содержит сведения, необходимые для отправки запроса на получение рекомендаций. Создайте похожий файл JSON для запросов, которые вы хотите выполнить, и скопируйте его в контейнер, созданный ранее в хранилище BLOB-объектов.

## Запуск пакетного задания

Следующий шаг заключается в отправке нового пакетного задания. Дополнительные сведения см. в [справочнике по API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/).

В тексте запроса API необходимо определить расположения для входных и выходных файлов, а также файлов с ошибками. Также необходимо указать учетные данные для доступа ко всем этим расположениям. Кроме того, необходимо задать некоторые параметры, которые применяются ко всему пакету (тип запрашиваемых рекомендаций, используемая модель и сборка, количество результатов на один вызов и т. д.).

Вот как может выглядеть текст запроса:

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

Обратите внимание на несколько важных моментов.

-	В настоящее время для параметра **authenticationType** необходимо использовать только значение **PublicOrSas**.

-	Чтобы предоставить API-интерфейсу рекомендаций разрешения на чтение из учетной записи хранилища BLOB-объектов и запись данных в нее, необходимо получить маркер подписанного URL-адреса (SAS). Дополнительные сведения о способах создания маркеров SAS можно найти на [странице об API рекомендаций](../storage/storage-dotnet-shared-access-signature-part-1.md).

-	В настоящее время параметр **apiName** поддерживает только значение **ItemRecommend**, которое используется для рекомендаций "элемент — элемент". Пакетная обработка пока не поддерживает рекомендации "пользователь — элемент".

## Ожидание завершения асинхронной операции

После запуска пакетной операции в ответе вернется заголовок Operation-Location, в котором содержатся все необходимые сведения для отслеживания операции. Для отслеживания пакетной операции (как и операции создания) используется [API получения состояния операции](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da).

## Получение результатов

После завершения операции, если не возникло никаких ошибок, вы можете забрать результаты из хранилища BLOB-объектов, указанного для хранения выходных данных.

Ниже приводится пример таких выходных данных. В этом примере для краткости представлены результаты пакетной операции только для двух запросов.

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## Дополнительные сведения об ограничениях

-	Для каждой подписки можно единовременно выполнять только одно пакетное задание.
-	Размер входного файла для пакетного задания не может превышать 2 МБ.

<!---HONumber=AcomDC_0824_2016-->