---
title: "Настройка учетной записи запуска от имени | Документация Майкрософт"
description: "В этом руководстве приводятся пошаговые инструкции по созданию и тестированию, а также пример проверки подлинности с помощью субъекта безопасности в службе автоматизации Azure."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
keywords: "имя субъекта-службы, SetSPN, проверка подлинности Azure"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/06/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: fbca3d195290551d37606e231b997a40a602351f
ms.lasthandoff: 03/29/2017


---

# <a name="authenticate-runbooks-with-an-azure-run-as-account"></a>Проверка подлинности модулей Runbook в Azure с помощью учетной записи запуска от имени
В этой статье объясняется, как настроить учетную запись службы автоматизации на портале Azure с использованием учетной записи запуска от имени, чтобы проверять подлинность ресурсов управления модулями runbook в Azure Resource Manager или в службе управления службами Azure.

При создании учетной записи службы автоматизации на портале Azure автоматически создаются две приведенные ниже учетные записи.

* Учетная запись запуска от имени. Эта учетная запись создает субъект-службу в Azure Active Directory (Azure AD) и сертификат, а также назначает управление доступом на основе ролей участника, используемое для управления ресурсами Resource Manager с помощью модулей runbook.
* Классическая учетная запись запуска от имени. Эта учетная запись отправляет сертификат управления, используемый для администрирования службы управления службами или классических ресурсов с помощью модулей runbook.

Создание учетной записи службы автоматизации упрощает процесс и помогает быстро приступить к созданию и развертыванию модулей runbook для решения конкретных задач автоматизации.

Используя учетную запись запуска от имени и классическую учетную запись, вы можете:

* внедрить стандартную процедуру проверки подлинности в Azure при администрировании ресурсов Resource Manager и ресурсов службы управления службами с помощью модулей runbook на портале Azure;
* автоматизировать использование глобальных модулей runbook, которые можно настроить в службе оповещений Azure.

> [!NOTE]
> Для работы [функции интеграции оповещений](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) Azure с глобальными модулями runbook службы автоматизации требуется учетная запись службы автоматизации, в которой настроены учетная запись запуска от имени и классическая учетная запись запуска от имени. Вы можете выбрать имеющуюся учетную запись службы автоматизации, в которой уже определены учетная запись запуска от имени и классическая учетная запись запуска от имени, или создать новую.
>  

В этой статье описано, как создать учетную запись службы автоматизации на портале Azure, обновить ее с помощью Azure PowerShell, а также как управлять конфигурацией учетной записи и проходить проверку подлинности в модулях runbook.

Прежде чем приступать к созданию учетной записи службы автоматизации, следует принять во внимание несколько моментов.

* Создание этой учетные записи не повлияет на имеющиеся учетные записи службы автоматизации, созданные в классической модели развертывания или в модели развертывания с помощью Resource Manager.
* Этот процесс работает только для учетных записей службы автоматизации, созданных на портале Azure. При попытке создать учетную запись на классическом портале Azure конфигурация учетной записи запуска от имени не будет реплицирована.
* Если вы уже создали модули runbook и ресурсы (например, расписания или переменные) для управления классическими ресурсами и хотите выполнять проверку подлинности в этих модулях runbook с помощью классической учетной записи запуска от имени, выполните одно из следующих действий:

  * Создайте классическую учетную запись запуска от имени, используя инструкции в разделе "Управление учетной записью запуска от имени". 
  * Обновите имеющуюся учетную запись с помощью сценария PowerShell, приведенного в разделе "Обновление учетной записи службы автоматизации с помощью PowerShell".
