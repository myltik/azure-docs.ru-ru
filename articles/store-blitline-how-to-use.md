<properties linkid="develop-net-how-to-guides-blitline-image-processing-service" urlDisplayName="Служба обработки изображений Blitline" pageTitle="Использование Blitline для обработки изображений — руководство по компонентам Azure " metaKeywords="" description="Узнайте, как использовать службу Blitline для обработки изображений в пределах приложения Azure." metaCanonical="" services="" documentationCenter=".NET" title="Использование Blitline с Azure и хранилищем Azure" authors="pennij" solutions="" manager="" editor="" />





# Использование Blitline с Azure и хранилищем Azure

В этом руководстве объясняется, как получить доступ к службам Blitline и отправлять задания в Blitline.

## Оглавление

[Что такое Blitline?][]
[Чем Blitline НЕ является][]
[Создание учетной записи Blitline][]
[Создание задания Blitline][]
[Сохранение изображения в хранилище Azure][]
[Следующие шаги][]

## <a id="whatis"></a>Что такое Blitline?

Blitline — это облачная служба обработки изображений, обеспечивающая обработку изображений корпоративного класса по доступной цене, которая значительно меньше стоимости самостоятельной разработки аналогичного решения.

Фактически, обработка изображений осуществляется довольно регулярно, при этом для каждого отдельного сайта соответствующие функции обычно реализуются "с нуля". Мы понимаем это, так как сами множество раз выполняли эту работу. Однажды мы решили, что пора создать универсальное решение. Мы знаем, как быстро и эффективно добиться этого, сохранив при этом работу всех сотрудников.

Дополнительные сведения см. на веб-сайте [http://www.blitline.com](http://www.blitline.com).

## <a id="whatisnot"></a>Чем Blitline НЕ является...

Чтобы прояснить преимущества Blitline, проще определить, что Blitline НЕ делает.

- Blitline НЕ содержит мини-приложения HTML для отправки изображений. Для Blitline необходимо использовать общедоступные изображения или изображения с ограниченными разрешениями.

- Blitline НЕ имеет функций динамической обработки изображений, аналогичных Aviary.com.

- Blitline НЕ поддерживает отправку изображений, то есть нельзя принудительно отправлять изображения напрямую в Blitline. Необходимо передавать их в хранилище Azure или в другие места, поддерживаемые Blitline, и затем указывать их расположение службе Blitline.

- Blitline использует массовый параллелизм и НЕ выполняет синхронную обработку. То есть вы должны предоставить нам URL-адрес обратной передачи postback_url, чтобы мы могли сообщить, где проводится обработка.

## <a id="createaccount"></a>Создание учетной записи Blitline

[WACOM.INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a id="createjob"></a>Создание задания Blitline

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

Дополнительные сведения о поддерживаемых *функциях* см. здесь: <http://http://www.blitline.com/docs/functions>

Кроме того, см. документацию о параметрах заданий: <http://www.blitline.com/docs/api>

Получив JSON, достаточно отправить ее с помощью **POST** на адрес `http://api.blitline.com/jobs`

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


Это означает, что служба Blitline получила запрос, поместила его в очередь обработки, а после его выполнения изображение будет доступно по адресу:
**https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_APP\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a id="saveazure"></a>Сохранение изображения в учетной записи хранения Azure

При наличии учетной записи хранения Azure можно легко настроить Blitline на принудительную отправку обработанных изображений в ваш контейнер Azure. Добавив "azure_destination", вы определяете место и разрешения для принудительной отправки изображения службой Blitline.

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


Заполнив параметры, ВЫДЕЛЕННЫЕ ЗАГЛАВНЫМИ БУКВАМИ, собственными значениями, можно отправить эту JSON на адрес http://api.blitline.com/job, и изображение SRC будет обработано с помощью фильтра размытия и затем отправлено в заданное расположение Azure.

<h3>Обратите внимание на следующее:</h3>

Подпись общего доступа (SAS) должна содержать весь URL-адрес SAS, включая имя конечного файла.

Пример:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


Вы также можете ознакомиться с последней версией документов о хранилище Azure от Blitline [здесь](http://www.blitline.com/docs/azure_storage).


## <a id="nextsteps"></a>Дальнейшие действия

Посетите сайт blitline.com, чтобы узнать обо всех других возможностях:

* Документы о конечных точках API Blitline <http://www.blitline.com/docs/api>
* Функции API Blitline <http://www.blitline.com/docs/functions>
* Примеры API Blitline <http://www.blitline.com/docs/examples>
* Сторонняя библиотека Nuget <http://nuget.org/packages/Blitline.Net>


  [Следующие шаги]: #nextsteps
  [Что такое Blitline?]: #whatis
  [Чем Blitline НЕ является]: #whatisnot
  [Создание учетной записи Blitline]: #createaccount
  [Создание задания Blitline]: #createjob
  [Сохранение изображения в хранилище Azure]: #saveazure


