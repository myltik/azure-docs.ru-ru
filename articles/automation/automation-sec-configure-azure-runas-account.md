---
title: "Настройка в Azure учетной записи запуска от имени | Документация Майкрософт"
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
ms.date: 03/10/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 15cbf897f3f67b9d1bee0845b4d287fdabe63ba8
ms.lasthandoff: 03/11/2017


---
# <a name="authenticate-runbooks-with-azure-run-as-account"></a>Проверка подлинности модулей Runbook в Azure с помощью учетной записи запуска от имени
В этой статье объясняется, как настроить учетную запись службы автоматизации на портале Azure с помощью учетной записи запуска от имени, чтобы проверять подлинность ресурсов управления модулями Runbook в Azure Resource Manager или в службе управления службами Azure.

При создании учетной записи службы автоматизации на портале Azure автоматически создаются следующие ресурсы.

* Учетная запись запуска от имени, которая создает субъект-службу в Azure Active Directory и сертификат, а также назначает контроль доступа на основе роли участника. Используется для управления ресурсами Resource Manager с помощью модулей Runbook.   
* Классическая учетная запись запуска от имени. Создается путем отправки сертификата управления. Используется для администрирования службы управления службами Azure или классических ресурсов с помощью модулей Runbook.  

Это упрощает процесс и помогает быстро начать построение и развертывание модулей Runbook для решения конкретных задач автоматизации.      

Используя учетную запись запуска от имени и классическую учетную запись, вы можете:

* внедрить стандартную процедуру проверки подлинности в Azure при управлении ресурсами Azure Resource Manager и управления службами Azure с помощью модулей Runbook на портале Azure;  
* автоматизировать использование глобальных модулей Runbook, настроенных в службе оповещений Azure.

> [!NOTE]
> Для работы [функции интеграции оповещений](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) Azure с глобальными модулями Runbook службы автоматизации требуется учетная запись службы автоматизации, в которой настроены учетная запись запуска от имени и классическая учетная запись запуска от имени. Вы можете выбрать учетную запись службы автоматизации, в которой уже определены учетная запись запуска от имени и классическая учетная запись запуска от имени, или создать новую.
>  

Мы покажем, как создать учетную запись службы автоматизации на портале Azure, обновить учетную запись службы автоматизации с помощью PowerShell, а также как управлять конфигурацией учетной записи и проходить проверку подлинности в модулях Runbook.

Прежде чем приступать к выполнению этих задач, следует принять во внимание несколько моментов.

