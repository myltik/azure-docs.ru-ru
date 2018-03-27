---
title: Автоматизация задач и процессов с помощью Visual Studio and Azure Logic Apps | Документация Майкрософт
description: В этом руководстве показано, как создать рабочие процессы, которые автоматизируют задачи и процессы с помощью Azure Logic Apps в Visual Studio.
author: ecfan
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/15/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 02e19de97654d751dc0cd557791a61a863a9a4e0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
# <a name="quickstart-automate-tasks-and-processes-with-azure-logic-apps---visual-studio"></a>Руководство. Автоматизация задач и процессов с помощью Azure Logic Apps в Visual Studio

С помощью [Azure Logic Apps](../logic-apps/logic-apps-overview.md) можно создать рабочие процессы, которые автоматизируют задачи и процессы для интеграции приложений, данных, систем и служб по предприятиям и организациям. В кратком руководстве показано, как можно разрабатывать и создавать эти рабочие процессы путем создания приложений логики в Visual Studio и развертывания этих приложений в <a href="https://docs.microsoft.com/azure/guides/developer/azure-developer-guide" target="_blank">Azure</a> в облаке. И хотя эти задачи можно выполнять на <a href="https://portal.azure.com" target="_blank">портале Azure</a>, Visual Studio позволяет добавлять приложения логики в систему управления версиями, публиковать различные версии и создавать шаблоны Azure Resource Manager для различных сред развертывания. 

Если вы не знакомы с Azure Logic Apps и хотите ознакомиться с основными понятиями, см. [руководство по созданию приложения логики на портале Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md). Конструктор приложений логики на портале Azure и в Visual Studio работает аналогичным образом. 

Здесь создается то же приложение логики, что и в кратком руководстве портала Azure, но с помощью Visual Studio. Это приложение логики отслеживает RSS-канал веб-сайта и отправляет сообщения электронной почты для каждого нового элемента, опубликованного на сайте. По завершении приложение логики будет выглядеть как этот высокоуровневый рабочий процесс:

![Готовое приложение логики](./media/quickstart-create-logic-apps-with-visual-studio/overview.png)

<a name="prerequisites"></a>

Прежде чем начать, убедитесь, что у вас есть эти элементы:

* Если у вас еще нет подписки Azure, <a href="https://azure.microsoft.com/free/" target="_blank">зарегистрируйтесь для получения бесплатной учетной записи Azure</a>.

* Скачайте и установите эти средства, если вы еще этого не сделали: 

  * <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2017 или Visual Studio 2015 — выпуски Community или выше</a>. 
  В этом кратком руководстве используется бесплатная версия Visual Studio Community 2017.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">Пакет SDK Azure (2.9.1 или более поздней версии)</a> и <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">Azure PowerShell</a>.

  * <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551" target="_blank">Средства Azure Logic Apps для Visual Studio 2017</a> или <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio" target="_blank">Visual Studio 2015</a>.
  
    Вы можете скачать и установить средства Azure Logic Apps напрямую из Visual Studio Marketplace или узнать, <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">как установить это расширение из Visual Studio</a>. 
    Убедитесь, что вы перезагрузили Visual Studio после завершения установки.

* Учетная запись, поддерживаемая Logic Apps, например Office 365 Outlook, Outlook.com или Gmail. Сведения о дополнительных поставщиках см. в <a href="https://docs.microsoft.com/connectors/" target="_blank">списке соединителей</a>. Это приложение логики использует Office 365 Outlook. Если вы используете другой поставщик, общие шаги те же, но пользовательский интерфейс может немного отличаться.

* Доступ к Интернету при использовании конструктора приложений логики

  Конструктору требуется подключение к Интернету, чтобы создать ресурсы в Azure и считать свойства и данные из соединителей в приложении логики. 
  Например, если вы используете соединитель Dynamics CRM Online, конструктор проверяет экземпляр CRM и получает информацию для отображения доступных свойств по умолчанию и пользовательских свойств.

