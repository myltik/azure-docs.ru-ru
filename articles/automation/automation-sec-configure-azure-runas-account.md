---
title: "Настройка в Azure учетной записи запуска от имени | Документация Майкрософт"
description: "В этом руководстве приводятся пошаговые инструкции по созданию и тестированию, а также пример проверки подлинности с помощью субъекта безопасности в службе автоматизации Azure."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
keywords: "имя субъекта-службы, SetSPN, проверка подлинности Azure"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/24/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 6966befa56dc6a0feff4b8a821bde4e423a2b53a
ms.openlocfilehash: 97853ce9f78078cc6bbccdfb5c5a06cae49e218c
ms.lasthandoff: 02/24/2017


---
# <a name="authenticate-runbooks-with-azure-run-as-account"></a>Проверка подлинности модулей Runbook в Azure с помощью учетной записи запуска от имени
В этой статье объясняется, как настроить учетную запись службы автоматизации на портале Azure с помощью учетной записи запуска от имени, чтобы проверять подлинность ресурсов управления модулями Runbook в Azure Resource Manager или в службе управления службами Azure.

При создании новой учетной записи службы автоматизации на портале Azure автоматически создаются следующие ресурсы.

* Учетная запись запуска от имени, которая создает субъект-службу в Azure Active Directory и сертификат, а также назначает контроль доступа на основе роли участника. Будет использоваться для управления ресурсами Resource Manager с помощью модулей Runbook.   
* Классическая учетная запись запуска от имени. Создается путем отправки сертификата управления. Будет использоваться для администрирования службы управления службами Azure или классических ресурсов с помощью модулей Runbook.  

Это упрощает процесс и помогает быстро начать построение и развертывание модулей Runbook для решения конкретных задач автоматизации.      

Используя учетную запись запуска от имени и классическую учетную запись, вы можете:

* внедрить стандартную процедуру проверки подлинности в Azure при управлении ресурсами Azure Resource Manager и управления службами Azure с помощью модулей Runbook на портале Azure;  
* автоматизировать использование глобальных модулей Runbook, настроенных в службе оповещений Azure.

> [!NOTE]
> Для работы [функции интеграции оповещений](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) Azure с глобальными модулями Runbook службы автоматизации требуется учетная запись службы автоматизации, в которой настроены учетная запись запуска от имени и классическая учетная запись запуска от имени. Вы можете выбрать учетную запись службы автоматизации, в которой уже определены учетная запись запуска от имени и классическая учетная запись запуска от имени, или создать новую.
>  

Мы покажем, как создать учетную запись службы автоматизации на портале Azure, обновить учетную запись службы автоматизации с помощью PowerShell, а также как управлять конфигурацией учетной записи и проходить аутентификацию в модулях Runbook.

Прежде чем приступать к выполнению этих задач, следует принять во внимание несколько моментов.

1. Эта конфигурация не повлияет на существующие учетные записи службы автоматизации, созданные в классической модели развертывания или в модели развертывания Resource Manager.  
2. Эта конфигурация работает только для учетных записей службы автоматизации, созданных с помощью портала Azure.  При попытке создать учетную запись на классическом портале конфигурация учетной записи запуска от имени не будет реплицирована.
3. Если вы уже создали модули Runbook и ресурсы (например, расписания, переменные и т. д.) для управления классическими ресурсами и хотите выполнять аутентификацию в этих модулях Runbook с помощью классической учетной записи запуска от имени, потребуется создать учетную запись запуска от имени с помощью управления учетными записями запуска от имени или обновить существующую учетную запись с помощью сценария PowerShell, приведенного ниже.  
4. Чтобы выполнять проверку подлинности с помощью новой учетной записи запуска от имени и классической учетной записи запуска от имени (учетная запись службы автоматизации), измените существующие модули Runbook с помощью примера кода, приведенного ниже.  **Обратите внимание**, учетная запись запуска от имени предназначена для проверки подлинности в ресурсах Resource Manager с помощью субъекта-службы на основе сертификата. А классическая учетная запись запуска от имени используется для проверки подлинности в ресурсах Resource Manager с помощью сертификата управления.     

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Создание учетной записи службы автоматизации на портале Azure
В этом разделе указаны действия, с помощью которых на портале Azure можно создать новую учетную запись службы автоматизации Azure.  При этом создаются учетная запись запуска от имени и классическая учетная запись запуска от имени.  

