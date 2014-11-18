<properties urlDisplayName="Get Started with Azure Automation" pageTitle="Начало работы со службой автоматизации Azure" metaKeywords="" description="Узнайте, как импортировать и выполнять задания по автоматизации в Azure." metaCanonical="" services="automation" documentationCenter="" title="Начало работы со службой автоматизации Azure" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bwren" />

# Начало работы со службой автоматизации Azure

Автоматизация Microsoft Azure позволяет разработчикам автоматизировать выполняемые вручную, длительные, подверженные ошибкам и часто повторяющиеся задачи, типичные для выполнения в облачных средах. С помощью модулей Runbook, за которыми скрываются рабочие процессы Windows PowerShell, можно создавать ресурсы в среде Azure, отслеживать их состояние, управлять ими и развертывать их. Дополнительные сведения об автоматизации см. в [руководстве, содержащем обзор автоматизации][руководстве, содержащем обзор автоматизации].

В этом учебнике описывается процедура импорта примера модуля Runbook «Hello World» в службу автоматизации Azure, выполнения этого модуля Runbook и последующего просмотра результата его выполнения.

> [WACOM.NOTE] Чтобы научиться автоматизировать операции Azure с помощью [командлетов Azure PowerShell][командлетов Azure PowerShell], см. раздел [Azure Automation: Authenticating to Azure using Azure Active Directory][Azure Automation: Authenticating to Azure using Azure Active Directory] (Служба автоматизации Azure: аутентификация в Azure с помощью Azure Active Directory).

## Примеры и вспомогательные модули Runbook

Группа, занимающаяся автоматизацией Azure, создала ряд примеров Runbook, которые помогут начать работу с автоматизацией. Эти примеры, в которых рассматриваются основные понятия автоматизации, призваны помочь научиться писать собственные модули Runbook.

Группа, занимающаяся автоматизацией, также создала вспомогательные модули Runbook, которые можно использовать в качестве стандартных блоков для более крупных задач автоматизации.

> [WACOM.NOTE] Рекомендуется создавать небольшие и предусматривающие повторное использование модули Runbook. После знакомства с автоматизацией также настоятельно рекомендуется создать собственные вспомогательные модули Runbook для часто используемых сценариев.

Посмотреть и загрузить примеры и вспомогательные модули Runbook, подготовленные командой ответственных за автоматизацию специалистов, можно в [Центре сценариев][Центре сценариев] или можно импортировать их непосредственно из [Коллекции Runbook][Коллекции Runbook].

## Сообщество, отзывы и предложения по службе автоматизации

Модули Runbook, созданные сообществом и другими рабочими группами корпорации Майкрософт, также публикуются в [Центре сценариев][1] и в [Runbook Gallery][Коллекции Runbook].

**Обратная связь!** Если нужен модуль Runbook для автоматизации или модуль интеграции, отправьте запрос сценария в Центр сценариев. Если у вас есть идея для новой функции автоматизации, поместите ее в раздел [Голос пользователя][Голос пользователя].

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Высокоуровневое описание действий для этого руководства

1.  [Регистрация для предварительной версии службы автоматизации][Регистрация для предварительной версии службы автоматизации]
2.  [Импортировать модули Runbook из Коллекции Runbook][Импортировать модули Runbook из Коллекции Runbook]
3.  [Опубликовать модуль Runbook][Опубликовать модуль Runbook]
4.  [Запустить модуль Runbook][Запустить модуль Runbook]

## <a name="preview"></a>Регистрация для предварительной версии службы автоматизации Azure

Чтобы перейти к использованию службы автоматизации, необходима активная подписка Azure с включенной функцией предварительной версии службы автоматизации.

-   На странице **предварительных версий компонентов** щелкните **Попробовать сейчас**.

    ![Включить предварительную версию][Включить предварительную версию]

## <a name="automationaccount"></a>Создание учетной записи автоматизации

1.  Выполните вход на [портал управления Azure][портал управления Azure].

2.  На портале управления выберите **Создать учетную запись автоматизации**.

    > [WACOM.NOTE] Если учетная запись автоматизации уже создана, можно перейти к шагу 4.

    ![Создание учетной записи][Создание учетной записи]

3.  На странице **Добавление новой учетной записи автоматизации** введите имя учетной записи и установите флажок.

    ![Добавление новой учетной записи][Добавление новой учетной записи]

## <a name="importrunbook"></a>Импорт модулей Runbook из галереи модулей Runbook

1.  На странице **Автоматизация** щелкните только что созданную новую учетную запись.

    ![Новая учетная запись][Новая учетная запись]

2.  Щелкните **МОДУЛИ RUNBOOK**.

    ![Вкладка "Модули Runbook"][Вкладка "Модули Runbook"]

3.  Щелкните **Создать** \> **Модуль Runbook** \> **Из Коллекции**.

    ![Коллекция Runbook][Коллекция Runbook]

4.  Выберите категорию **Учебные**, а затем **"Hello World" для службы автоматизации Azure** . Нажмите кнопку со стрелкой, направленной вправо.

    ![Импортировать модуль Runbook][Импортировать модуль Runbook]

5.  Ознакомьтесь с содержанием модуля Runbook, а затем нажмите кнопку со стрелкой вправо.

    ![Определение модуля Runbook][Определение модуля Runbook]

6.  Ознакомьтесь с информацией о модуле Runbook, а затем нажмите на кнопку флажка.

    ![Информация о модуле Runbook][Информация о модуле Runbook]

## <a name="publishrunbook"></a>Публикация модулей Runbook

