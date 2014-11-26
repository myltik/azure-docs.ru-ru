<properties title="Step 6: Access the Azure Machine Learning web service" pageTitle="Step 6: Access the Machine Learning web service | Azure" description="Step 6: Access an active Azure Machine Learning API web service" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

Это последний шаг из пошагового руководства [Разработка прогнозирующего решения с помощью Azure ML][Разработка прогнозирующего решения с помощью Azure ML].

1.  [Создание рабочего пространства ML][Создание рабочего пространства ML]
2.  [Отправка существующих данных][Отправка существующих данных]
3.  [Создание нового эксперимента][Создание нового эксперимента]
4.  [Подготовка и оценка моделей][Подготовка и оценка моделей]
5.  [Публикация веб-службы][Публикация веб-службы]
6.  **Доступ к веб-службе**

------------------------------------------------------------------------

# Шаг 6. Доступ к веб-службе машинного обучения Azure

Чтобы веб-служба была полезной, пользователи должны иметь возможность отправлять данные в службу и получать результаты. Веб-службой является веб-служба Azure, которая может получать и возвращать данные с помощью одного из двух способов:

-   **Запрос/Ответ** - пользователь отправляет одиночный набор данных о кредитах в службу с помощью протокола HTTP, и служба в качестве ответа возвращает одиночный набор результатов.
-   **Пакетное выполнение** - пользователь отправляет в службу URL-адрес BLOB-объекта Azure, который содержит одну или несколько строк данных о кредитах. Служба хранит результаты в другом BLOB-объекте и возвращает URL-адрес данного контейнера.

На вкладке **ПАНЕЛЬ МОНИТОРИНГА** для веб-службы существуют две ссылки на информацию, которая поможет разработчику разрабатывать код для доступа к этой службе. Щелкните ссылку **Страница справки API** в строке **ЗАПРОС/ОТВЕТ**. Появляется страница с примером кода для использования протокола запроса/ответа службы. Аналогичным образом, если щелкнуть ссылку в строке **ПАКЕТНОЕ ВЫПОЛНЕНИЕ**, появляется страница с примером кода для отправки пакетного запроса в службу.

Страница справки API содержит примеры для языков программирования R, C# и Python. Например, ниже приведен код R, который можно использовать для доступа к опубликованной нами веб-службе (фактический URL-адрес службы будет отображаться в примере кода):

    library("RCurl")
    library("RJSONIO")

    h = basicTextGatherer()
    req = list(Id="score00001",
     Instance=list(FeatureVector=list(
        "row.names"= "0",
        "Status of checking account"= "0",
        "Duration in months"= "0",
        "Credit history"= "0",
        "Purpose"= "0",
        "Credit amount"= "0",
        "Savings account/bond"= "0",
        "Present employment since"= "0",
        "Installment rate in percentage of disposable income"= "0",
        "Personal status and sex"= "0",
        "Other debtors"= "0",
        "Present residence since"= "0",
        "Property"= "0",
        "Age in years"= "0",
        "Other installment plans"= "0",
        "Housing"= "0",
        "Number of existing credits"= "0",
        "Job"= "0",
        "Number of people providing maintenance for"= "0",
        "Telephone"= "0",
        "Foreign worker"= "0",
        "Credit risk"= "0"
     ),GlobalParameters=fromJSON('{}')))

    body = toJSON(req)
    api_key = "abc123" # You can obtain the API key from the publisher of the web service

    h$reset()
    curlPerform(url = "http://xxx.cloudapp.net/workspaces/xxx/services/xxx/score",
                httpheader=c('Content-Type' = "application/json", 'Authorization' = "Bearer " + api_key),
                postfields=body,
                writefunction = h$update,
                verbose = TRUE
                )

    result = h$value()

  [Разработка прогнозирующего решения с помощью Azure ML]: ../machine-learning-walkthrough-develop-predictive-solution/
  [Создание рабочего пространства ML]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Отправка существующих данных]: ../machine-learning-walkthrough-2-upload-data/
  [Создание нового эксперимента]: ../machine-learning-walkthrough-3-create-new-experiment/
  [Подготовка и оценка моделей]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Публикация веб-службы]: ../machine-learning-walkthrough-5-publish-web-service/
