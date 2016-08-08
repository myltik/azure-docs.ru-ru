<properties
	pageTitle="Пакетное получение рекомендаций: API рекомендаций для машинного обучения | Microsoft Azure"
	description="Рекомендации для Машинного обучения Azure: пакетное получение рекомендаций"
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
	ms.date="05/24/2016"
	ms.author="luisca"/>

# Пакетное получение рекомендаций

>[AZURE.NOTE] Процесс получения сразу всех рекомендаций более сложный, чем процесс получения наборов результатов рекомендаций по одному. Сведения о получении рекомендаций для одного запроса см. в справочных материалах по API, перейдя по следующим ссылкам:<br> [Рекомендации "элемент — элемент"](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4)<br> [Рекомендации "пользователь — элемент"](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)
>
> Операции пакетной оценки поддерживаются только сборками, созданными после 21 июля 2016 года.


Иногда необходимо получить рекомендации для нескольких элементов за раз. Например, при создании кэша рекомендаций или выполнении анализа типов полученных рекомендаций.

Операции пакетной оценки являются асинхронными. Сначала необходимо отправить запрос, дождаться завершения операции, а затем собрать результаты. Ниже приведены конкретные шаги для выполнения таких операций.

1.	Создайте контейнер службы хранилища Azure, если у вас еще его нет.
2.	Загрузите входной файл, в котором описываются все запросы рекомендаций к хранилищу BLOB-объектов Azure.
3.	Запустите пакетное задание оценки.
4.	Дождитесь завершения асинхронной операции.
5.	Затем соберите результаты из хранилища BLOB-объектов Azure.

Давайте рассмотрим каждый из этих шагов более детально.

## Создание контейнера службы хранилища Azure

Перейдите на [портал управления Azure](https://portal.azure.com) и создайте учетную запись хранения (если у вас ее нет). Для этого последовательно выберите *+Создать* > *Данные* > *Хранилище* > *Учетная запись хранения*.

Далее необходимо создать контейнер больших двоичных объектов, в котором будут храниться входные и выходные данные выполнения пакета.

Загрузите входной файл, в котором описываются все запросы рекомендаций к хранилищу BLOB-объектов Azure. В этом примере он будет называться input.json. После создания контейнера необходимо отправить из службы рекомендаций файл, содержащий сведения о каждом запросе, который необходимо выполнить. Пакетная операция используется для выполнения только одного типа запросов из конкретной сборки. Сведения об определении этой информации приведены в следующем разделе. Сейчас предположим, что мы будем получать рекомендации по элементам не из конкретной сборки. В каждом входном файле содержатся входные данные (в нашем случае это начальные элементы) каждого запроса.

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
    
Как видно, это файл JSON, каждый запрос в котором содержит сведения, необходимые для отправки запроса на получение рекомендаций. Создайте похожий файл JSON для нужных запросов и скопируйте его в контейнер хранилища BLOB-объектов.

## Запуск пакетного задания

Следующий шаг заключается в отправке нового пакетного задания. Дополнительные сведения см. в [справочных материалах по API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/).

В тексте запроса API необходимо определить расположение, где будут храниться входные, выходные файлы и файлы с ошибками, а также указать учетные данные для доступа к этим расположениям. Кроме того, необходимо задать некоторые параметры, которые применяются ко всему пакету (например, тип рекомендаций для запроса, используемая модель и сборка, количество результатов на вызов и т. д.).

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

Обратите внимание на следующее:

-	В настоящее время для параметра AuthenticationType необходимо использовать только значение PublicOrSas.
-	Чтобы предоставить API рекомендаций разрешения на чтение из учетной записи хранилища BLOB-объектов и запись данных в нее, необходимо получить маркер подписанного URL-адреса (SAS). Дополнительные сведения о создании маркеров SAS см. [здесь](../storage/storage-dotnet-shared-access-signature-part-1.md).
-	В настоящее время параметр *apiName* поддерживает только значение ItemRecommend, которое используется для рекомендаций "элемент — элемент". Рекомендации "пользователь — элемент" не поддерживаются операциями пакетной обработки.

## Дождитесь завершения асинхронной операции.

После запуска пакетной операции в ответе вернется заголовок Operation-Location (Операция-расположение), в котором содержатся все необходимые сведения для отслеживания операции. Для отслеживания пакетной операции (как и операции создания) используется [API извлечения состояния операции](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da).

## Получение результатов

После успешного завершения операции из выходного файла, расположенного в хранилище BLOB-объектов, можно извлечь результаты.

Ниже приведен пример того, как может выглядеть выходной файл. В этом примере показаны результаты пакетной операции с двумя запросами.

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


## Ограничения

-	Для каждой подписки можно за раз вызвать только одно пакетное задание.
-	Размер входного файла пакетного задания не может превышать 2 МБ.

<!---HONumber=AcomDC_0727_2016-->