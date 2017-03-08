---
title: "Шаг 1. Создание рабочей области машинного обучения | Документация Майкрософт"
description: "Первый этап пошагового руководства по разработке прогнозного решения: узнайте, как настроить рабочую область студии машинного обучения Azure."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b3c97e3d-16ba-4e42-9657-2562854a1e04
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: a9ebbbdc431a34553de04e920efbbc8c2496ce5f
ms.openlocfilehash: 116dbf0ee30497d82c984b10e61ae02301e820a4


---
# <a name="walkthrough-step-1-create-a-machine-learning-workspace"></a>Шаг 1. Создание рабочей области машинного обучения
Это первый этап из пошагового руководства [Разработка решения для прогнозной аналитики в службе машинного обучения Azure](machine-learning-walkthrough-develop-predictive-solution.md).

1. **Создание рабочей области машинного обучения**
2. [Отправка существующих данных](machine-learning-walkthrough-2-upload-data.md)
3. [Создание нового эксперимента](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Обучение и анализ моделей](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Развертывание веб-службы](machine-learning-walkthrough-5-publish-web-service.md)
6. [Доступ к веб-службе](machine-learning-walkthrough-6-access-web-service.md)

- - -
<!-- This needs to be updated to refer to the new way of creating workspaces in the Ibiza portal -->

Для использования студии машинного обучения требуется рабочая область машинного обучения Microsoft Azure. Такая рабочая область содержит инструменты, необходимые для создания, публикации экспериментов и управления ими.  

<!--
## To create a workspace
1. Sign in to the [Azure classic portal](https://manage.windowsazure.com).
2. In the  Azure services panel, click **MACHINE LEARNING**.  
   ![Create workspace][1]
3. Click **CREATE AN ML WORKSPACE**.
4. On the **QUICK CREATE** page, enter your workspace information and then click **CREATE AN ML WORKSPACE**.
-->

Администратору подписки Azure понадобится создать рабочую область и добавить вас в качестве владельца или участника. Подробные сведения см. в статье [Создание рабочей области машинного обучения Azure и предоставление к ней общего доступа](machine-learning-create-workspace.md).

Создав рабочую область, откройте Студию машинного обучения ([https://studio.azureml.net](https://studio.azureml.net)). Если это ваша единственная рабочая область, Студия откроет ее автоматически. В противном случае можно выбрать рабочую область на панели инструментов в правом верхнем углу окна.

![Выбор рабочей области в Студии][2]

> [!TIP]
> Владелец рабочей области может предоставить общий доступ к экспериментам, над которыми он работает, пригласив других пользователей в свою рабочую область. Это можно сделать в Студии машинного обучения на странице **ПАРАМЕТРЫ** . Для каждого пользователя необходима учетная запись Майкрософт или учетная запись организации.
> 
> На странице **Параметры** щелкните **Пользователи**, а затем — **Invite more users** (Пригласить пользователей) в нижней части окна.
> 
> 

- - -
**Далее:[ отправка имеющихся данных](machine-learning-walkthrough-2-upload-data.md)**

[1]: ./media/machine-learning-walkthrough-1-create-ml-workspace/create1.png
[2]: ./media/machine-learning-walkthrough-1-create-ml-workspace/open-workspace.png



<!--HONumber=Feb17_HO3-->