## <a name="create-azure-resource-group-project"></a>Создание проекта группы ресурсов Azure

Чтобы начать работу, создайте [проект группа ресурсов Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Узнайте больше о [группах ресурсов Azure и ресурсах](../azure-resource-manager/resource-group-overview.md).

1. Запустите Visual Studio и войдите в учетную запись Azure.

2. В меню **Файл** выберите **Создать** > **Проект**. (Клавиатура: Ctrl+Shift+N)

   ![В меню "Файл" выберите "Создать > Проект".](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

3. В разделе **Установленные** выберите **Visual C#** или **Visual Basic**. Выберите **Облако** > **Группа ресурсов Azure**. Назовите свой проект, например:

   ![Создание проекта группы ресурсов Azure](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

4. Выберите шаблон **Приложение логики**. 

   ![Выбор шаблона Logic App](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   После того, как в Visual Studio будет создан проект, в обозревателе решений откроется ваше решение. 

   ![Обозреватель решений с новым решением приложения логики и файлом развертывания](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

   В решении файл **LogicApp.json** — это не только хранилище определения для приложения логики, но и шаблон Azure Resource Manager, который можно настроить для развертывания.

## <a name="create-blank-logic-app"></a>Создание пустого приложения логики

Создав проект группы ресурсов Azure, создайте и настройте приложение логики, начиная с **шаблона пустого приложения логики**.

1. В обозревателе решений откройте контекстное меню для файла **LogicApp.json**. Выберите **Открыть в конструкторе Logic App**. (Клавиатура: Ctrl+L)

   ![Открытие файла JSON приложения логики с помощью конструктора приложений логики](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

2. В поле **Подписка** выберите подписку Azure, которую вы хотите использовать. В поле **Группа ресурсов** выберите **Создать новый...**. При этом будет создана группа ресурсов Azure. 

   ![Выберите подписку, группу ресурсов и расположение.](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   Visual Studio требуется подписка Azure и группа ресурсов для создания и развертывания ресурсов, связанных с приложением логики и подключениями. 

   | Параметр | Пример значения | ОПИСАНИЕ | 
   | ------- | ------------- | ----------- | 
   | Список профилей пользователя | Contoso <br> jamalhartnett@contoso.com | По умолчанию это учетная запись, используемая для входа | 
   | **Подписка** | Оплата по мере использования <br> (jamalhartnett@contoso.com) | Имя подписки Azure и связанной учетной записи |
   | **Группа ресурсов** | MyLogicApp-RG <br> (Западная часть США) | Группа ресурсов Azure и расположение для хранения и развертывания ресурсов приложения логики | 
   | **Местоположение.** | MyLogicApp-RG2 <br> (Западная часть США) | Другое расположение, если вы не хотите использовать расположение группы ресурсов |
   ||||

3. Открывается конструктор Logic Apps и отображается страница с вводным видео и часто используемыми триггерами. Прокрутите видео и триггеры. В разделе **Шаблоны** выберите **Пустое приложение логики**.

   ![Выбор пункта "Пустое приложение логики"](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Рабочий процесс сборки приложения логики

Затем добавьте [триггер](../logic-apps/logic-apps-overview.md#logic-app-concepts), который срабатывает при появлении нового элемента RSS-канала. Каждое приложение логики должно начинаться с триггера, который срабатывает при удовлетворении определенных критериев. При каждой активации триггера обработчик Logic Apps создает экземпляр приложения логики для выполнения рабочего процесса.

1. В конструкторе приложений логики введите "rss" в поле поиска. Выберите этот триггер: **RSS - When a feed item is published** (RSS — при публикации элемента веб-канала).

   ![Создание приложений логики путем добавления триггера и действий](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

   Триггер теперь будет отображаться в конструкторе:

   ![Триггер RSS появляется в конструкторе приложения логики](./media/quickstart-create-logic-apps-with-visual-studio/rss-trigger-logic-app.png)

2. Чтобы завершить создание приложения логики, следуйте рабочему процессу в [кратком руководстве на портале Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger), а затем вернитесь к этой статье.

   По завершении приложение логики может выглядеть следующим образом: 

   ![Готовое приложение логики](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app.png)

3. Чтобы сохранить приложение логики, сохраните решение Visual Studio. (Клавиатура: Ctrl+S)

Теперь, прежде чем протестировать приложение логики, разверните его в Azure.

## <a name="deploy-logic-app-to-azure"></a>Развертывание приложения логики в Azure

Перед запуском приложения логики разверните приложение из Visual Studio в Azure за несколько шагов.

1. В обозревателе решений в контекстном меню проекта выберите **Развернуть** > **Создать...**. Если отобразится запрос на вход в учетную запись Azure, выполните его.

   ![Создайте развертывание приложения логики](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

2. Для этого развертывания сохраните подписку, группу ресурсов и другие настройки по умолчанию для Azure. Когда вы будете готовы, выберите **Развернуть**. 

   ![Развертывание приложения логики в группу ресурсов Azure](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

3. Если появится диалоговое окно **Изменение параметров**, укажите имя ресурса приложения логики для использования в развертывании, а затем сохраните свои параметры, например:

   ![Указание имени развертывания для приложения логики](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   При запуске развертывания его состояние отобразится в окне **Выходные данные** Visual Studio. 
   Если состояние не отображается, откройте список **Показать вывод из** и выберите свою группу ресурсов Azure.

   ![Вывод информации о состоянии развертывания](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   После развертывания приложение логики будет работать в реальном времени на портале Azure и проверять RSS-канал в зависимости от заданного расписания (каждую минуту). 
   Если в RSS-канале есть новые элементы, приложение логики отправляет сообщение электронной почты для каждого нового элемента. 
   В противном случае приложение логики ожидает до следующего интервала перед повторной проверкой. 

   Например, ниже приведены примеры электронных писем, которые отправляет это приложение логики. 
   Если сообщения электронной почты не приходят, проверьте папку нежелательной почты. 

   ![Outlook отправляет сообщение электронной почты для каждого нового элемента RSS](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

   Технически, когда триггер проверяет веб-канал RSS и находит новые элементы, он срабатывает, и подсистема Logic Apps создает для приложения логики экземпляр рабочего процесса, в котором выполняются действия.
   Если триггер не находит новые элементы, он не срабатывает и "пропускает" создание экземпляра рабочего процесса.

Итак, вы успешно создали и развернули приложение логики с помощью Visual Studio! Чтобы управлять приложением логики и просматривать его журнал выполнения, ознакомьтесь со статьей [Manage logic apps with Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md) (Управление приложениями логики в Visual Studio).

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ненужную группу ресурсов, содержащую приложение логики и связанные ресурсы.

1. Войдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a> с учетной записью, использованной для создания приложения логики. 

2. В главном меню Azure выберите **Группы ресурсов**. Выберите группы ресурсов для своего приложения логики.

3. Выберите **Удалить группу ресурсов**. Введите имя группы ресурсов для подтверждения и нажмите кнопку **Удалить**.

   !["Группы ресурсов > Обзор > Удалить группу ресурсов"](./media/quickstart-create-logic-apps-with-visual-studio/delete-resource-group.png)

4. Удалите решение Visual Studio со своего локального компьютера.

## <a name="get-support"></a>Получение поддержки

* Если у вас возникли вопросы, то посетите <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">форум Azure Logic Apps</a>.
* Отправить идею по поводу возможности или проголосовать за нее вы можете на <a href="http://aka.ms/logicapps-wish" target="_blank">сайте отзывов пользователей Logic Apps</a>.

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы создавали, развертывали и выполняли свое приложение логики с помощью Visual Studio. Дополнительные сведения о выполнении расширенного развертывания для приложений логики и управлении им c помощью Visual Studio см. в этих статьях:

> [!div class="nextstepaction"]
> [Управление приложениями логики в Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)