---
title: "Управление Azure Logic Apps из Visual Studio Cloud Explorer | Документация Майкрософт"
description: "Управление Azure Logic Apps из Visual Studio Cloud Explorer."
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
ms.date: 12/19/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: a9f786259676a1bc9aa616f2db2935dff45475e2
ms.openlocfilehash: dcb0512b4f9e6c94262a3f39ef59f82fcfbf313e


---
# <a name="manage-logic-apps-with-the-visual-studio-cloud-explorer"></a>Управление Logic Apps с помощью Visual Studio Cloud Explorer
Хотя [портал Azure](https://portal.azure.com) предоставляет отличный способ проектирования приложений логики и управления ими, вы также можете управлять многими ресурсами Azure, включая Logic Apps, из Visual Studio.  С помощью Cloud Explorer можно просматривать опубликованные приложения логики, выполнять другие действия, например включать, отключать, изменять и просматривать журналы выполнения. 

## <a name="installation-steps"></a>Шаги установки
Ниже описаны действия по установке и настройке инструментов Visual Studio для приложений логики.

### <a name="prerequisites"></a>Предварительные требования
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [пакет Azure SDK последней версии](https://azure.microsoft.com/downloads/) (версия&2;.9.1 или выше);
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* доступ к Интернету при использовании внедренного конструктора.

### <a name="install-visual-studio-tools-for-logic-apps"></a>Установка инструментов Visual Studio для приложений логики
Убедитесь, что выполнены предварительные требования, а затем выполните следующее: 

1. В Visual Studio 2015 откройте меню **Сервис** и выберите пункт **Расширения и обновления**.
2. Чтобы выполнить поиск в Интернете, выберите категорию **В сети** .
3. Выполните поиск по запросу **Logic Apps** (Приложения логики), чтобы отобразить **Azure Logic Apps для Visual Studio** (Приложения логики Azure для Visual Studio).
4. Нажмите кнопку **Скачать** , чтобы скачать и установить расширение.
5. После установки перезапустите Visual Studio.

> [!NOTE]
> Расширения также можно скачать непосредственно по [этой ссылке](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)
> 
> 

## <a name="browsing-logic-apps"></a>Просмотр приложений логики
Чтобы просмотреть приложения логики в Cloud Explorer, откройте этот инструмент из меню "Вид" > Cloud Explorer. Теперь можно выбрать просмотр по группе или типу ресурсов.  Просматривая по типу ресурсов, выберите подписку и разверните раздел "Приложения логики", а затем выберите приложение.  Щелкните правой кнопкой мыши одно из приложений логики или воспользуйтесь меню "Действия" в нижней части Cloud Explorer, чтобы выполнить требуемое действие.

![Обзор](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-logic-app-with-the-designer"></a>Изменение приложения логики с помощью конструктора
Чтобы открыть развернутое приложение логики в том же конструкторе, что и на портале Azure, щелкните приложение логики правой кнопкой мыши и выберите команду "Открыть в редакторе приложений логики".  С помощью конструктора можно изменить приложение логики и сохранить его в облаке, а также запустить новое выполнение с помощью кнопки "Запустить триггер".

![Конструктор](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-logic-app-run-history"></a>Просмотр журнала выполнения приложения логики
Чтобы отобразить журнал выполнения приложения логики, щелкните это приложение правой кнопкой мыши и выберите команду "Открыть журнал запусков".  В этом представлении можно упорядочить записи по любому отображаемому свойству, выбрав соответствующий заголовок столбца.  

![Запуски](media/logic-apps-manage-from-vs/runs.png)

При двойном щелчке экземпляра выполнения отображается журнал выполнения для этого экземпляра, где можно просмотреть результаты выполнения, включая входные и выходные данные каждого шага.

![Журнал](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Дальнейшие действия
* Чтобы начать работу с приложениями логики, выполните задания из учебника [Создание приложения логики](logic-apps-create-a-logic-app.md) .  
* [Примеры приложений логики и распространенные сценарии](logic-apps-examples-and-scenarios.md)
* [Вы можете автоматизировать бизнес-процессы с помощью приложений логики](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Узнайте, как интегрировать системы с приложениями логики](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Jan17_HO3-->


