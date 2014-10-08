<properties title="Develop a predictive solution with Azure Machine Learning" pageTitle="Develop a predictive solution with Machine Learning | Azure" description="Walkthrough of how to create a predictive analytics experiment in Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"/>

# Разработка прогнозирующего решения с помощью машинного обучения Azure

Предположим, требуется спрогнозировать кредитный риск претендентов на кредит на основе сведений, которые они предоставляют в заявке на кредит.

Конечно, это сложная проблема, но давайте слегка упростим параметры задачи и используем ее в качестве примера использования машинного обучения Microsoft Azure с ML Studio и службой API ML для создания такого прогнозирующего аналитического решения.

В этом пошаговом руководстве мы пройдем процесс разработки прогнозирующей аналитической модели в ML Studio, а затем опубликуем эту модель в службе API ML. Начнем с общедоступных данных по кредитным рискам, разработаем и подготовим прогнозирующую модель на основе этих данных, а затем опубликуем ее как веб-службу, которую смогут использовать другие.

Будут выполняться следующие действия.

1.  [Создание рабочего пространства ML][]
2.  [Отправка существующих данных][]
3.  [Создание нового эксперимента][]
4.  [Подготовка и оценка моделей][]
5.  [Публикация веб-службы][]
6.  [Доступ к веб-службе][]

Это пошаговое руководство построено на упрощенной версии
[эксперимента с примером прогнозирования кредитных рисков][], включенного в ML Studio.

  [Создание рабочего пространства ML]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Отправка существующих данных]: ../machine-learning-walkthrough-2-upload-data/
  [Создание нового эксперимента]: ../machine-learning-walkthrough-3-create-new-experiment/
  [Подготовка и оценка моделей]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Публикация веб-службы]: ../machine-learning-walkthrough-5-publish-web-service/
  [Доступ к веб-службе]: ../machine-learning-walkthrough-6-access-web-service/
  [эксперимента с примером прогнозирования кредитных рисков]: ../machine-learning-sample-credit-risk-prediction/