1. Эта конфигурация не повлияет на существующие учетные записи службы автоматизации, созданные в классической модели развертывания или в модели развертывания Resource Manager.  
2. Эта конфигурация работает только для учетных записей службы автоматизации, созданных с помощью портала Azure.  При попытке создать учетную запись на классическом портале конфигурация учетной записи запуска от имени не будет реплицирована.
3. Если вы уже создали модули Runbook и ресурсы (например, расписания, переменные и т. д.) для управления классическими ресурсами и хотите выполнять проверку подлинности в этих модулях Runbook с помощью классической учетной записи запуска от имени, потребуется создать учетную запись запуска от имени с помощью управления учетными записями запуска от имени или обновить имеющуюся учетную запись с помощью скрипта PowerShell, приведенного ниже.  
4. Чтобы выполнять проверку подлинности с помощью новой учетной записи запуска от имени и классической учетной записи запуска от имени (учетная запись службы автоматизации), измените имеющиеся модули Runbook с помощью примера кода, приведенного в разделе [Примеры кода для проверки подлинности](#authentication-code-examples).  
   
    >[!NOTE] 
    >Учетная запись запуска от имени предназначена для проверки подлинности в ресурсах Resource Manager с помощью субъекта-службы на основе сертификата. А классическая учетная запись запуска от имени используется для проверки подлинности в ресурсах Resource Manager с помощью сертификата управления.     

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
| AzureRunAsCertificate |Ресурс-контейнер сертификатов, который автоматически создается во время создания учетной записи службы автоматизации или с помощью приведенного ниже скрипта PowerShell для имеющейся учетной записи.  Он позволяет пройти проверку подлинности в Azure, что дает возможность управлять ресурсами Azure Resource Manager с помощью модулей Runbook.  Срок действия этого сертификата — один год. |
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

### <a name="self-signed-certificate-renewal"></a>Обновление самозаверяющего сертификата
Самозаверяющий сертификат, созданный для учетной записи запуска от имени Azure, можно обновить в любое время до истечения его срока действия, составляющего один год с момента создания.  При обновлении старое значение сертификата сохраняется, чтобы гарантировать, что все модули Runbook, которые поставлены в очередь или активно выполняются и для аутентификации которых используется учетная запись запуска от имени, не будут затронуты.  Сертификат будет существовать до окончания срока действия.    

> [!NOTE]
> Если вы настроили учетную запись запуска от имени службы автоматизации так, чтобы использовать сертификат, выданный центром сертификации предприятия, и используете этот параметр, то этот сертификат будет заменен самозаверяющим сертификатом.  

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

то служба автоматизации обнаружит эти изменения и уведомит вас, отобразив состояние **Не завершено** в колонке свойств **Учетные записи запуска от имени** для вашей учетной записи.<br><br> ![Сообщение о том, что настройка учетной записи запуска от имени не завершена](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)<br><br>При выборе учетной записи запуска от имени в области свойств учетной записи появится приведенное ниже сообщение об ошибке.<br><br> ![Предупреждение о том, что настройка учетной записи запуска от имени не завершена](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png)<br>  
Если учетная запись запуска от имени настроена неправильно, это можно быстро устранить, удалив и повторно создав ее.   

## <a name="update-an-automation-account-using-powershell"></a>Обновление учетной записи службы автоматизации с помощью PowerShell
В этом разделе объясняется, как использовать PowerShell для обновления существующей учетной записи службы автоматизации в следующих случаях.

1. Вы создали учетную запись службы автоматизации, но не создали учетную запись запуска от имени. 
2. У вас уже есть учетная запись службы автоматизации для управления ресурсами Resource Manager, и вы хотите обновить ее, чтобы включить учетную запись запуска от имени в процедуру проверки подлинности модуля Runbook.
4. У вас есть учетная запись службы автоматизации для управления классическими ресурсами, и вы хотите обновить ее, чтобы использовать классическую учетную запись запуска от имени, а не создавать новую учетную запись и переносить в нее модули Runbook и ресурсы-контейнеры.   
5. Вы хотите создать учетную запись от имени Azure и классическую учетную запись запуска от имени, используя сертификат, выданный ЦС предприятия.

Предварительные требования для выполнения этого сценария:

1. Этот сценарий поддерживает выполнение только в ОС Windows 10 и Windows Server 2016, в которых установлены модули Azure Resource Manager 2.01 или более поздней версии.  Более ранние версии Windows не поддерживаются.  
2. Azure PowerShell 1.0 или более поздней версии. Сведения об этом выпуске и его установке см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).
3. Создана учетная запись службы автоматизации.  Эта учетная запись будет указана как значение для параметров -AutomationAccountName и -ApplicationDisplayName в приведенном ниже сценарии.

