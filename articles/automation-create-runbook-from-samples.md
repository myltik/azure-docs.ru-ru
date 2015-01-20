<properties urlDisplayName="Get Started with Azure Automation" pageTitle="Начало работы со службой автоматизации Azure" metaKeywords="" description="Узнайте, как импортировать и выполнять задания по автоматизации в Azure." metaCanonical="" services="automation" documentationCenter="" title="Get Started with Azure Automation" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/21/2014" ms.author="bwren" />


# Начало работы со службой автоматизации Azure

Автоматизация Microsoft Azure позволяет разработчикам автоматизировать выполняемые вручную, длительные, подверженные ошибкам и часто повторяющиеся задачи, типичные для выполнения в облачных средах. С помощью модулей Runbook, за которыми скрываются рабочие процессы Windows PowerShell, можно создавать ресурсы в среде Azure, отслеживать их состояние, управлять ими и развертывать их. Дополнительные сведения о службе автоматизации см. в [обзорном руководстве по автоматизации](http://go.microsoft.com/fwlink/p/?LinkId=392861). 

В этом учебнике описывается процедура импорта примера модуля Runbook "Hello World" в службу автоматизации Azure, выполнения этого модуля Runbook и последующего просмотра результата его выполнения.

>[WACOM.NOTE] О том, как автоматизировать операции Azure с помощью [командлета Azure PowerShell](http://msdn.microsoft.com/ru-ru/library/jj156055.aspx), см. в разделе <a href="http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/">Служба автоматизации Azure: проверка подлинности в Azure с помощью Azure Active Directory</a>.

## Примеры и вспомогательные модули Runbook

Группа, занимающаяся автоматизацией Azure, создала ряд примеров Runbook, которые помогут начать работу с автоматизацией.  Эти примеры, в которых рассматриваются основные понятия автоматизации, призваны помочь научиться писать собственные модули Runbook.  

Группа, занимающаяся автоматизацией, также создала вспомогательные модули Runbook, которые можно использовать в качестве стандартных блоков для более крупных задач автоматизации.  

>[WACOM.NOTE] Лучше всего писать маленькие, унифицированные модули Runbook, пригодные для повторного использования. Кроме того, после освоения службы автоматизации настоятельно рекомендуется создать собственные вспомогательные модули для часто используемых операций.  

Можно просмотреть и загрузить образец приложения и вспомогательные модули от разработчиков службы автоматизации в [центре сценариев](http://go.microsoft.com/fwlink/p/?LinkId=393029) или импортировать их напрямую из [коллекции модулей Runbook](http://aka.ms/runbookgallery). 

## Сообщество, отзывы и предложения по службе автоматизации

Модули от других разработчиков и других команд Майкрософт также публикуются в [центре сценариев](http://go.microsoft.com/fwlink/?LinkID=391681) и [коллекции модулей Runbook](http://aka.ms/runbookgallery). 

<strong>Отправляйте нам отзывы!</strong>  Если вы ищете определенное решение, построенное на основе службы автоматизации, или модуль интеграции, разместите запрос в центре сценариев. А в разделе [Отзывы пользователей](http://feedback.windowsazure.com/forums/34192--general-feedback) можно опубликовать предложения по новым функциям службы автоматизации.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Высокоуровневое описание действий для этого руководства

1. [Создание учетной записи службы автоматизации](#automationaccount)
2. [Импорт модуля Runbook из коллекции](#importrunbook)
3. [Публикация модуля Runbook](#publishrunbook)
4. [Запуск модуля Runbook](#startrunbook)


## <a name="automationaccount"></a>Создание учетной записи службы автоматизации

1.	Выполните вход на [портал управления Azure](http://manage.windowsazure.com).

2.	На портале управления нажмите кнопку **Создать учетную запись службы автоматизации**.

	>[WACOM.NOTE] Если учетная запись уже создана, то можно перейти на шаг 4.

	![Create Account](./media/automation/automation_01_CreateAccount.png)

3.	На странице **Добавление новой учетной записи службы автоматизации** введите имя для записи и установите флажок.

	![Add New Account](./media/automation/automation_02_addnewautoacct.png)

## <a name="importrunbook"></a>Импорт модуля Runbook из коллекции
 
4.	На странице **Автоматизация** выберите созданную учетную запись.
 
	![New Account](./media/automation/automation_03_NewAutoAcct.png)

5.	Щелкните **МОДУЛИ RUNBOOK**.

	![Runbooks Tab](./media/automation/automation_04_RunbooksTab.png)
  
6.	Щелкните **Создать** > **Runbook** > **Из коллекции**.

	![Runbook Gallery](./media/automation/automation_05_ImportGallery.png)

7.  Выберите категорию **Учебные**, а затем **Hello World для службы автоматизации Azure**. Нажмите кнопку со стрелкой, направленной вправо.

	![Import Runbook](./media/automation/automation_06_ImportRunbook.png)

8.  Ознакомьтесь с содержанием модуля Runbook, а затем нажмите кнопку со стрелкой вправо.

	![Runbook Definition](./media/automation/automation_07_RunbookDefinition.png)

8.	Ознакомьтесь с информацией о модуле Runbook, а затем нажмите на кнопку флажка.

	![Runbook Details](./media/automation/automation_08_RunbookDetails.png)

## <a name="publishrunbook"></a>Публикация модуля Runbook 

9.	После завершения импорта модуля щелкните **Write-HelloWorld**.

	![Imported Runbook](./media/automation/automation_07_ImportedRunbook.png)

9.	Щелкните **АВТОР**, а затем **ЧЕРНОВИК**.  

	Изменить содержимое модуля Runbook можно в режиме черновика. Для этого модуля Runbook вносить изменения не потребуется.

	![Author Draft](./media/automation/automation_08_AuthorDraft.png)  
 
10.	Нажмите кнопку **ОПУБЛИКОВАТЬ**, чтобы пометить модуль как готовый к использованию в рабочей среде.

	![Publish](./media/automation/automation_085_Publish.png)
   
11.	В ответ на предложение сохранить и опубликовать модуль Runbook нажмите кнопку **Да**.
 
	![Save and Pub prompt](./media/automation/automation_09_SavePubPrompt.png)

## <a name="startrunbook"></a>Запуск модуля Runbook

12.	После открытия модуля Runbook **Write-HelloWorld** щелкните **ЗАПУСТИТЬ**.

	![Published](./media/automation/automation_10_PublishStart.png)
 
13.	На странице **Укажите значения параметров Runbook** введите **Имя**, которое будет использоваться в качестве входного параметра для сценария Write-HelloWorld.ps1, и установите флажок.

	![Runbook Parameters](./media/automation/automation_11_RunbookParams.png)
  
14.	Щелкните **ЗАДАНИЯ**, чтобы проверить состояние только что запущенного задания Runbook, а затем щелкните метку времени в столбце **ЗАПУСК ЗАДАНИЯ**, чтобы просмотреть сведения о задании.

	![Runbook Status](./media/automation/automation_12_RunbookStatus.png)

15.	На странице **СВОДКА** можно просмотреть описание, введенные параметры и результат выполнения задания.
 
	![Runbook Summary](./media/automation/automation_13_RunbookSummary_callouts.png)


# Управление службами Azure из модуля Runbook 
В приведенном выше примере показан простой модуль Runbook, который не управляет службами Azure. [Командлеты Azure](http://msdn.microsoft.com/ru-ru/library/jj156055.aspx) должны проходить проверку подлинности в Azure. Вы можете следовать указаниям в разделе [Azure Automation: Проверка подлинности в Azure с помощью Azure Active Directory](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/) при настройке подписки Azure на управление через службы автоматизации Azure.

# См. также

- [Обзор служб автоматизации](http://go.microsoft.com/fwlink/p/?LinkId=392860)
- [Руководство по созданию модулей Runbook](http://go.microsoft.com/fwlink/p/?LinkID=301740)
- [Форум по службам автоматизации](http://go.microsoft.com/fwlink/p/?LinkId=390561)
- [Автоматизация Azure: проверка подлинности в Azure с помощью Azure Active Directory](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)

<!--HONumber=35.2-->
