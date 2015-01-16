<properties title="Step 1: Create an Azure Machine Learning workspace" pageTitle="Шаг 1. Создание рабочей области машинного обучения | Azure" description="Шаг 1. Создание новой рабочей области Студии машинного обучения Azure" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="garye" />


Это первый шаг из пошагового руководства [Разработка прогнозирующего решения с помощью Azure ML][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/

1.	**Создание рабочей области ML**
2.	[Отправка существующих данных][upload-data]
3.	[Создание нового эксперимента][create-new]
4.	[Обучение и анализ моделей][train-models]
5.	[Публикация веб-службы][publish]
6.	[Доступ к веб-службе][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Шаг 1. Создание рабочей области машинного обучения Azure

Для использования ML Studio требуется рабочая область ML. Такая рабочая область содержит инструменты, необходимые для создания, публикации экспериментов и управления ими.  

##Создание рабочей области  

1.	Войдите в систему с помощью учетной записи Microsoft Azure.
2.	На панели служб Microsoft Azure выберите **МАШИННОЕ ОБУЧЕНИЕ**.  
![Create workspace][1]

3.	Выберите **СОЗДАТЬ РАБОЧУЮ ОБЛАСТЬ ML**.
4.	На странице **БЫСТРОЕ СОЗДАНИЕ** введите информацию о рабочей области и выберите **СОЗДАТЬ РАБОЧУЮ ОБЛАСТЬ ML**.

	>**ПРИМЕЧАНИЕ**. В поле **ВЛАДЕЛЕЦ РАБОЧЕЙ ОБЛАСТИ** необходимо указать свою учетную запись Майкрософт (например, name@outlook.com) или учетную запись организации.

После создания рабочей области ML она появится в списке на странице **Машинное обучение**.  

>**Подсказка**. Чтобы сделать общими эксперименты, над которыми вы работаете, пригласите других пользователей в свою рабочую область. Это можно сделать в ML Studio на странице **НАСТРОЙКИ**. Для каждого пользователя необходима учетная запись Майкрософт или учетная запись организации.

----------

**Далее: [Отправка существующих данных][upload-data]**

[1]: ./media/machine-learning-walkthrough-1-create-ml-workspace/create1.png