Чтобы получить значения для параметров *SubscriptionID*, *ResourceGroup* и *AutomationAccountName*, которые являются обязательными для сценариев, на портале Azure в колонке **Учетная запись службы автоматизации** выберите свою учетную запись службы автоматизации, а затем щелкните **Все параметры**.  В колонке **Все параметры** в разделе **Параметры учетной записи** выберите пункт **Свойства**.  В колонке **Свойства** вы увидите нужные значения.<br><br> ![Свойства учетной записи службы автоматизации](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-run-as-account-powershell-script"></a>Создание сценария PowerShell для учетной записи запуска от имени
Этот сценарий PowerShell включает в себя поддержку следующих конфигураций: 

* Создание учетной записи запуска от имени Azure, используя самозаверяющий сертификат.
* Создание учетной записи запуска от имени Azure и классической учетной записи запуска от имени Azure, используя самозаверяющий сертификат.
* Создание учетной записи запуска от имени Azure и классической учетной записи запуска от имени Azure, используя корпоративный сертификат.
* Создание учетной записи запуска от имени Azure и классической учетной записи запуска от имени Azure, используя самозаверяющий сертификат в облаке Azure для государственных организаций.

В зависимости от выбранного варианта конфигурации сценарий PowerShell создаст следующее:

* Приложение Azure AD, которое будет использовать для экспорта самозаверяющий сертификат или открытый ключ корпоративного сертификата, создаст учетную запись субъекта-службы для этого приложения в Azure AD и назначит роль участника (вместо нее может использоваться роль владельца или любая другая роль) этой учетной записи в вашей текущей подписке.  Дополнительные сведения см. в статье [Управление доступом на основе ролей в службе автоматизации Azure](automation-role-based-access-control.md).
* Ресурс-контейнер сертификатов службы автоматизации в указанной учетной записи службы автоматизации с именем **AzureRunAsCertificate**, содержащий закрытый ключ сертификата, используемый в приложении Azure AD.
* Ресурс-контейнер подключений к службе автоматизации в указанной учетной записи службы автоматизации с именем **AzureRunAsConnection**, содержащий идентификаторы приложения, клиента и подписки, а также отпечаток сертификата.    

Для классической учетной записи запуска от имени Azure:

* Ресурс-контейнер сертификатов службы автоматизации в указанной учетной записи службы автоматизации с именем **AzureClassicRunAsCertificate**, содержащий закрытый ключ сертификата, используемый в сертификате управления.  
* Ресурс-контейнер подключений к службе автоматизации в указанной учетной записи службы автоматизации с именем **AzureClassicRunAsConnection**, содержащий имя подписки, идентификатор подписки и имя ресурса-контейнера сертификатов.

Если вы выбрали создание классической учетной записи запуска от имени, после выполнения скрипта необходимо отправить открытый сертификат (в формате CER) в хранилище управления подписки, в которой создана учетная запись службы автоматизации. Процедура выполнения сценария и отправки сертификата описана ниже.    

1. Сохраните приведенный ниже сценарий на компьютере.  В этом примере используйте имя файла **New-RunAsAccount.ps1**.  
   
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
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use Key credentials and create AAD Application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id
   
        # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
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

        # Create Run As Account using Service Principal
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

        # Create Service Principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId} 

        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create Run As Account using Service Principal
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

        # Create the automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. На компьютере запустите с повышенными правами **Windows PowerShell** с **начального** экрана.
3. Из оболочки командной строки PowerShell с повышенными привилегиями перейдите в папку, которая содержит сценарий, созданный на шаге 1, и выполните этот сценарий, установив значения параметров в зависимости от требуемой конфигурации.  

    **Создание учетной записи запуска от имени Azure, используя самозаверяющий сертификат**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false` 

    **Создание учетной записи запуска от имени Azure и классической учетной записи запуска от имени Azure, используя самозаверяющий сертификат**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Создание учетной записи запуска от имени Azure и классической учетной записи запуска от имени Azure, используя корпоративный сертификат**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Создание учетной записи запуска от имени Azure и классической учетной записи запуска от имени Azure, используя самозаверяющий сертификат в облаке Azure для государственных организаций**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`
 
    > [!NOTE]
    > После выполнения сценария появится запрос на проверку подлинности в Azure. Войдите в учетную запись, которая является участником роли администраторов подписки и соадминистратором подписки.
    > 
    > 

После успешного выполнения скрипта (при условии создания классической учетной записи запуска от имени) выполните следующие действия, чтобы [отправить сертификат API управления](../azure-api-management-certs.md) на классический портал Azure.  Если вы создали классическую учетную запись запуска от имени с помощью самозаверяющего открытого сертификата (в формате CER), можно найти копию сертификата, созданную в папке временных файлов на компьютере в профиле пользователя, который выполнял сеанс PowerShell: *%USERPROFILE%\AppData\Local\Temp*.  Если же вы настроили классическую учетную запись запуска от имени для использования сертификата (в формате CER), созданного центром сертификации предприятия, вам потребуется использовать этот сертификат.  Отправив сертификат, воспользуйтесь [примером кода](#sample-code-to-authenticate-with-service-management-resources), чтобы проверить конфигурацию учетных данных с помощью ресурсов управления службами.  

Если вы не создали классическую учетную запись запуска от имени, используйте [пример кода](#sample-code-to-authenticate-with-resource-manager-resources), указанный ниже, для проверки подлинности с помощью ресурсов Resource Manager и проверки конфигурации учетных данных.   

##  <a name="authentication-code-examples"></a>Примеры кода для проверки подлинности

В следующих примерах показано, как выполнить проверку подлинности модулей Runbook в ресурсах Resource Manager или классических ресурсах с помощью учетной записи запуска от имени.

### <a name="authenticate-with-resource-manager-resources"></a>Проверка подлинности с помощью ресурсов Resource Manager
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

### <a name="authenticate-with-service-management-resources"></a>Проверка подлинности с помощью ресурсов управления службами
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


