<properties linkid="develop-net-how-to-guides-blitline-image-processing-service" urlDisplayName="Blitline Image Processing Service" pageTitle="How to use Blitline for image processing - Azure feature guide " metaKeywords="" description="Learn how to use the Blitline service to process images within an Azure application." metaCanonical="" services="" documentationCenter=".NET" title="How to use Blitline with Azure and Azure Storage" authors="pennij" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="pennij"></tags>

# Как использовать Blitline с Azure и службой хранилища Azure

В этом руководстве объясняется, как получить доступ к службам Blitline и отправлять задания в Blitline.

## Оглавление

[Что такое Blitline?][Что такое Blitline?]
[Чем Blitline НЕ является][Чем Blitline НЕ является]
[Создание учетной записи Blitline][Создание учетной записи Blitline]
[Как создать задание Blitline][Как создать задание Blitline]
[Как сохранить изображение в службе хранилища Azure][Как сохранить изображение в службе хранилища Azure]
[Дальнейшие действия][Дальнейшие действия]

## <span id="whatis"></span></a>Что такое Blitline?

Blitline — это облачная служба обработки изображений, обеспечивающая обработку изображений корпоративного класса по доступной цене, которая значительно меньше стоимости самостоятельной разработки аналогичного решения.

Фактически, обработка изображений осуществляется довольно регулярно, при этом для каждого отдельного сайта соответствующие функции обычно реализуются "с нуля". Мы понимаем это, так как сами множество раз делали это. Однажды мы решили, что пора создать универсальное решение. Мы знаем, как быстро и эффективно добиться этого, сохранив при этом работу всех сотрудников.

Дополнительные сведения см. на веб-сайте [][]<http://www.blitline.com></a>.

## <span id="whatisnot"></span></a>Чем Blitline НЕ является...

Чтобы прояснить преимущества Blitline, проще определить, что Blitline НЕ делает.

-   Blitline НЕ содержит мини-приложения HTML для отправки изображений. Для Blitline необходимо использовать общедоступные изображения или изображения с ограниченными разрешениями.

-   Blitline НЕ поддерживает функции динамической обработки изображений, аналогичных Aviary.com.

-   Blitline НЕ поддерживает отправку изображений, то есть принудительная отправка изображений напрямую в Blitline невозможна. Необходимо передавать их в хранилище Azure или в другие места, поддерживаемые Blitline, и затем указывать их расположение службе Blitline.

-   Blitline использует массовый параллелизм и НЕ выполняет синхронную обработку. То есть вы должны предоставить нам URL-адрес обратной передачи postback\_url, чтобы мы могли сообщить, когда обработка завершена.

## <span id="createaccount"></span></a>Создание учетной записи Blitline

[WACOM.INCLUDE [blitline-signup][blitline-signup]]

## <span id="createjob"></span></a>Как создать задание Blitline

Blitline использует JSON для определения действий, которые вы хотите выполнить для изображения. Эта JSON состоит из нескольких простых полей.

Ниже приведен простейший пример:

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

Здесь приведена JSON, которая обратится к изображению SRC "... boys.jpeg" и увеличит его размер до 240x140.

Идентификатор приложения можно найти на вкладке **СВЕДЕНИЯ О ПОДКЛЮЧЕНИИ** или **УПРАВЛЕНИЕ** в Azure. Это ваш секретный идентификатор, который позволяет выполнять задания в Blitline.

Параметр "save" определяет сведения о расположении, куда требуется поместить изображение после обработки. В этом простейшем случае такое расположение не задано. Если расположение не определено, Blitline сохраняет изображение локально (и временно) в уникальном расположении в облаке. Сведения об этом расположении можно получить из JSON, возвращаемой Blitline при вызове. Идентификатор "image" является обязательным и возвращается, когда требуется идентифицировать данное конкретное сохраненное изображение.

Дополнительную информацию о поддерживаемых *функциях* см. здесь: <http://www.blitline.com/docs/functions>

Кроме того, см. документацию по параметрам заданий здесь: <http://www.blitline.com/docs/api>

Получив объект JSON, достаточно отправить его с помощью команды **POST** на адрес `http://api.blitline.com/jobs`

Обратно возвращается JSON следующего вида:

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }

Это означает, что ваш запрос поступил в Blitline и был поставлен в очередь обработки. Когда же запрос будет выполнен, готовое изображение будет доступно по адресу:
**[https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\\_APP\\_ID/CK3f0xBF\_2bV6wf7gEZE8w.jpg][https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\\_APP\\_ID/CK3f0xBF\_2bV6wf7gEZE8w.jpg]**

## <span id="saveazure"></span></a>Как сохранить изображение в учетной записи хранения Azure

При наличии учетной записи хранения Azure можно легко настроить Blitline на принудительную отправку обработанных изображений в ваш контейнер Azure. Добавив "azure\_destination", вы определяете место и разрешения для принудительной отправки изображения службой Blitline.

Пример:

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'

Указав необходимые значения для параметров, ВЫДЕЛЕННЫХ ЗАГЛАВНЫМИ БУКВАМИ, вы можете отправить этот объект JSON на адрес <http://api.blitline.com/job>. SRC-изображение будет обработано с использованием фильтра размытия и затем отправлено в заданное расположение Azure.

### Обратите внимание на следующее:

Подпись общего доступа (SAS) должна содержать весь URL-адрес SAS, включая имя конечного файла.

Пример:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D

Вы также можете ознакомиться с последней версией документов о хранилище Azure от Blitline [здесь][здесь].

## <span id="nextsteps"></span></a> Дальнейшие действия

Посетите сайт blitline.com, чтобы узнать обо всех других возможностях:

-   Документы о конечных точках API Blitline <http://www.blitline.com/docs/api>
-   Функции API Blitline <http://www.blitline.com/docs/functions>
-   Примеры API Blitline <http://www.blitline.com/docs/examples>
-   Библиотека Nuget стороннего поставщика <http://nuget.org/packages/Blitline.Net>

  [Что такое Blitline?]: #whatis
  [Чем Blitline НЕ является]: #whatisnot
  [Создание учетной записи Blitline]: #createaccount
  [Как создать задание Blitline]: #createjob
  [Как сохранить изображение в службе хранилища Azure]: #saveazure
  [Дальнейшие действия]: #nextsteps
  []: http://www.blitline.com
  [blitline-signup]: ../includes/blitline-signup.md
  [https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\\\_APP\\\_ID/CK3f0xBF\_2bV6wf7gEZE8w.jpg]: https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_APP\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg
  [здесь]: http://www.blitline.com/docs/azure_storage
