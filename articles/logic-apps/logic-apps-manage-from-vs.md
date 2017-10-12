---
title: "Управление приложениями логики в Visual Studio с помощью Azure Logic Apps | Документация Майкрософт"
description: "Узнайте, как управлять приложениями логики и другими ресурсами Azure с помощью Visual Studio с помощью Visual Studio Cloud Explorer."
author: klam
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 12/19/2016
ms.author: LADocs; klam
ms.openlocfilehash: a5bf24de1a7a2b6d4c1ae6416c95d83ef7506da3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>Управление приложениями логики с помощью Visual Studio Cloud Explorer

Хотя [портал Azure](https://portal.azure.com/) предоставляет отличный способ проектирования приложений логики Azure Logic Apps и управления ими, вы также можете использовать Visual Studio Cloud Explorer для управления многими ресурсами Azure, включая приложения логики. Visual Studio Cloud Explorer позволяет просматривать, изменять и скачивать опубликованные приложения логики, а также управлять ими. К задачам управления относятся включение, отключение и просмотр журнала выполнения. 

Чтобы иметь доступ к приложениям логики в Visual Studio и управлять ими, установите и настройте перечисленные ниже инструменты Visual Studio для Azure Logic Apps. 

## <a name="prerequisites"></a>Предварительные требования

* [Visual Studio 2015 или Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* [пакет Azure SDK последней версии](https://azure.microsoft.com/downloads/) (версия 2.9.1 или выше);
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* доступ к Интернету при использовании внедренного конструктора.

## <a name="install-visual-studio-tools-for-logic-apps"></a>Установка инструментов Visual Studio для приложений логики

После установки необходимых компонентов скачайте и установите инструменты Azure Logic Apps для Visual Studio.

1. Откройте Visual Studio. Откройте меню **Средства** и выберите пункт **Расширения и обновления**.
2. Разверните категорию **В сети**, чтобы можно было выполнять онлайн-поиск в коллекции Visual Studio.
3. Найдите **средства Azure Logic Apps Tools для Visual Studio**. Если потребуется, выполните поиск по названию **Logic Apps**.
4. Нажмите кнопку **Скачать**, чтобы скачать и установить это расширение.
5. После установки перезапустите Visual Studio.

> [!NOTE]
> Чтобы скачать инструменты Azure Logic Apps для Visual Studio напрямую, перейдите в [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9).

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>Поиск приложений логики в Cloud Explorer

1.  Чтобы открыть Cloud Explorer, в меню **Вид** выберите **Cloud Explorer**.
2.  Найдите приложение логики, используя поиск по группе ресурсов или типу ресурса. 

    * При поиске по типу ресурса выберите подписку Azure, разверните раздел **Приложения логики** и выберите свое приложение. 
    * При поиске по группе ресурсов разверните группу ресурсов, которая содержит ваше приложение логики, и выберите это приложение.

    Чтобы просмотреть команды для приложения логики, щелкните это приложение правой кнопкой мыши или воспользуйтесь меню **Действия** в нижней части Cloud Explorer.

    ![Поиск приложения логики](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-apps-designer"></a>Изменение приложения логики с помощью конструктора приложений логики

Из Cloud Explorer можно открыть развернутое приложение логики в том же конструкторе, что и на портале Azure. 

* Чтобы изменить приложение логики, в Cloud Explorer щелкните это приложение правой кнопкой мыши и выберите команду **Открыть в редакторе приложений логики**. 

* Для публикации обновлений в облаке выберите **Опубликовать**. 

* Чтобы запустить новое выполнение, выберите **Запустить триггер**.

![Конструктор Logic Apps](./media/logic-apps-manage-from-vs/designer.png)

Из конструктора также можно **скачать** приложение логики. Это действие автоматически параметризует определение приложения логики и сохраняет его как шаблон развертывания Azure Resource Manager. Этот шаблон развертывания можно добавить в проект группы ресурсов Azure.

## <a name="browse-your-logic-app-run-history"></a>Просмотр журнала выполнения приложения логики

Чтобы просмотреть журнал выполнения приложения логики, щелкните это приложение правой кнопкой мыши и выберите команду **Открыть журнал запусков**. Чтобы упорядочить журнал выполнения по одному из отображаемых свойств, выберите соответствующий заголовок столбца.

![Журнал выполнения](media/logic-apps-manage-from-vs/runs.png)

Дважды щелкните один из экземпляров выполнения, чтобы отобразился журнал выполнения для этого экземпляра, где можно просмотреть результаты выполнения, включая входные и выходные данные каждого шага.

![Результаты в журнале выполнения, входные и выходные данные по шагам](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Создание приложения логики](logic-apps-create-a-logic-app.md)
* [Создание и развертывание приложений логики Azure в Visual Studio](logic-apps-deploy-from-vs.md).
* [Примеры приложений логики и распространенные сценарии](logic-apps-examples-and-scenarios.md)
* [Видео. Автоматизация бизнес-процессов с помощью Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694)
* [Видео. Интеграция систем с Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