1.  После завершения импорта модуля Runbook щелкните **Write-HelloWorld**.

    ![Импортированный модуль Runbook][Импортированный модуль Runbook]

2.  Щелкните **АВТОР**, а затем щелкните **ЧЕРНОВИК**.

    Изменить содержимое модуля Runbook можно в режиме черновика. Для этого модуля Runbook вносить изменения не потребуется.

    ![Автор Черновик][Автор Черновик]

3.  Нажмите кнопку **ОПУБЛИКОВАТЬ**, чтобы пометить модуль Runbook как готовый для использования в производственной среде.

    ![Опубликовать][Опубликовать]

4.  В ответ на предложение сохранить и опубликовать модуль Runbook нажмите кнопку **Да**.

    ![Предложение сохранить и опубликовать][Предложение сохранить и опубликовать]

## <a name="startrunbook"></a>Запуск модулей Runbook

1.  После открытия модуля Runbook **Write-HelloWorld** щелкните **ЗАПУСТИТЬ**.

    ![Опубликовано][Опубликовано]

2.  На странице **Укажите значения параметров Runbook** введите **Имя**, которое будет использоваться в качестве входного параметра для сценария Write-HelloWorld.ps1, и установите флажок.

    ![Параметры модуля Runbook][Параметры модуля Runbook]

3.  Щелкните **ЗАДАНИЯ**, чтобы проверить состояние только что запущенного задания Runbook, и щелкните метку времени в столбце **ЗАПУСК ЗАДАНИЯ** для просмотра информации о заданиях.

    ![Состояние модуля Runbook][Состояние модуля Runbook]

4.  На странице **СВОДКА** можно просмотреть сводку, введенные параметры и результат задания.

    ![Сводка для модуля Runbook][Сводка для модуля Runbook]

# Управление службами Azure из модуля Runbook

В приведенном выше примере показан простой модуль Runbook, который не управляет службами Azure. [Командлетам Azure][командлетов Azure PowerShell] необходима аутентификация в Azure. Вы можете следовать указаниям в разделе [Azure Automation: Authenticating to Azure using Azure Active Directory][Azure Automation: Authenticating to Azure using Azure Active Directory] (Служба автоматизации Azure: аутентификация в Azure с помощью Azure Active Directory), чтобы настроить подписку Azure для управления с помощью службы автоматизации Azure.

# См. также

-   [Обзор автоматизации][Обзор автоматизации]
-   [Руководство по созданию модулей Runbook][Руководство по созданию модулей Runbook]
-   [Форум автоматизации][Форум автоматизации]
-   [Azure Automation: Authenticating to Azure using Azure Active Directory][Azure Automation: Authenticating to Azure using Azure Active Directory] (Служба автоматизации Azure: аутентификация в Azure с помощью Azure Active Directory)

  [руководстве, содержащем обзор автоматизации]: http://go.microsoft.com/fwlink/p/?LinkId=392861
  [командлетов Azure PowerShell]: http://msdn.microsoft.com/ru-ru/library/jj156055.aspx
  [Azure Automation: Authenticating to Azure using Azure Active Directory]: http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/
  [Центре сценариев]: http://go.microsoft.com/fwlink/p/?LinkId=393029
  [Коллекции Runbook]: http://aka.ms/runbookgallery
  [1]: http://go.microsoft.com/fwlink/?LinkID=391681
  [Голос пользователя]: http://feedback.windowsazure.com/forums/34192--general-feedback
  [Регистрация для предварительной версии службы автоматизации]: #automationaccount
  [Импортировать модули Runbook из Коллекции Runbook]: #importrunbook
  [Опубликовать модуль Runbook]: #publishrunbook
  [Запустить модуль Runbook]: #startrunbook
  [Включить предварительную версию]: ./media/automation/automation_00_EnablePreview.png
  [портал управления Azure]: http://manage.windowsazure.com
  [Создание учетной записи]: ./media/automation/automation_01_CreateAccount.png
  [Добавление новой учетной записи]: ./media/automation/automation_02_addnewautoacct.png
  [Новая учетная запись]: ./media/automation/automation_03_NewAutoAcct.png
  [Вкладка "Модули Runbook"]: ./media/automation/automation_04_RunbooksTab.png
  [Коллекция Runbook]: ./media/automation/automation_05_ImportGallery.png
  [Импортировать модуль Runbook]: ./media/automation/automation_06_ImportRunbook.png
  [Определение модуля Runbook]: ./media/automation/automation_07_RunbookDefinition.png
  [Информация о модуле Runbook]: ./media/automation/automation_08_RunbookDetails.png
  [Импортированный модуль Runbook]: ./media/automation/automation_07_ImportedRunbook.png
  [Автор Черновик]: ./media/automation/automation_08_AuthorDraft.png
  [Опубликовать]: ./media/automation/automation_085_Publish.png
  [Предложение сохранить и опубликовать]: ./media/automation/automation_09_SavePubPrompt.png
  [Опубликовано]: ./media/automation/automation_10_PublishStart.png
  [Параметры модуля Runbook]: ./media/automation/automation_11_RunbookParams.png
  [Состояние модуля Runbook]: ./media/automation/automation_12_RunbookStatus.png
  [Сводка для модуля Runbook]: ./media/automation/automation_13_RunbookSummary_callouts.png
  [Обзор автоматизации]: http://go.microsoft.com/fwlink/p/?LinkId=392860
  [Руководство по созданию модулей Runbook]: http://go.microsoft.com/fwlink/p/?LinkID=301740
  [Форум автоматизации]: http://go.microsoft.com/fwlink/p/?LinkId=390561
