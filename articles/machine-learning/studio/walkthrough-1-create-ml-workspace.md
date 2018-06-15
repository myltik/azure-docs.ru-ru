---
title: Шаг 1. Создание рабочей области машинного обучения | Документация Майкрософт
description: 'Первый этап пошагового руководства по разработке прогнозного решения: узнайте, как настроить рабочую область студии машинного обучения Azure.'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: b3c97e3d-16ba-4e42-9657-2562854a1e04
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: f6f6ed67a1ddde3d8f3102a8f7032dc03b0e91b9
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835820"
---
# <a name="walkthrough-step-1-create-a-machine-learning-workspace"></a>Шаг 1. Создание рабочей области машинного обучения
Это первый этап из пошагового руководства [Разработка решения для прогнозной аналитики в службе машинного обучения Azure](walkthrough-develop-predictive-solution.md).

1. **Создание рабочей области машинного обучения**
2. [Отправка существующих данных](walkthrough-2-upload-data.md)
3. [Создание нового эксперимента](walkthrough-3-create-new-experiment.md)
4. [Обучение и анализ моделей](walkthrough-4-train-and-evaluate-models.md)
5. [Развертывание веб-службы](walkthrough-5-publish-web-service.md)
6. [Доступ к веб-службе](walkthrough-6-access-web-service.md)

- - -
<!-- This needs to be updated to refer to the new way of creating workspaces in the Ibiza portal -->

Для использования студии машинного обучения требуется рабочая область машинного обучения Microsoft Azure. Такая рабочая область содержит инструменты, необходимые для создания, публикации экспериментов и управления ими.  

Администратору подписки Azure нужно создать рабочую область и добавить вас в качестве владельца или участника. Подробные сведения см. в статье [Создание рабочей области машинного обучения Azure и предоставление к ней общего доступа](create-workspace.md).

Создав рабочую область, откройте Студию машинного обучения ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)). Если у вас несколько рабочих областей, можно выбрать рабочую область на панели инструментов в правом верхнем углу окна.

![Выбор рабочей области в Студии][2]

> [!TIP]
> Владелец рабочей области может предоставить общий доступ к экспериментам, над которыми он работает, пригласив других пользователей в свою рабочую область. Это можно сделать в Студии машинного обучения на странице **ПАРАМЕТРЫ** . Для каждого пользователя необходима учетная запись Майкрософт или учетная запись организации.
> 
> На странице **Параметры** щелкните **Пользователи**, а затем — **Invite more users** (Пригласить пользователей) в нижней части окна.
> 
> 

- - -
**Далее:[ отправка имеющихся данных](walkthrough-2-upload-data.md)**

[1]: ./media/walkthrough-1-create-ml-workspace/create1.png
[2]: ./media/walkthrough-1-create-ml-workspace/open-workspace.png
