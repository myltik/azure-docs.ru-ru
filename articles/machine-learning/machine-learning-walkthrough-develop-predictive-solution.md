<properties
	pageTitle="Прогнозное решение для оценки кредитных рисков с помощью машинного обучения | Microsoft Azure"
	description="Подробное пошаговое руководство по созданию решения для прогнозной аналитики в Студии машинного обучения Azure для оценки кредитных рисков."	
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="07/10/2015"
	ms.author="garye"/>


# Пошаговое руководство по разработке решения для прогнозной аналитики в службе машинного обучения Azure для оценки кредитных рисков

Предположим, требуется спрогнозировать кредитный риск претендентов на кредит на основе сведений, которые они предоставляют в заявке на кредит.

Оценка кредитных рисков — несомненно, сложная задача, но давайте немного упростим ее вводные. Затем мы сможем использовать ее как пример того, как с помощью машинного обучения Microsoft Azure, Студии машинного обучения и веб-службы машинного обучения создать решение для прогнозной аналитики.

В этой статье мы пошагово пройдем процесс разработки модели прогнозной аналитики в Студии машинного обучения, а затем опубликуем эту модель в веб-службе машинного обучения Azure. Для начала мы воспользуемся общедоступными данными по кредитным рискам, разработаем и обучим на основе этих данных прогнозную модель, а затем опубликуем ее как веб-службу, которую смогут использовать другие для оценки рисков по кредитам.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Откройте Студию машинного обучения: [https://studio.azureml.net/Home](https://studio.azureml.net/Home). Дополнительную информацию о начале работы со Студией машинного обучения см. на [домашней странице Студии машинного обучения Microsoft Azure](https://studio.azureml.net/).

Создание нашего решения для оценки кредитных рисков предполагает такие действия:

1.	[Создание рабочей области машинного обучения](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Отправка существующих данных](machine-learning-walkthrough-2-upload-data.md)
3.	[Создание нового эксперимента](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Обучение и анализ моделей](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Публикация веб-службы](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Доступ к веб-службе](machine-learning-walkthrough-6-access-web-service.md)

Это пошаговое руководство построено на упрощенной версии [примера эксперимента прогнозирования кредитных рисков](../machine-learning-sample-credit-risk-prediction.md), включенного в Студию машинного обучения.
 

<!---HONumber=July15_HO3-->