---
title: Пример. Создание пользовательского навыка в конвейере когнитивного поиска в службе "Поиск Azure" | Документы Майкрософт
description: Демонстрация использования API перевода текста в пользовательском навыке, сопоставленном с конвейером индексирования когнитивного поиска в Поиске Azure.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 056cff192b25068fa2e895fd46d143a834b7af0b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641090"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>Пример создания пользовательского навыка с помощью API перевода текста

В этом примере вы можете узнать, как создать пользовательский навык веб-API, принимающий текст на любом языке и переводящий его на английский язык. Этот пример использует [Функцию Azure](https://azure.microsoft.com/services/functions/) для заключения [API перевода текста](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) в оболочку, чтобы он реализовывал интерфейс пользовательского навыка.

## <a name="prerequisites"></a>предварительным требованиям

+ Вы можете прочитать статью об [интерфейсе пользовательского навыка](cognitive-search-custom-skill-interface.md), если не знакомы с интерфейсом ввода-вывода, который должен реализовывать пользовательский навык.

+ [Зарегистрируйтесь для доступа к API перевода текстов](../cognitive-services/translator/translator-text-how-to-signup.md) и получите ключ API для его использования.

+ Установите [Visual Studio 2017 версии 15.5](https://www.visualstudio.com/vs/) или более поздней версии с рабочей нагрузкой разработки Azure.

## <a name="create-an-azure-function"></a>Создание функции Azure

Хотя этот пример использует Функцию Azure для размещения веб-API, это необязательно.  При условии соблюдения [требований к интерфейсу для когнитивного навыка](cognitive-search-custom-skill-interface.md) выбранный вами подход не имеет значения. Однако Функции Azure упрощают создание пользовательских навыков.

### <a name="create-a-function-app"></a>Создание приложения-функции

1. В меню "Файл" Visual Studio выберите **Создать** > **Проект**.

1. В диалоговом окне "Новый проект" выберите **Установленные**, разверните узел **Visual C#** > **Облако**, выберите **Функции Azure**, укажите имя проекта и нажмите кнопку **ОК**. Имя приложения-функции должно быть допустимым в качестве пространства имен C#, поэтому не используйте символы подчеркивания, дефисы и другие символы, не являющиеся буквенно-цифровыми.

1. Выберите тип **Триггер HTTP**.

1. Для учетной записи хранения можно выбрать **Нет**, так как для этой функции никакое хранилище не требуется.

1. Нажмите кнопку **ОК**, чтобы создать проект функции и функцию, активируемую с помощью HTTP.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Изменение кода для вызова службы Translate Cognitive Service

Visual Studio создает проект и класс в нем, содержащий стандартный код для выбранного типа функции. Атрибут метода *FunctionName* задает имя функции. Атрибут *HttpTrigger* указывает, что функция вызывается HTTP-запросом.

Замените содержимое файла *Function1.cs* следующим кодом:

```csharp
using System.IO;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
        #region classes used to serialize the response
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }
        #endregion


        /// <summary>
        /// Note that this function can translate up to 1000 characters. If you expect to need to translate more characters, use 
        /// the paginator skill before calling this custom enricher
        /// </summary>
        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req, 
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string originalLanguage = null;
            string translatedText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                // It could not find a record, then return empty values array.
                return new BadRequestObjectResult(" Could not find valid records in values array");
            }

            recordId = data?.values?.First?.recordId?.Value as string;
            originalText = data?.values?.First?.data?.text?.Value as string;
            originalLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            // Only translate records that actually need to be translated. 
            if (!originalLanguage.Contains("en"))
            {
                translatedText = TranslateText(originalText, "en-us").Result;
            }
            else
            {
                // text is already in English.
                translatedText = originalText;
            }

            // Put together response.
            WebApiResponseRecord responseRecord = new WebApiResponseRecord();
            responseRecord.data = new Dictionary<string, object>();
            responseRecord.recordId = recordId;
            responseRecord.data.Add("text", translatedText);

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            response.values.Add(responseRecord);

            return (ActionResult)new OkObjectResult(response); 
        }

        /// <summary>
        /// Use Cognitive Service to translate text from one language to antoher.
        /// </summary>
        /// <param name="myText">The text to translate</param>
        /// <param name="destinationLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string myText, string destinationLanguage)
        {
            string host = "https://api.microsofttranslator.com";
            string path = "/V2/Http.svc/Translate";

            // NOTE: Replace this example key with a valid subscription key.
            string key = "064d8095730d4a99b49f4bcf16ac67f8";

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

            List<KeyValuePair<string, string>> list = new List<KeyValuePair<string, string>>() {
                new KeyValuePair<string, string>(myText, "en-us")
            };

            StringBuilder totalResult = new StringBuilder();

            foreach (KeyValuePair<string, string> i in list)
            {
                string uri = host + path + "?to=" + i.Value + "&text=" + System.Net.WebUtility.UrlEncode(i.Key);

                HttpResponseMessage response = await client.GetAsync(uri);

                string result = await response.Content.ReadAsStringAsync();

                // Parse the response XML
                System.Xml.XmlDocument xmlResponse = new System.Xml.XmlDocument();
                xmlResponse.LoadXml(result);
                totalResult.Append(xmlResponse.InnerText); 
            }

            return totalResult.ToString();
        }
    }
}
```

Обязательно введите собственное значение *ключа* в методе *TranslateText*, используя ключ, полученный при регистрации для API перевода текста.

Этот пример является простым обогатителем, который работает только для одной записи за раз. Этот факт становится важным позже — при настройке размера пакета для набора навыков.

## <a name="test-the-function-from-visual-studio"></a>Тестирование функции из Visual Studio

Нажмите клавишу **F5** для запуска программы и тестирования поведения функции. Используйте Postman или Fiddler для выполнения вызова, как показано ниже:

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>Тело запроса
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "es"
            }
        }
   ]
}
```
### <a name="response"></a>Ответ
Вы должны увидеть отклик, аналогичный следующему примеру:

```json
{
    "values": [
        {
            "recordId": "a1",
            "data": {
                "text": "This is a contract in English"
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Публикация функции в Azure

Если вы удовлетворены поведением функции, ее можно опубликовать.

1. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите пункт **Опубликовать**. Выберите **Создать** > **Опубликовать**.

1. Если вы еще не подключили Visual Studio к учетной записи Azure, выберите **Добавить учетную запись...**

1. Следуйте инструкциям на экране. Вам будет предложено указать учетную запись Azure, группу ресурсов, план размещения и учетную запись хранения, которые вы хотите использовать. При отсутствии группы ресурсов, плана размещения и учетной записи хранения их можно создать. По завершении выберите **Создать**.

1. После завершения развертывания запомните URL-адрес сайта. Это адрес приложения-функции в Azure. 

1. На [портале Azure](https://portal.azure.com) перейдите в группу ресурсов и найдите опубликованную функцию перевода. В разделе **Управление** должны отображаться ключи узла. Выберите значок **копирования** для ключа узла *по умолчанию*.  


## <a name="test-the-function-in-azure"></a>Тестирование функции в Azure

Теперь, когда у вас есть ключ узла по умолчанию, протестируйте функцию следующим образом:

```http
POST https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]
```
### <a name="request-body"></a>Текст запроса
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "es"
            }
        }
   ]
}
```

Это должно дать такой же результат, как и при запуске функции в локальной среде.

## <a name="connect-to-your-pipeline"></a>Подключение к конвейеру
Теперь, когда у вас есть новый пользовательский навык, можно добавить его в ваш набор навыков. Приведенный ниже пример показывает, как вызвать навык. Так как навык не обрабатывает пакеты, добавьте инструкцию, ограничивающую максимальный размер пакета всего лишь значением ```1```, чтобы отправлять документы по одному.

```json
{
    "skills": [
      ...,  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new translator custom skill",
        "uri": "http://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]",
        "batchSize":1,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "translatedText"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие действия
Поздравляем! Вы создали первый пользовательский обогатитель. Теперь вы можете использовать ту же схему для добавления пользовательских функций. 

+ [Добавление пользовательского навыка в конвейер когнитивного поиска](cognitive-search-custom-skill-interface.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
+ [Создание набора навыков (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Сопоставление обогащенных полей](cognitive-search-output-field-mapping.md)