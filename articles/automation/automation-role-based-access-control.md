<properties 
   pageTitle="Управление доступом на основе ролей в службе автоматизации Azure | Microsoft Azure"
   description="Контроль доступа на основе ролей (RBAC) Azure обеспечивает управление доступом к ресурсам Azure. В этой статье описывается настройка RBAC в службе автоматизации Azure."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="автоматизация RBAC, контроль доступа на основе ролей, RBAC Azure" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/12/2016"
   ms.author="magoedte;sngun"/>

# Управление доступом на основе ролей в службе автоматизации Azure

## Контроль доступа на основе ролей

Контроль доступа на основе ролей (RBAC) Azure обеспечивает управление доступом к ресурсам Azure. С помощью [RBAC](../active-directory/role-based-access-control-configure.md) вы сможете распределить обязанности внутри своей команды и предоставить доступ пользователям, группам и приложениям на том уровне, который им необходим для выполнения поставленных задач. Для предоставления доступа на основе ролей можно использовать портал Azure, средства командной строки Azure и API управления Azure.

## Управление доступом в учетных записях автоматизации

В службе автоматизации Azure доступ предоставляется путем назначения соответствующей роли RBAC пользователям, группам и приложениям в области учетной записи автоматизации. Ниже перечислены встроенные роли, поддерживаемые учетной записью автоматизации.

|**Роль** | **Описание** |
|:--- |:---|
| Владелец | Роль владельца обеспечивает доступ ко всем ресурсам и действиям в учетной записи службы автоматизации, включая предоставление доступа на управление учетной записью службы автоматизации другим пользователям, группам и приложениям. |
| Участник | Роль участника позволяет управлять всем, кроме изменения разрешений других пользователей на доступ к учетной записи службы автоматизации. |
| Читатель | Роль читателя позволяет просматривать все ресурсы в учетной записи службы автоматизации, но не дает возможность вносить какие-либо изменения.|
| Оператор службы автоматизации | Роль оператора службы автоматизации позволяет выполнять такие рабочие задачи, как запуск, остановка, приостановка, возобновление и планирование заданий. Эта роль пригодится, если вам нужно защитить ресурсы учетной службы автоматизации, такие как учетные данные, активы и модули Runbook, от просмотра и изменения, и в то же время разрешить членам вашей организации выполнять эти модули Runbook. |
| Администратор доступа пользователей | Роль администратора доступа пользователей позволяет управлять доступом пользователей к учетным записям службы автоматизации Azure. |

>[AZURE.NOTE] Невозможно предоставить права доступа конкретным модулям Runbook. Их можно предоставить только ресурсам и действиям в учетной записи службы автоматизации.

В этой статье мы рассмотрим порядок настройки RBAC в службе автоматизации Azure. Сначала давайте подробнее рассмотрим отдельные разрешения, предоставляемые ролям участника, читателя, оператора службы автоматизации и администратора доступа пользователей. Таким образом мы расширим свои знания, прежде чем предоставлять кому-либо права к учетной записи службы автоматизации. Отсутствие знаний может привести к непредвиденным и нежелательным последствиям.

## Разрешения для роли участника

В следующей таблице представлены конкретные действия, которые может выполнять пользователь с ролью участника в службе автоматизации.

| **Тип ресурса** | **чтение** | **запись** | **Удалить** | **Другие действия** |
|:--- |:---|:--- |:---|:--- |
| Учетная запись службы автоматизации Azure | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | 
| Ресурс сертификатов службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | |
| Ресурс подключений службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | 
| Ресурс типа подключения службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | 
| Ресурс учетных данных службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | |
| Ресурс расписания службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | |
| Ресурс переменных службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | |
| Настройка требуемого состояния службы автоматизации | | | | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) |
| Тип ресурсов гибридной рабочей роли Runbook | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | 
| Задание службы автоматизации Azure | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | 
| Поток заданий службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | | 
| Расписание заданий службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | |
| Модуль службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | |
| Runbook службы автоматизации Azure | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) |
| Черновик Runbook службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) |
| Задание тестирования черновика Runbook службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | 
| Объект webhook службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) |

## Разрешения роли читателя

В следующей таблице представлены конкретные действия, которые может выполнять пользователь с ролью читателя в службе автоматизации.

| **Тип ресурса** | **чтение** | **запись** | **Удалить** | **Другие действия** |
|:--- |:---|:--- |:---|:--- |
| Классический администратор подписки | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | 
| Блокировка управления | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | 
| Разрешение | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | |
| Операции с поставщиками | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | 
| Назначение роли | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | 
| Определение роли | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | 

## Разрешения роли оператора службы автоматизации

В следующей таблице представлены конкретные действия, которые может выполнять пользователь с ролью оператора в службе автоматизации.