* Чтобы выполнять проверку подлинности с помощью новой учетной записи запуска от имени и классической учетной записи запуска от имени (учетная запись службы автоматизации), измените имеющиеся модули runbook с помощью примера кода, приведенного в разделе с [примерами кода для проверки подлинности](#authentication-code-examples).

    >[!NOTE]
    >Учетная запись запуска от имени используется для проверки подлинности в ресурсах Resource Manager с помощью субъекта-службы на основе сертификата, а классическая учетная запись запуска от имени — в ресурсах управления службами с помощью сертификата управления.

## <a name="create-an-automation-account-from-the-azure-portal"></a>Создание учетной записи службы автоматизации на портале Azure
В этом разделе описано, как создать учетную запись службы автоматизации Azure на портале Azure, в результате чего создаются учетная запись запуска от имени и классическая учетная запись запуска от имени.

>[!NOTE]
>Чтобы создать учетную запись службы автоматизации, пользователь должен быть участником роли "Администраторы служб" или соадминистратором подписки, из которой предоставляется доступ к подписке. Кроме того, пользователь также должен быть добавлен в роль "Пользователь" в стандартных подписках Active Directory. Учетной записи не нужно назначать привилегированную роль.
>
>Если пользователь, которого добавляют к роли соадминистратора подписки, не является участником подписки Active Directory, он будет добавлен в Active Directory в качестве гостя. В этом случае отобразится соответствующее предупреждение "У вас нет разрешений на создание..." в колонке **Добавление учетной записи службы автоматизации**.
>
>Пользователей, получивших роль соадминистратора, можно удалить из подписки Active Directory, а затем повторно добавить, чтобы предоставить им права полного доступа к Active Directory. Чтобы проверить это, на портале Azure в области **Azure Active Directory** выберите **Пользователи и группы**, а затем — **Все пользователи**. Выбрав конкретного пользователя, щелкните **Профиль**. Значение атрибута **Тип пользователя** в профиле пользователя не должно соответствовать значению **Гость**.
>

1. Войдите на портал Azure с помощью учетной записи, добавленной в качестве участника роли "Администраторы подписки" и соадминистратора подписки.

2. Выберите элемент **Учетные записи службы автоматизации**.

3. В колонке **Учетные записи службы автоматизации** щелкните **Добавить**.
После этого откроется колонка **Добавление учетной записи службы автоматизации**.

 ![Колонка "Добавление учетной записи службы автоматизации"](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

   > [!NOTE]
   > Если учетная запись не является участником роли "Администраторы подписки" и соадминистратором подписки, в колонке **Добавление учетной записи службы автоматизации** отобразится следующее предупреждение:
   >
   >![Предупреждение в колонке "Добавление учетной записи службы автоматизации"](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   >
   >

4. В колонке **Добавление учетной записи службы автоматизации** в поле **Имя** введите имя новой учетной записи службы автоматизации.

5. Если у вас имеется несколько подписок, выполните следующее:

    А. В разделе **Подписка** укажите одну из них для новой учетной записи.

    b. В разделе **Группа ресурсов** щелкните **Создать** или **Использовать существующую**.

    c. В разделе **Расположение** выберите расположение центра обработки данных Azure.

6. В разделе **Создать учетную запись запуска от имени Azure** выберите **Да** и нажмите кнопку **Создать**.

   > [!NOTE]
   > Если вы решили не создавать учетную запись запуска от имени и выбрали значение **Нет**, в колонке **Добавление учетной записи службы автоматизации** появится предупреждение. Хотя учетная запись и создается на портале Azure, она не будет содержать соответствующее удостоверение проверки подлинности в службе каталогов классической подписки или подписки Resource Manager. Поэтому у этой учетной записи не будет доступа к ресурсам в вашей подписке. В этом случае модули runbook, ссылающиеся на эту учетную запись, не смогут проходить проверку подлинности и выполнять задачи, используя ресурсы в соответствующих моделях развертывания.
   >
   > ![Предупреждение в колонке "Добавление учетной записи службы автоматизации"](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)
   >
   > Кроме того, если субъект-служба не создан, роль участника не назначается.
   >

7. Ход создания учетной записи службы автоматизации в Azure можно отслеживать в разделе **Уведомления** в меню.

### <a name="resources"></a>Ресурсы
После создания учетной записи службы автоматизации автоматически создаются несколько ресурсов. Ниже представлена таблица, в которой указаны эти ресурсы.

#### <a name="run-as-account-resources"></a>Ресурсы учетной записи запуска от имени

| Ресурс | Описание |
| --- | --- |
| Модуль Runbook AzureAutomationTutorial | Пример графического модуля runbook, который демонстрирует, как выполнить проверку подлинности с помощью учетной записи запуска от имени, и получает доступ ко всем ресурсам Resource Manager. |
| Модуль Runbook AzureAutomationTutorialScript | Пример модуля runbook PowerShell, который демонстрирует, как выполнить проверку подлинности с помощью учетной записи запуска от имени, и получает доступ ко всем ресурсам Resource Manager. |
| AzureRunAsCertificate | Ресурс сертификатов, автоматически создаваемый во время создания учетной записи службы автоматизации или с помощью приведенного ниже сценария PowerShell для имеющейся учетной записи. Этот сертификат позволяет пройти проверку подлинности в Azure, что дает возможность управлять ресурсами Azure Resource Manager с помощью модулей runbook. Срок действия этого сертификата — один год. |
| AzureRunAsConnection | Ресурс подключений, автоматически создаваемый во время создания учетной записи службы автоматизации или с помощью сценария PowerShell для имеющейся учетной записи. |

#### <a name="classic-run-as-account-resources"></a>Ресурсы классической учетной записи запуска от имени

| Ресурс | Описание |
| --- | --- |
| Модуль Runbook AzureClassicAutomationTutorial | Пример графического модуля runbook, который получает доступ ко всем виртуальным машинам, созданным с использованием классической модели развертывания, в подписке с помощью классической учетной записи запуска от имени (сертификат), а затем записывает имя и состояние виртуальной машины. |
| Модуль Runbook AzureClassicAutomationTutorialScript | Пример модуля runbook PowerShell, который получает доступ ко всем классическим виртуальным машинам в подписке с помощью классической учетной записи запуска от имени (сертификат), а затем записывает имя и состояние виртуальной машины. |
| AzureClassicRunAsCertificate | Автоматически созданный ресурс сертификатов, используемый для проверки подлинности в Azure, что позволяет управлять классическими ресурсами Azure с помощью модулей runbook. Срок действия этого сертификата — один год. |
| AzureClassicRunAsConnection | Автоматически созданный ресурс подключений, используемый для проверки подлинности в Azure, что позволяет управлять классическими ресурсами Azure с помощью модулей runbook. |

## <a name="verify-run-as-authentication"></a>Тестирование проверки подлинности с помощью учетной записи запуска от имени
Выполним небольшой тест, чтобы убедиться, что вы сможете успешно пройти проверку подлинности с помощью новой учетной записи запуска от имени.

1. На портале Azure откройте учетную запись службы автоматизации, созданную ранее.

2. Щелкните элемент **Модули Runbook** , чтобы открыть список модулей Runbook.

3. Выберите модуль runbook **AzureAutomationTutorialScript** и нажмите кнопку **Запустить**, чтобы запустить его. После этого происходят следующие события:
 * Создается [задание runbook](automation-runbook-execution.md), открывается колонка **Задание**, а на плитке **Сводка по заданию** отображается состояние задания.
 * Сначала задание получает состояние **В очереди**, указывающее на то, что задание ожидает, пока рабочая роль runbook в облаке станет доступной.
 * Как только рабочая роль затребует задание, оно получит состояние **Запущено**.
 * С началом фактического выполнения модуля runbook состояние изменится на **Выполняется**.
 * После выполнения задания runbook состояние должно измениться на **Выполнено**.

       ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)
4. Чтобы просмотреть подробные результаты задания runbook, щелкните плитку **Выходные данные**.  
После этого откроется колонка **Выходные данные** со сведениями о том, что модуль runbook прошел проверку подлинности и вернул список всех ресурсов, доступных в группе ресурсов.

5. Закройте колонку **Выходные данные**, чтобы вернуться к колонке **Сводка по заданию**.

6. Закройте колонку **Сводка по заданию** и соответствующую колонку модуля runbook **AzureAutomationTutorialScript**.

## <a name="verify-classic-run-as-authentication"></a>Тестирование проверки подлинности классической учетной записи запуска от имени
Выполним небольшой похожий тест, чтобы убедиться, что вы сможете успешно пройти проверку подлинности с помощью новой классической учетной записи запуска от имени.

1. На портале Azure откройте учетную запись службы автоматизации, созданную ранее.

2. Щелкните элемент **Модули Runbook** , чтобы открыть список модулей Runbook.

3. Выберите модуль Runbook **AzureClassicAutomationTutorialScript** и нажмите кнопку **Запустить**, чтобы запустить его. После этого происходят следующие события:

 * Создается [задание runbook](automation-runbook-execution.md), открывается колонка **Задание**, а на плитке **Сводка по заданию** отображается состояние задания.
 * Сначала задание получает состояние **В очереди**, указывающее на то, что задание ожидает, пока рабочая роль runbook в облаке станет доступной.
 * Как только рабочая роль затребует задание, оно получит состояние **Запущено**.
 * С началом фактического выполнения модуля runbook состояние изменится на **Выполняется**.
 * После выполнения задания runbook состояние должно измениться на **Выполнено**.

    ![Тестирование модуля Runbook субъекта безопасности](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
4. Чтобы просмотреть подробные результаты задания runbook, щелкните плитку **Выходные данные**.  
После этого откроется колонка **Выходные данные** со сведениями о том, что модуль runbook прошел проверку подлинности и вернул список всех классических виртуальных машин в подписке.

5. Закройте колонку **Выходные данные**, чтобы вернуться к колонке **Сводка по заданию**.

6. Закройте колонку **Сводка по заданию** и соответствующую колонку модуля runbook **AzureAutomationTutorialScript**.

## <a name="managing-your-run-as-account"></a>Управление учетной записью запуска от имени
В определенный момент перед истечением срока действия учетной записи службы автоматизации вам потребуется обновить сертификат. Если вы считаете, что учетная запись запуска от имени была скомпрометирована, ее можно удалить и создать заново. В этом разделе описано, как выполнить эти операции.

### <a name="self-signed-certificate-renewal"></a>Обновление самозаверяющего сертификата
Срок действия самозаверяющего сертификата, созданного для учетной записи запуска от имени, составляет один год с момента создания. Его можно обновить в любое время до истечения его срока действия. При обновлении текущее значение сертификата сохраняется, чтобы гарантировать, что все модули runbook, которые поставлены в очередь или активно выполняются и для проверки подлинности которых используется учетная запись запуска от имени, не будут затронуты. Сертификат будет существовать до окончания срока действия.

> [!NOTE]
> Если вы настроили учетную запись запуска от имени службы автоматизации так, чтобы использовать сертификат, выданный центром сертификации предприятия, и используете этот параметр, то этот сертификат предприятия будет заменен самозаверяющим сертификатом.

Чтобы обновить сертификат, сделайте следующее:

1. На портале Azure откройте учетную запись службы автоматизации.

2. В колонке **Учетная запись службы автоматизации** в области **свойств учетной записи** выберите **Учетные записи запуска от имени** в разделе **Параметры учетной записи**.

    ![Область свойств учетной записи службы автоматизации](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)
3. В колонке свойств **Учетные записи запуска от имени** выберите учетную запись запуска от имени или классическую учетную запись запуска от имени, для которой нужно обновить сертификат.

4. В колонке **Свойства** выбранной учетной записи щелкните **Обновление сертификата**.

    ![Обновление сертификата для учетной записи запуска от имени](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)

5. Ход обновления сертификата можно отслеживать в разделе **Уведомления** в меню.

### <a name="delete-a-run-as-or-classic-run-as-account"></a>Удаление учетной записи запуска от имени или классической учетной записи запуска от имени
В этом разделе описано, как удалить и повторно создать учетную запись запуска от имени или классическую учетную запись запуска от имени. При выполнении этого действия учетная запись службы автоматизации сохраняется. После удаления учетной записи запуска от имени или классической учетной записи запуска от имени ее можно создать заново на портале Azure.

1. На портале Azure откройте учетную запись службы автоматизации.

2. В колонке **Учетная запись службы автоматизации** в области свойств учетной записи выберите **Учетные записи запуска от имени**.

3. В колонке свойств **Учетные записи запуска от имени** выберите учетную запись запуска от имени или классическую учетную запись запуска от имени, которую нужно удалить. Затем в колонке **Свойства** выбранной учетной записи щелкните **Удалить**.

 ![Удаление учетной записи запуска от имени](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)

4. Ход удаления учетной записи можно отслеживать в разделе **Уведомления** в меню.

5. После удаления учетной записи ее можно повторно создать в колонке свойств **Учетные записи запуска от имени**, выбрав параметр создания **Учетная запись запуска от имени Azure**.

 ![Повторное создание учетной записи запуска от имени службы автоматизации](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)

### <a name="misconfiguration"></a>Неправильные настройки
Во время начальной настройки некоторые из элементов конфигурации, необходимых для правильной работы учетной записи запуска от имени или классической учетной записи запуска от имени, могут быть удалены или неправильно созданы. К этим элементам относятся:

* ресурс сертификата,
* ресурс подключения,
* учетная запись запуска от имени (удалена из роли участника),
* субъект-служба или приложение-служба в Azure AD,

В случае такой неправильной настройки (или в других примерах) учетная запись службы автоматизации обнаружит эти изменения и отобразит в колонке свойств **Учетные записи запуска от имени** состояние *Не завершено*.

![Сообщение о том, что настройка учетной записи запуска от имени не завершена](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)

При выборе учетной записи запуска от имени в области **Свойства** учетной записи отобразится следующее сообщение об ошибке:

![Предупреждение о том, что настройка учетной записи запуска от имени не завершена](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png).

Эти проблемы с учетной записью запуска от имени можно быстро устранить, удалив и повторно создав ее.

## <a name="update-your-automation-account-by-using-powershell"></a>Обновление учетной записи службы автоматизации с помощью PowerShell
Имеющуюся учетную запись службы автоматизации можно обновлять с помощью PowerShell в следующих случаях:

* Вы создали учетную запись службы автоматизации, но не создали учетную запись запуска от имени.
* У вас уже есть учетная запись службы автоматизации для управления ресурсами Resource Manager, и вы хотите обновить ее, чтобы включить учетную запись запуска от имени в процедуру проверки подлинности модуля runbook.
* У вас есть учетная запись службы автоматизации для управления классическими ресурсами, и вы хотите обновить ее, чтобы использовать классическую учетную запись запуска от имени, а не создавать учетную запись и переносить в нее модули runbook и ресурсы.   
* Вы хотите создать учетную запись запуска от имени и классическую учетную запись запуска от имени, используя сертификат, выданный центром сертификации предприятия.

Предварительные требования для выполнения этого сценария:

* Этот сценарий можно выполнять только в ОС Windows 10 и Windows Server 2016 с модулями Azure Resource Manager 2.01 или более поздней версии. Более ранние версии Windows не поддерживаются.
* Azure PowerShell 1.0 или более поздней версии. Сведения о выпуске PowerShell 1.0 см. в статье [Приступая к работе с командлетами Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Учетная запись службы автоматизации, указанная как значение для параметров *-AutomationAccountName* и *-ApplicationDisplayName* в приведенных ниже сценариях PowerShell.

Чтобы получить значения для параметров *SubscriptionID*, *ResourceGroup* и *AutomationAccountName*, которые являются обязательными для сценариев, сделайте следующее:
1. На портале Azure выберите свою учетную запись службы автоматизации в колонке **Учетная запись службы автоматизации** и выберите **Все параметры**. 
2. В колонке **Все параметры** в разделе **Параметры учетной записи** выберите **Свойства**. 
3. Обратите внимание на значения в колонке **Свойства**.

![Колонка "Свойства" учетной записи службы автоматизации](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-a-run-as-account-powershell-script"></a>Создание сценария PowerShell для учетной записи запуска от имени
Этот сценарий PowerShell включает в себя поддержку следующих конфигураций:

* создание учетной записи запуска от имени с использованием самозаверяющего сертификата;
* создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата;
* создание учетной записи запуска от имени и классической учетной записи запуска от имени Azure с использованием корпоративного сертификата;
* создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата в облаке Azure для государственных организаций.

В зависимости от выбранного варианта конфигурации сценарий создает приведенные ниже элементы.

**Для учетной записи запуска от имени:**

* Приложение Azure AD, используемое для экспорта самозаверяющего сертификата или открытого ключа корпоративного сертификата, и учетную запись субъекта-службы для этого приложения в Azure AD, а также назначает роль участника в текущей подписке. Вместо этой роли можно использовать роль владельца или любую другую роль. Дополнительные сведения см. в статье [Управление доступом на основе ролей в службе автоматизации Azure](automation-role-based-access-control.md).
* Ресурс сертификатов службы автоматизации с именем *AzureRunAsCertificate* в указанной учетной записи службы автоматизации. Этот ресурс содержит закрытый ключ сертификата, используемый в приложении Azure AD.
* Ресурс подключений службы автоматизации с именем *AzureRunAsConnection* в указанной учетной записи службы автоматизации. Этот ресурс содержит идентификаторы приложения, клиента и подписки, а также отпечаток сертификата.

**Для классической учетной записи запуска от имени Azure:**

* Ресурс сертификатов службы автоматизации с именем *AzureClassicRunAsCertificate* в указанной учетной записи службы автоматизации. Этот ресурс содержит закрытый ключ сертификата, используемый в сертификате управления.
* Ресурс подключений службы автоматизации с именем *AzureClassicRunAsConnection* в указанной учетной записи службы автоматизации. Этот ресурс содержит имя подписки, идентификатор подписки и имя ресурса сертификатов.

>[!NOTE]
> Если вы выбрали создание классической учетной записи запуска от имени, после выполнения сценария отправьте открытый сертификат (файл в формате CER) в хранилище управления подписки, в которой создана учетная запись службы автоматизации.
> 

Чтобы выполнить сценарий и отправить сертификат, сделайте следующее:

1. Сохраните приведенный ниже сценарий на компьютере. В этом примере используйте имя файла *New-RunAsAccount.ps1*.

        #Requires -RunAsAdministrator
         Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword,
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.EndDate = $KeyCredential.EndDate.AddDays(-1)
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use key credentials and create an Azure AD application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
           Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
           return
        }

        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create a Run As account by using a service principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"

        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
          $CertificateName = $AutomationAccountName+$CertifcateAssetName
          $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
          $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
          $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create a service principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create a Run As account by using a service principal
            $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
            $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
            $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
            $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#"

             if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
             $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
             $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
             $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        } else {
             $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
             $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
             $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
             $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
             $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. На компьютере откройте меню **Пуск** и запустите с повышенными правами **Windows PowerShell**.

3. Из оболочки командной строки PowerShell с повышенными привилегиями перейдите в папку, которая содержит сценарий, созданный на этапе 1.

4. Выполните этот сценарий, установив значения параметров в зависимости от требуемой конфигурации.

    **Создание учетной записи запуска от имени с использованием самозаверяющего сертификата**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Создание учетной записи запуска от имени и классической учетной записи запуска от имени Azure с использованием корпоративного сертификата**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата в облаке Azure для государственных организаций**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > После выполнения сценария появится запрос на проверку подлинности в Azure. Войдите в систему, используя учетную запись, которая является участником роли "Администраторы подписки" и соадминистратором подписки.
    >
    >

После выполнения сценария обратите внимание на следующее.
* Если вы создали классическую учетную запись запуска от имени с использованием самозаверяющего открытого сертификата (CER-файл), сценарий создает ее и сохраняет в папке временных файлов на компьютере в профиле пользователя, который выполнял сеанс PowerShell: *%Профиль_пользователя%\AppData\Local\Temp*.
* Если вы создали классическую учетную запись запуска от имени с использованием открытого корпоративного сертификата (CER-файл), используйте этот сертификат. Следуя инструкциям, [отправьте сертификат управления API на классический портал Azure](../azure-api-management-certs.md), а затем используйте [пример кода для проверки подлинности](#sample-code-to-authenticate-with-service-management-resources), чтобы проверить конфигурацию учетных данных с помощью ресурсов управления службами. 
* Если вы *не* создали классическую учетную запись запуска от имени, выполните проверку подлинности с помощью ресурсов Resource Manager и проверьте конфигурацию учетных данных, используя [этот пример кода](#sample-code-to-authenticate-with-resource-manager-resources).

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Пример кода для проверки подлинности с помощью ресурсов Resource Manager
Чтобы выполнить проверку подлинности с помощью учетной записи запуска от имени для управления ресурсами Resource Manager, используя модули runbook, используйте обновленный пример кода, приведенный ниже, взятый из примера модуля runbook *AzureAutomationTutorialScript*.

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Signing in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
       "Setting context to a specific subscription"     
       Set-AzureRmContext -SubscriptionId $SubId              
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    }

Этот сценарий содержит две дополнительные строки кода, что позволяет ссылаться на контекст подписки и без проблем работать с несколькими подписками. Ресурс переменных *SubscriptionId* содержит идентификатор подписки. После инструкции командлета `Add-AzureRmAccount` командлет [`Set-AzureRmContext`](https://msdn.microsoft.com/library/mt619263.aspx) указывается с набором параметров *-SubscriptionId*. Если имя переменной слишком общее, вы можете изменить это имя, включив в него префикс или другое соглашение об именовании, чтобы упростить идентификацию. Кроме того, вы также можете использовать набор параметров *-SubscriptionName* вместо *-SubscriptionId* с соответствующим ресурсом переменных.

Командлет `Add-AzureRmAccount`, применяемый для проверки подлинности в модуле runbook, использует набор параметров *ServicePrincipalCertificate*. Он выполняет проверку подлинности с помощью сертификата субъекта-службы, а не учетных данных пользователя.

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Пример кода для проверки подлинности с помощью ресурсов управления службами
Чтобы выполнить проверку подлинности с помощью классической учетной записи запуска от имени для управления классическими ресурсами с помощью модулей runbook, используйте обновленный пример кода из примера модуля runbook *AzureClassicAutomationTutorialScript*, приведенный ниже.

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID

## <a name="next-steps"></a>Дальнейшие действия
* [Объекты приложения и субъекта-службы в Azure Active Directory](../active-directory/active-directory-application-objects.md)
* [Управление доступом на основе ролей в службе автоматизации Azure](automation-role-based-access-control.md)
* [Общие сведения о сертификатах для облачных служб Azure](../cloud-services/cloud-services-certs-create.md)

