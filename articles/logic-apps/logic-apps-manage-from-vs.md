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
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: 2fafb3c127183c4903ed7ee0da5811988db9116c
ms.openlocfilehash: 9a542f83eb35b159de04f0395cd850376da498de
ms.lasthandoff: 03/02/2017

---

# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>Управление приложениями логики с помощью Visual Studio Cloud Explorer

Хотя [портал Azure](https://portal.azure.com/) предоставляет отличный способ проектирования приложений логики Azure и управления ими, вы также можете управлять многими ресурсами Azure, включая приложения логики, с помощью Visual Studio, используя Visual Studio Cloud Explorer. Вы можете просматривать опубликованные приложения логики и выполнять другие задачи, например включать и отключать приложения, а также изменять и просматривать журналы выполнения. 

## <a name="installation-steps"></a>Шаги установки

Ниже описаны действия по установке и настройке средств Visual Studio для Azure Logic Apps.

### <a name="prerequisites"></a>Предварительные требования

* [Visual Studio 2015 или Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* [пакет Azure SDK последней версии](https://azure.microsoft.com/downloads/) (версия&2;.9.1 или выше);
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* доступ к Интернету при использовании внедренного конструктора.

### <a name="install-visual-studio-tools-for-logic-apps"></a>Установка инструментов Visual Studio для приложений логики

После установки необходимых компонентов выполните следующие действия.

1. Откройте Visual Studio. Откройте меню **Средства** и выберите пункт **Расширения и обновления**.
2. Разверните категорию **В сети**, чтобы выполнить поиск в Интернете.
3. Найдите **средства Azure Logic Apps Tools для Visual Studio**. Если потребуется, выполните поиск по названию **Logic Apps**.
4. Нажмите кнопку **Скачать**, чтобы скачать и установить это расширение.
5. После установки перезапустите Visual Studio.

> [!NOTE]
> Также средства Azure Logic Apps для Visual Studio можно скачать непосредственно в [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9).

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>Поиск приложений логики в Cloud Explorer

1.    Чтобы открыть Cloud Explorer, в меню **Вид** выберите **Cloud Explorer**.
2.    Найдите приложение логики, используя поиск по группе ресурсов или типу ресурса. 

    Просматривая по типу ресурса, выберите подписку Azure, разверните раздел "Приложения логики" и выберите необходимое приложение. 
    Щелкните одно из приложений логики правой кнопкой мыши или воспользуйтесь меню **Действия** в нижней части Cloud Explorer.

    ![Поиск приложения логики](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-app-designer"></a>Изменение приложения логики с помощью конструктора приложений логики

Чтобы открыть развернутое приложение логики в том же конструкторе, что и на портале Azure, щелкните свое приложение логики правой кнопкой мыши и выберите команду **Открыть в редакторе приложений логики**. 

В конструкторе можно изменить приложение логики, сохранить изменения в облаке и запустить новое выполнение с помощью кнопки **Запустить триггер**.

![Конструктор приложений логики](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-your-logic-app-run-history"></a>Просмотр журнала выполнения приложения логики

Чтобы просмотреть журнал выполнения приложения логики, щелкните это приложение правой кнопкой мыши и выберите команду **Открыть журнал запусков**. Чтобы упорядочить журнал выполнения по одному из отображаемых свойств, выберите соответствующий заголовок столбца.

![Журнал выполнения](media/logic-apps-manage-from-vs/runs.png)

Дважды щелкните один из экземпляров выполнения, чтобы отобразился журнал выполнения для этого экземпляра, где можно просмотреть результаты выполнения, включая входные и выходные данные каждого шага.

![Результаты в журнале выполнения, входные и выходные данные по шагам](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Дальнейшие действия

*    Если вы еще не знакомы с Azure Logic Apps, то см. статью [Создание нового приложения логики, подключающего службы SaaS](logic-apps-create-a-logic-app.md).
* [Создание и развертывание приложений логики Azure в Visual Studio](logic-apps-deploy-from-vs.md).
* [Примеры приложений логики и распространенные сценарии](logic-apps-examples-and-scenarios.md)
* Узнайте, как можно [автоматизировать бизнес-процессы с помощью Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694).
* Изучите вопросы [интеграции систем с Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462).