| **Тип ресурса** | **чтение** | **запись** | **Удалить** | **Другие действия** |
|:--- |:---|:--- |:---|:--- |
| Учетная запись службы автоматизации Azure | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | 
| Ресурс сертификатов службы автоматизации | | | |
| Ресурс подключений службы автоматизации | | | |
| Ресурс типа подключения службы автоматизации | | | |
| Ресурс учетных данных службы автоматизации | | | |
| Ресурс расписания службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | |
| Ресурс переменных службы автоматизации | | | |
| Настройка требуемого состояния службы автоматизации | | | | |
| Тип ресурсов гибридной рабочей роли Runbook | | | | | 
| Задание службы автоматизации Azure | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | .![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | 
| Поток заданий службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | |  
| Расписание заданий службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | |
| Модуль службы автоматизации | | | |
| Runbook службы автоматизации Azure | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Черновик Runbook службы автоматизации | | | |
| Задание тестирования черновика Runbook службы автоматизации | | | |  
| Объект webhook службы автоматизации | | | |

Дополнительные сведения см. в разделе [Оператор службы автоматизации](../active-directory/role-based-access-built-in-roles.md#automation-operator). В нем перечислены действия, поддерживаемые ролью оператора в рамках учетной записи службы автоматизации и ее ресурсами.

## Разрешения роли администратора доступа пользователей

В следующей таблице представлены конкретные действия, которые может выполнять пользователь с ролью администратора доступа пользователей в службе автоматизации.

| **Тип ресурса** | **чтение** | **запись** | **Удалить** | **Другие действия** |
|:--- |:---|:--- |:---|:--- |
| Учетная запись службы автоматизации Azure | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ресурс сертификатов службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ресурс подключений службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ресурс типа подключения службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ресурс учетных данных службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ресурс расписания службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ресурс переменных службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Настройка требуемого состояния службы автоматизации | | | | |
| Тип ресурсов гибридной рабочей роли Runbook | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | | 
| Задание службы автоматизации Azure | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | | 
| Поток заданий службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | | 
| Расписание заданий службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Модуль службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Runbook службы автоматизации Azure | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Черновик Runbook службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Задание тестирования черновика Runbook службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | | | 
| Объект webhook службы автоматизации | ![Зеленый индикатор](media/automation-role-based-access-control/green-checkmark.png) | | |

## Настройки RBAC для учетной записи службы автоматизации с помощью портала Azure

1.	Войдите на [портал Azure](https://portal.azure.com/) и откройте учетную запись службы автоматизации в колонке "Учетные записи службы автоматизации".

2.	Щелкните элемент управления **Доступ** в правом верхнем углу экрана. Откроется колонка **Пользователи**, в которую можно добавить новых пользователей, группы и приложения, чтобы управлять учетной записью службы автоматизации и просматривать имеющиеся роли, настраиваемые для этой учетной записи.

    ![Кнопка доступа](media/automation-role-based-access-control/automation-01-access-button.png)

>[AZURE.NOTE] **Администраторы подписки** уже существуют в качестве пользователей по умолчанию. Группа администраторов подписки Active Directory включает администраторов и соадминистраторов службы в рамках подписки Azure. Администратор службы является владельцем подписки Azure и ее ресурсов, а также наследует роль владельца в учетных записях службы автоматизации. Это означает, что доступ к подписке **наследуется** для **администраторов и соадминистраторов службы** и **назначается** для всех остальных пользователей. Щелкните **Администраторы подписки**, чтобы посмотреть дополнительные сведения об их разрешениях.

### Добавление нового пользователя и назначение роли

1.	В колонке "Пользователи" нажмите кнопку **Добавить**, чтобы открыть колонку **Добавить доступ**, в которой можно добавить пользователя, группу или приложение, а также назначить им роль.

    ![Добавить пользователя](media/automation-role-based-access-control/automation-02-add-user.png)

2.	Выберите роль из списка доступных ролей. Мы выберем роль **Читатель**, но можно выбрать любую из доступных встроенных ролей, поддерживаемых учетной записью службы автоматизации, или пользовательскую роль, которую вы создали сами.

    ![Выбрать роль](media/automation-role-based-access-control/automation-03-select-role.png)

3.	Щелкните **Добавить пользователей**, чтобы открыть колонку **Добавление пользователей**. Если вы уже добавили пользователей, группы или приложения для управления подпиской, выберите пользователей для предоставления доступа в открывшемся списке. Если пользователей в списке нет или нужный пользователь не указан, щелкните **Пригласить**, чтобы открыть колонку **Пригласить гостя**, где можно пригласить пользователя с электронным адресом действительной учетной записи Майкрософт, например Outlook.com, OneDrive или Xbox Live. Указав адрес электронной почты пользователя, щелкните **Выбрать**, чтобы добавить пользователя, а затем нажмите кнопку **ОК**.

    ![Добавление пользователей](media/automation-role-based-access-control/automation-04-add-users.png)
 
    Пользователь будет добавлен в колонку **Пользователи** с назначенной ролью **Читатель**.

    ![Список пользователей](media/automation-role-based-access-control/automation-05-list-users.png)

    Кроме того, роль можно назначить пользователю в колонке **Роли**.

1. В колонке "Пользователи" щелкните **Роли**, чтобы открыть колонку **Роли**. В этой колонке отображаются имена ролей и число пользователей и групп, которым она назначена.

    ![Назначение роли из колонки пользователей](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)
   
    >[AZURE.NOTE] Управление доступом на основе ролей настраивается только на уровне учетной записи службы автоматизации и не может настраиваться в ресурсах более низкого уровня.

    Пользователю, группе или приложению можно назначить сразу несколько ролей. Например, если добавить пользователю одновременно роль **оператора службы автоматизации** и роль **читателя**, он сможет просматривать все ресурсы службы автоматизации и выполнять задания модуля Runbook. Для просмотра всех назначенных пользователю ролей разверните раскрывающийся список.

    ![Просмотр нескольких ролей](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)
 
### Удаление пользователя

Разрешение на доступ для пользователя, который не управляет учетной записью службы автоматизации или прекращает работу в организации, можно удалить. Чтобы удалить пользователя, выполните описанные ниже действия.

1.	В колонке **Пользователи** выберите назначение роли, которое нужно удалить.

2.	Нажмите кнопку **Удалить** в колонке сведений о назначении.

3.	Нажмите кнопку **Да** для подтверждения удаления.

    ![Удаление пользователей](media/automation-role-based-access-control/automation-08-remove-users.png)

## Пользователь с назначенной ролью

Когда пользователь с назначенной ролью входит в свою учетную запись службы автоматизации, учетная запись ее владельца отображается в списке **каталогов по умолчанию**. Для просмотра учетной записи службы автоматизации, в которую он был добавлен, пользователь должен перейти из каталога по умолчанию в каталог владельца по умолчанию.

![Каталог по умолчанию](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)

### Возможности для пользователя с ролью оператора службы автоматизации

Когда пользователь с ролью оператора службы автоматизации просматривает учетную запись службы автоматизации, в которой он был назначен, отображаются только список модулей Runbook, задания Runbook и расписания, созданные в учетной записи службы автоматизации, но не отображаются их определения. Такой пользователь может запускать, останавливать, приостанавливать, возобновлять и планировать задания Runbook. У него не будет доступа к другим ресурсам службы автоматизации, включая конфигурации, гибридные рабочие роли и узлы DSC.

![Нет доступа к ресурсам](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

Когда пользователь выбирает тот или иной модуль Runbook, команды для просмотра источника или правки этого модуля не отображаются, поскольку роль оператора службы автоматизации не позволяет выполнять подобные действия.

![Нет доступа к правке модуля](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)

Пользователь может просматривать и создавать расписания, но не имеет доступа к другим типам активов.

![Нет доступа к активам](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)

Кроме того, пользователь не видит, какие объекты Webhook связаны с модулем Runbook.

![Нет доступа к объектам webhook](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)

## Настройки RBAC для учетной записи службы автоматизации с помощью Azure PowerShell

Доступ к учетной записи службы автоматизации на основе ролей можно также настроить с помощью указанных ниже [командлетов Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).

• Командлет [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) выводит список всех ролей RBAC, доступных в Azure Active Directory. При использовании со свойством **Name** он позволяет получить список всех действий, которые может выполнить определенная роль. **Пример:** ![Получение определения роли](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)

• Командлет [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) выводит список назначений ролей RBAC Azure AD в указанной области. Без параметров эта команда возвращает все назначения ролей, существующие в подписке. С помощью параметра **ExpandPrincipalGroups** можно получить список назначений доступа для указанного пользователя, а также для групп, участником которых он является. **Пример**. Следующая команда выводит список всех пользователей в учетной записи службы автоматизации с указанием их ролей.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Получение назначения роли](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• Командлет [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) предоставляет доступ пользователям, группам и приложениям в определенной области. **Пример**. Следующая команда назначает роль оператора службы автоматизации пользователю в области учетной записи службы автоматизации.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Новое назначение роли](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• Командлет [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) удаляет доступ для указанного пользователя, группы или приложения в определенной области. **Пример**. Следующая команда удаляет пользователя из роли оператора службы автоматизации в области учетной записи службы автоматизации.

    Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

В представленных выше примерах замените **идентификатор для входа**, **идентификатор подписки**, **имя группы ресурсов** и **имя учетной записи службы автоматизации** данными своей учетной записи. Прежде чем продолжить удаление назначений ролей пользователей, выберите **Да** при появлении запроса на подтверждение.


## Дальнейшие действия
-  Сведения о различных способах настройки RBAC для службы автоматизации Azure см. в статье [Управление доступом на основе ролей с помощью Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
- Дополнительные сведения о различных способах запуска модуля Runbook см. в статье [Запуск модуля Runbook](automation-starting-a-runbook.md).
- Сведения о различных типах модуля Runbook см. в статье [Типы модулей Runbook в службе автоматизации Azure](automation-runbook-types.md).

<!---HONumber=AcomDC_0914_2016-->