<properties title="Step 6: Access the Azure Machine Learning web service" pageTitle="Шаг 6: Доступ к веб-службе машинного обучения | Azure" description="Шаг 6. Доступ к активной веб-службе API Машинного обучения Azure" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="garye" />


Это последний шаг пошагового руководства [Разработка прогнозирующего решения с помощью Azure ML][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Создание рабочей области ML][create-workspace]
2.	[Отправка существующих данных][upload-data]
3.	[Создание эксперимента][create-new]
4.	[Подготовка и оценка моделей][train-models]
5.	[Публикация веб-службы][publish]
6.	**Доступ к веб-службе**

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Шаг 6: Доступ к веб-службе машинного обучения Azure

Чтобы веб-служба была полезной, пользователи должны иметь возможность отправлять данные в службу и получать результаты. Данная веб-служба представляет собой веб-службу Azure, которая может получать и возвращать данные одним из двух способов:  

-	**Запрос-ответ** - пользователь отправляет единичный набор данных о кредитах в службу с помощью протокола HTTP, а служба в качестве ответа возвращает одиночный набор результатов.
-	**Пакетное выполнение** - пользователь отправляет в службу URL-адрес BLOB-объекта Azure, который содержит одну или несколько строк данных о кредитах. Служба сохраняет результаты в другом BLOB-объекте и возвращает URL-адрес данного контейнера.  

На вкладке **ПАНЕЛЬ МОНИТОРИНГА** веб-службы существуют две ссылки на данные, которые помогут разработчику разрабатывать код для доступа к этой службе. Щелкните ссылку **Страница справки API** в строке **ЗАПРОС/ОТВЕТ**. Появляется страница с примером кода для использования протокола запроса/ответа службы. Аналогичным образом, если щелкнуть ссылку в строке **ПАКЕТНОЕ ВЫПОЛНЕНИЕ**, появляется страница с примером кода для отправки пакетного запроса в службу.  

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