> [!NOTE]
> Пользователь, выполняющий эти действия, должен быть участником роли администраторов службы или соадминистратором подписки, из которой пользователю предоставляется доступ к подписке. Пользователь также должен быть добавлен в роль "Пользователь" в стандартных подписках Active Directory. Учетной записи не нужно назначать привилегированную роль. Перед добавлением к роли соадминистратора подписки пользователи, не являющиеся участниками подписки Active Directory, будут добавлены в Active Directory в качестве гостей и увидят соответствующее предупреждение "У вас нет разрешений на создание..." в колонке **Add Automation Account** (Добавление учетной записи службы автоматизации). Пользователей, получивших роль соадминистратора, можно удалить из подписки Active Directory, а затем повторно добавить, чтобы предоставить им права полного доступа к Active Directory. Это можно проверить на портале Azure в области **Azure Active Directory**. Для этого щелкните **Пользователи и группы**, а затем **Все пользователи** и, выбрав конкретного пользователя, щелкните **Профиль**.  Значение атрибута **Тип пользователя** в профиле пользователя не должно соответствовать значению **Гость**.  
> 

1. Войдите на портал Azure с помощью учетной записи, которая является участником роли "Администраторы подписки" и соадминистратором подписки.
2. Выберите элемент **Учетные записи службы автоматизации**.
3. В колонке "Учетные записи службы автоматизации" щелкните **Добавить**.<br>![Добавление учетной записи службы автоматизации](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)
   
   > [!NOTE]
   > Если вы видите в колонке **Добавление учетной записи службы автоматизации** следующее предупреждение, это означает, что ваша учетная запись не является участником роли администраторов подписки и соадминистратором подписки.<br>![Предупреждение в колонке "Добавление учетной записи службы автоматизации"](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   > 
   > 
4. В колонке **Добавление учетной записи службы автоматизации** в поле **Имя** введите имя новой учетной записи службы автоматизации.
5. Если у вас несколько подписок, укажите одну из них для новой учетной записи, а также новую или существующую **группу ресурсов** и **расположение** центра обработки данных Azure.
6. Убедитесь, что для параметра **Создать учетную запись запуска от имени Azure** выбрано значение **Да**, и нажмите кнопку **Создать**.  
   
   > [!NOTE]
   > Если вы решили не создавать учетную запись запуска от имени, выбрав значение **Нет**, в колонке **Добавление учетной записи службы автоматизации** появится предупреждение.  Хотя учетная запись и создается на портале Azure, она не будет содержать соответствующее удостоверение проверки подлинности в службе каталогов классической подписки или подписки Resource Manager, поэтому у этой учетной записи не будет доступа к ресурсам в вашей подписке.  Из-за этого модули Runbook, ссылающиеся на эту учетную запись, не смогут проходить проверку подлинности и выполнять задачи, используя ресурсы в соответствующих моделях развертывания.
   > 
   > ![Предупреждение в колонке "Добавление учетной записи службы автоматизации"](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br>
   > Если субъект-служба не создан, роль участника не назначается.
   > 

7. Ход создания учетной записи службы автоматизации в Azure можно отслеживать в разделе **Уведомления** в меню.

### <a name="resources-included"></a>Создаваемые ресурсы
После создания учетной записи службы автоматизации автоматически создаются несколько ресурсов.  В следующей таблице перечислены ресурсы для учетной записи запуска от имени.<br>

| Ресурс | Описание |
| --- | --- |
| Модуль Runbook AzureAutomationTutorial |Пример графического модуля Runbook, который демонстрирует, как выполнить аутентификацию с помощью учетной записи запуска от имени, и получает доступ ко всем ресурсам Resource Manager. |
| Модуль Runbook AzureAutomationTutorialScript |Пример модуля Runbook PowerShell, который демонстрирует, как выполнить проверку подлинности с помощью учетной записи запуска от имени, и получает доступ ко всем ресурсам Resource Manager. |
| AzureRunAsCertificate |Ресурс-контейнер сертификатов, который автоматически создается во время создания учетной записи службы автоматизации или с помощью приведенного ниже сценария PowerShell для существующей учетной записи.  Он позволяет пройти проверку подлинности в Azure, что дает возможность управлять ресурсами Azure Resource Manager с помощью модулей Runbook.  Срок действия этого сертификата — один год. |
| AzureRunAsConnection |Ресурс-контейнер подключений, который автоматически создается во время создания учетной записи службы автоматизации или с помощью приведенного ниже сценария PowerShell для существующей учетной записи. |

В следующей таблице перечислены ресурсы для классической учетной записи запуска от имени.<br>

| Ресурс | Описание |
| --- | --- |
| Модуль Runbook AzureClassicAutomationTutorial |Пример графического модуля Runbook, который получает доступ ко всем классическим виртуальным машинам в подписке с помощью классической учетной записи запуска от имени (сертификат), а затем выводит имя и состояние виртуальной машины. |
| Модуль Runbook AzureClassicAutomationTutorialScript |Пример модуля Runbook PowerShell, который получает доступ ко всем классическим виртуальным машинам в подписке с помощью классической учетной записи запуска от имени (сертификат), а затем выводит имя и состояние виртуальной машины. |
| AzureClassicRunAsCertificate |Автоматически созданный ресурс-контейнер сертификатов, который используется для проверки подлинности в Azure, что позволяет управлять классическими ресурсами Azure с помощью модулей Runbook.  Срок действия этого сертификата — один год. |
| AzureClassicRunAsConnection |Автоматически созданный ресурс-контейнер подключений, который используется для проверки подлинности в Azure, что позволяет управлять классическими ресурсами Azure с помощью модулей Runbook. |

## <a name="verify-run-as-authentication"></a>Тестирование проверки подлинности с помощью учетной записи запуска от имени
Выполним небольшой тест, чтобы убедиться, что вы сможете успешно пройти проверку подлинности с помощью новой учетной записи запуска от имени.     

1. На портале Azure откройте учетную запись службы автоматизации, созданную ранее.  
2. Щелкните плитку **Модули Runbook** , чтобы открыть список модулей Runbook.
3. Выберите модуль Runbook **AzureAutomationTutorialScript** и нажмите кнопку **Запустить**, чтобы запустить его.  Появится запрос на подтверждение запуска модуля Runbook.
4. Будет создано [задание Runbook](automation-runbook-execution.md) , откроется колонка "Задание", а состояние задания будет отображаться на плитке **Сводка по заданию** .  
5. Сначала задание получает состояние *В очереди* , указывающее на то, что задание ожидает, пока рабочая роль Runbook в облаке станет доступной. Как только рабочая роль затребует задание, оно получит состояние *Запущено*, а с началом фактического выполнения модуля Runbook — состояние *Выполняется*.  
6. После выполнения задания Runbook должно отобразиться состояние **Выполнено**.<br> ![Тестирование модуля Runbook субъекта безопасности](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. Чтобы просмотреть подробные результаты задания Runbook, щелкните плитку **Выходные данные** .
8. В колонке **Выходные данные** должна быть информация о том, что модуль Runbook прошел проверку подлинности и вернул список всех ресурсов, доступных в группе ресурсов.
9. Закройте колонку **Выходные данные**, чтобы вернуться к колонке **Сводка по заданию**.
10. Закройте колонку **Сводка по заданию** и соответствующую колонку модуля Runbook **AzureAutomationTutorialScript**.

## <a name="verify-classic-run-as-authentication"></a>Тестирование проверки подлинности классической учетной записи запуска от имени
Выполним небольшой тест, чтобы убедиться, что вы сможете успешно пройти проверку подлинности с помощью новой классической учетной записи запуска от имени.     

1. На портале Azure откройте учетную запись службы автоматизации, созданную ранее.  
2. Щелкните плитку **Модули Runbook** , чтобы открыть список модулей Runbook.
3. Выберите модуль Runbook **AzureClassicAutomationTutorialScript** и нажмите кнопку **Запустить**, чтобы запустить его.  Появится запрос на подтверждение запуска модуля Runbook.
4. Будет создано [задание Runbook](automation-runbook-execution.md) , откроется колонка "Задание", а состояние задания будет отображаться на плитке **Сводка по заданию** .  
5. Сначала задание получает состояние *В очереди* , указывающее на то, что задание ожидает, пока рабочая роль Runbook в облаке станет доступной. Как только рабочая роль затребует задание, оно получит состояние *Запущено*, а с началом фактического выполнения модуля Runbook — состояние *Выполняется*.  
6. После выполнения задания Runbook должно отобразиться состояние **Выполнено**.<br> ![Тестирование модуля Runbook субъекта безопасности](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. Чтобы просмотреть подробные результаты задания Runbook, щелкните плитку **Выходные данные** .
8. В колонке **Выходные данные** должна отображаться информация о том, что модуль Runbook прошел проверку подлинности и вернул список всех классических виртуальных машин в подписке.
9. Закройте колонку **Выходные данные**, чтобы вернуться к колонке **Сводка по заданию**.
10. Закройте колонку **Сводка по заданию** и колонку модуля Runbook **AzureClassicAutomationTutorialScript**.

## <a name="managing-azure-run-as-account"></a>Управление учетной записью запуска от имени Azure
В течение времени существования учетной записи службы автоматизации необходимо обновлять сертификат, пока срок его действия не истек, или, если вы считаете, что учетная запись была скомпрометирована, можно удалить учетную запись запуска от имени и создать ее заново.  В этом разделе будут приведены инструкции для выполнения этих операций.  

### <a name="certificate-renewal"></a>Обновление сертификата
Сертификат, созданный для учетной записи запуска от имени Azure, можно обновить в любое время до истечения его срока действия, составляющего один год с момента создания.  При обновлении старое значение сертификата сохраняется, чтобы гарантировать, что все модули Runbook, которые поставлены в очередь или активно выполняются и для аутентификации которых используется учетная запись запуска от имени, не будут затронуты.  Сертификат будет существовать до окончания срока действия.     

1. На портале Azure откройте учетную запись службы автоматизации.  
2. В колонке учетной записи автоматизации в области свойств учетной записи выберите **Учетные записи запуска от имени** в разделе **Параметры учетной записи**.<br><br> ![Область свойств учетной записи службы автоматизации](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)<br><br>
3. В колонке свойств **Учетные записи запуска от имени** выберите учетную запись запуска от имени или классическую учетную запись запуска от имени, для которой нужно обновить сертификат, и в колонке свойств выбранной учетной записи щелкните **Обновление сертификата**.<br><br> ![Обновление сертификата для учетной записи запуска от имени](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)<br><br> Появится запрос на подтверждение удаления.  
4. Ход обновления сертификата можно отслеживать в разделе **Уведомления** в меню.

### <a name="delete-run-as-account"></a>Удаление учетной записи запуска от имени
Ниже описывается удаление и повторное создание учетной записи запуска от имени Azure или классической учетной записи запуска от имени.  При выполнения этого действия учетная запись автоматизации сохраняется.  После удаления учетной записи запуска от имени или классической учетной записи запуска от имени ее можно создать заново на портале.  

1. На портале Azure откройте учетную запись службы автоматизации.  
2. В колонке учетной записи автоматизации в области свойств учетной записи выберите **Учетные записи запуска от имени** в разделе **Параметры учетной записи**.
3. В колонке свойств **Учетные записи запуска от имени** выберите учетную запись запуска от имени или классическую учетную запись запуска от имени, которую нужно удалить, и в колонке свойств выбранной учетной записи щелкните **Удалить**.<br><br> ![Удаление учетной записи запуска от имени](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)<br><br>  Появится запрос на подтверждение удаления.
4. Ход удаления учетной записи можно отслеживать в разделе **Уведомления** в меню.  После завершения удаления ее можно будет повторно создать в колонке свойств **Учетные записи запуска от имени**, выбрав параметр создания **Учетная запись запуска от имени Azure**.<br><br> ![Повторное создание учетной записи запуска от имени службы автоматизации](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)<br> 

### <a name="misconfiguration"></a>Неправильные настройки
Если какой-либо из элементов конфигурации, необходимых для правильной работы учетной записи запуска от имени или классической учетной записи запуска от имени, был удален или неправильно создан во время начальной настройки, в том числе:

* ресурс сертификата, 
* ресурс подключения, 
* учетная запись запуска от имени (удалена из роли участника),
* субъект-служба или приложение-служба в Azure AD,

то служба автоматизации обнаружит эти изменения и уведомит вас, отобразив состояние **Не завершено** в колонке свойств **Учетные записи запуска от имени** для вашей учетной записи.<br><br> ![Сообщение о том, что настройка учетной записи запуска от имени не завершена](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)<br><br>При выборе учетной записи запуска от имени в области свойств учетной записи появится приведенное ниже предупреждение.<br><br> ![Предупреждение о том, что настройка учетной записи запуска от имени не завершена](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png)<br>  
Если учетная запись запуска от имени настроена неправильно, это можно быстро устранить, удалив и повторно создав ее.   

## <a name="update-an-automation-account-using-powershell"></a>Обновление учетной записи службы автоматизации с помощью PowerShell
В этом разделе объясняется, как использовать PowerShell для обновления существующей учетной записи службы автоматизации в следующих случаях.

1. Вы создали учетную запись службы автоматизации, но не создали учетную запись запуска от имени.
2. Необходимо создать учетную запись автоматизации в облаке Azure для государственных организаций.
3. У вас уже есть учетная запись службы автоматизации для управления ресурсами Resource Manager, и вы хотите обновить ее, чтобы включить учетную запись запуска от имени в процедуру проверки подлинности модуля Runbook.
4. У вас есть учетная запись службы автоматизации для управления классическими ресурсами, и вы хотите обновить ее, чтобы использовать классическую учетную запись запуска от имени, а не создавать новую учетную запись и переносить в нее модули Runbook и ресурсы-контейнеры.   

Прежде чем продолжить, проверьте, выполнены ли следующие действия:

1. Этот сценарий поддерживает выполнение только в ОС Windows 10 и Windows Server 2016, в которых установлены модули Azure Resource Manager 2.01 или более поздней версии.  Более ранние версии Windows не поддерживаются.  
2. Azure PowerShell 1.0 или более поздней версии. Сведения об этом выпуске и его установке см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).
3. Создана учетная запись службы автоматизации.  Эта учетная запись будет указана как значение для параметров -AutomationAccountName и -ApplicationDisplayName в приведенных ниже сценариях.

Чтобы получить значения для параметров *SubscriptionID*, *ResourceGroup* и *AutomationAccountName*, которые являются обязательными для сценариев, на портале Azure в колонке **Учетная запись службы автоматизации** выберите свою учетную запись службы автоматизации, а затем щелкните **Все параметры**.  В колонке **Все параметры** в разделе **Параметры учетной записи** выберите пункт **Свойства**.  В колонке **Свойства** вы увидите нужные значения.<br><br> ![Свойства учетной записи службы автоматизации](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-run-as-account-powershell-script"></a>Создание сценария PowerShell для учетной записи запуска от имени
Сценарий PowerShell, приведенный ниже, настроит следующую конфигурацию.

* Приложение Azure AD, которое будет использовать для проверки подлинности самозаверяющий сертификат, создаст учетную запись субъекта-службы для этого приложения в Azure AD и назначит роль участника (вместо нее может использоваться роль владельца или любая другая роль) этой учетной записи в вашей текущей подписке.  Дополнительные сведения см. в статье [Управление доступом на основе ролей в службе автоматизации Azure](automation-role-based-access-control.md).
* Ресурс-контейнер сертификатов службы автоматизации в указанной учетной записи службы автоматизации с именем **AzureRunAsCertificate**, содержащий сертификат, используемый в субъекте-службе.
* Ресурс-контейнер подключений к службе автоматизации в указанной учетной записи службы автоматизации с именем **AzureRunAsConnection**, содержащий идентификаторы приложения, клиента и подписки, а также отпечаток сертификата.    

Процедура выполнения сценария описана ниже.

1. Сохраните приведенный ниже сценарий на компьютере.  В этом примере используйте имя файла **New-AzureServicePrincipal.ps1**.  
   
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
        [String] $CertPlainPassword,
   
        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12

        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
        )
   
        #Check to see which cloud environment to sign into.
        Switch ($Environment)
        {
          "AzureCloud" {Login-AzureRmAccount}
          "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment} 
        }
        Import-Module AzureRM.Resources
        Select-AzureRmSubscription -SubscriptionId $SubscriptionId
   
        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
   
        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"
   
        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
   
        $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
        $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())
   
        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= $EndDate
        $KeyCredential.KeyId = $KeyId
        #$KeyCredential.Type = "AsymmetricX509Cert"
        #$KeyCredential.Usage = "Verify"
        $KeyCredential.CertValue = $KeyValue
   
        # Use Key credentials
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential
   
        New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
        Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose
   
        $NewRole = $null
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
           Sleep 5
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           Sleep 10
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
   
        # Get the tenant id for this subscription
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
   
        # Create the automation resources
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose
   
        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        $ConnectionAssetName = "AzureRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues

2. На компьютере запустите с повышенными правами **Windows PowerShell** с **начального** экрана.
3. Из оболочки командной строки PowerShell с повышенными правами перейдите к папке, которая содержит сценарий, созданный на шаге 1, и выполните его, изменив значения параметров *-ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId*, *-CertPlainPassword* и *-Environment*.<br>
   
   > [!NOTE]
   > После выполнения сценария появится запрос на проверку подлинности в Azure. Войдите в учетную запись, которая является участником роли администраторов подписки и соадминистратором подписки.
   > 
   > 
   
        .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName>
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>" -Environment <valid values are AzureCloud or AzureUSGovernment>  
   <br>

После успешного выполнения сценария используйте [пример кода](#sample-code-to-authenticate-with-resource-manager-resources) , указанный ниже, для проверки подлинности с помощью ресурсов Resource Manager и проверки конфигурации учетных данных.

### <a name="create-classic-run-as-account-powershell-script"></a>Создание сценария PowerShell для классической учетной записи запуска от имени
Сценарий PowerShell, приведенный ниже, настроит следующую конфигурацию.

* Ресурс-контейнер сертификатов службы автоматизации в указанной учетной записи службы автоматизации с именем **AzureClassicRunAsCertificate**, содержащий сертификат, используемый для проверки подлинности модулей Runbook.
* Ресурс-контейнер подключений к службе автоматизации в указанной учетной записи службы автоматизации с именем **AzureClassicRunAsConnection**, содержащий имя подписки, идентификатор подписки и имя ресурса-контейнера сертификатов.

Сценарий создаст самозаверяющий сертификат управления и сохранит его в папке временных файлов на компьютере в профиле пользователя, который выполнял сеанс PowerShell: *%ПРОФИЛЬ_ПОЛЬЗОВАТЕЛЯ%\AppData\Local\Temp*.  После выполнения сценария необходимо отправить сертификат управления Azure в хранилище управления подписки, в которой создана учетная запись службы автоматизации.  Процедура выполнения сценария и отправки сертификата описана ниже.  

1. Сохраните приведенный ниже сценарий на компьютере.  В этом примере используйте имя файла **New-AzureClassicRunAsAccount.ps1**.
   
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
        [String] $CertPlainPassword,
   
        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )
   
        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId
        $SubscriptionName = $subscription.Subscription.SubscriptionName
   
        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
        $CertPathCer = Join-Path $env:TEMP ($ApplicationDisplayName + ".cer")
   
        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"
   
        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPathCer -Type CERT | Write-Verbose
   
        # Create the automation resources
        $ClassicCertificateAssetName = "AzureClassicRunAsCertificate"
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name $ClassicCertificateAssetName  -Password $CertPassword -Exportable | write-verbose
   
        # Create a Automation connection asset named AzureClassicRunAsConnection in the Automation account. This connection uses the ClassicCertificateAssetName.
        $ConnectionAssetName = "AzureClassicRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicCertificateAssetName}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureClassicCertificate -ConnectionFieldValues $ConnectionFieldValues
   
        Write-Host -ForegroundColor red "Please upload the cert $CertPathCer to the Management store by following the steps below."
        Write-Host -ForegroundColor red "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates."
        Write-Host -ForegroundColor red "Then click Upload and upload the certificate $CertPathCer"

2. На компьютере запустите с повышенными правами **Windows PowerShell** с **начального** экрана.  
3. Из оболочки командной строки PowerShell с повышенными правами перейдите к папке, которая содержит сценарий, созданный на этапе 1, и выполните его, изменив значения параметров *-ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* и *-CertPlainPassword*.<br>
   
   > [!NOTE]
   > После выполнения сценария появится запрос на проверку подлинности в Azure. Войдите в учетную запись, которая является участником роли администраторов подписки и соадминистратором подписки.
   > 
   > 
   
        .\New-AzureClassicRunAsAccount.ps1 -ResourceGroup <ResourceGroupName>
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"

После успешного выполнения сценария скопируйте сертификат, созданный в папке временных файлов **Temp** своего профиля пользователя.  [Отправьте сертификат управления API](../azure-api-management-certs.md) на классический портал Azure. Затем воспользуйтесь [примером кода](#sample-code-to-authenticate-with-service-management-resources), чтобы проверить конфигурацию учетных данных с помощью ресурсов управления службами.

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Пример кода для проверки подлинности с помощью ресурсов Resource Manager
Чтобы выполнить проверку подлинности с помощью учетной записи запуска от имени для управления ресурсами Resource Manager с помощью модулей Runbook, используйте обновленный пример кода, приведенный ниже, взятый из примера модуля Runbook **AzureAutomationTutorialScript** .   

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


Этот сценарий включает две дополнительные строки кода, что позволяет ссылаться на контекст подписки и без проблем работать с несколькими подписками. Ресурс-контейнер переменных с именем SubscriptionId содержит идентификатор подписки, а после инструкции командлета Add-AzureRmAccount [командлет Set-AzureRmContext](https://msdn.microsoft.com/library/mt619263.aspx) указывается с набором параметров *-SubscriptionId*. Если имя переменной слишком общее, можно изменить имя переменной, включив в него префикс или другое соглашение об именовании, чтобы упростить идентификацию для ваших целей. Кроме того, можно использовать набор параметров -SubscriptionName вместо -SubscriptionId с соответствующим ресурсом-контейнером переменных.    

Обратите внимание, что командлет **Add-AzureRmAccount**, применяемый для проверки подлинности в модуле Runbook, использует набор параметров *ServicePrincipalCertificate* .  Он выполняет проверку подлинности с помощью сертификата субъекта-службы, а не учетных данных.  

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Пример кода для проверки подлинности с помощью ресурсов управления службами
Чтобы выполнить проверку подлинности с помощью классической учетной записи запуска от имени для управления классическими ресурсами с помощью модулей Runbook, используйте обновленный пример кода, приведенный ниже, взятый из примера модуля Runbook **AzureClassicAutomationTutorialScript** .

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
* Дополнительные сведения о субъектах-службах см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](../active-directory/active-directory-application-objects.md).
* Дополнительные сведения об управлении доступом на основе ролей в службе автоматизации Azure см. в [этой статье](automation-role-based-access-control.md).
* Дополнительные сведения о сертификатах и службах Azure см. в статье [Общие сведения о сертификатах для облачных служб Azure](../cloud-services/cloud-services-certs-create.md).


