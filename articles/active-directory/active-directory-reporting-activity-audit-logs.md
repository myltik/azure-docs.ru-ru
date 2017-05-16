---

title: "Отчеты о действиях аудита на портале Azure Active Directory | Документация Майкрософт"
description: "Общие сведения об отчетах о действиях аудита на портале Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 4065682658bdd99066266b8b4e5e4c4605ff3db9
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Отчеты о действиях аудита на портале Azure Active Directory 

Функция отчетов в Azure Active Directory (Azure AD) позволяет получать всю необходимую информацию, с помощью которой можно определить, как работает среда.

Архитектура создания отчетов в Azure AD состоит из следующих компонентов.

- **Действие** 
    - **Действия входа** — информация об использовании управляемых приложений и действиях входа.
    - **Журналы аудита** — данные системных операций об управлении пользователями и группами, об управляемых приложениях и действиях с каталогами.
- **Безопасность** 
    - **Входы, представляющие риск**. Вход, представляющий риск, означает, что в систему пытался войти пользователь, который не является законным владельцем учетной записи. Дополнительные сведения см. в разделе "Вход, представляющий риск".
    - **Пользователи, находящиеся в группе риска**. Такая пометка означает, что конфиденциальность учетной записи пользователя, возможно, нарушена. Дополнительные сведения см. в разделе "Пользователи, помеченные для события риска".

В этом разделе содержатся общие сведения о действиях аудита.
 


## <a name="audit-logs"></a>Журналы аудита

Журналы аудита в Azure Active Directory содержат записи о действиях системы (необходимые для соответствия требованиям).  
Знакомство с данными аудита следует начать с **журналов аудита** в разделе **Действие** службы **Azure Active Directory**.

![Журналы аудита](./media/active-directory-reporting-activity-audit-logs/61.png "Журналы аудита")

Журнал аудита содержит представление списка по умолчанию, в котором отображаются:

- дата и время выполнения действия;
- инициатор и субъект (*кто*) действия; 
- действие (*что*); 
- целевой объект.

![Журналы аудита](./media/active-directory-reporting-activity-audit-logs/18.png "Журналы аудита")

Представление списка можно настроить, щелкнув **Столбцы** на панели инструментов.

![Журналы аудита](./media/active-directory-reporting-activity-audit-logs/19.png "Журналы аудита")

Здесь вы можете добавить или удалить отображаемые поля.

![Журналы аудита](./media/active-directory-reporting-activity-audit-logs/21.png "Журналы аудита")


Щелкнув элемент в представлении списка, вы получите дополнительные сведения о нем.

![Журналы аудита](./media/active-directory-reporting-activity-audit-logs/22.png "Журналы аудита")


## <a name="filtering-audit-logs"></a>Фильтрация журналов аудита

Для сужения результатов до подходящего уровня вы можете отфильтровать данные аудита, используя следующие поля:

- диапазон дат;
- инициатор (субъект);
- Категория
- Тип ресурса действия
- Действие

![Журналы аудита](./media/active-directory-reporting-activity-audit-logs/23.png "Журналы аудита")


Фильтр **диапазона дат** позволяет определить интервал времени для возвращаемых данных.  
Возможные значения:

- 1 месяц
- 7 дней
- 24 часа
- Пользовательская

При выборе пользовательского интервала времени можно настроить время начала и окончания.

Фильтр **Инициатор** позволяет определить имя субъекта или его универсальное имя (UPN).

С помощью фильтра **Категория** можно выбрать один из следующих фильтров:

- Все
- Core category (Основная категория);
- Core directory (Основной каталог);
- Self-service password management (Самостоятельное управление паролями);
- Самостоятельное управление группами
- "Подготовка учетных записей";
- Automated password rollover (Автоматическая смена пароля);
- Invited users (Приглашаемые пользователи);
- MIM Service (Служба MIM).

С помощью фильтра **Тип ресурса действия** можно выбрать один из следующих фильтров:

- Все 
- Группа
- Каталог
- Пользователь
- Приложение
- Политика
- Устройство
- Другие

При выборе **группы** в качестве **типа ресурса действия** вы получите дополнительную категорию фильтра, позволяющую указать **источник**.

- Azure AD
- O365




Фильтр **Действие** зависит от выбранной категории и типа ресурса действия. Вы можете выбрать определенное действие или просмотреть все. 

| Категория действия| Тип ресурса действия| Действие |
| :-- | :-: | :-- |
| "Core Directory" (Основной каталог);| Группа| Delete Group Settings (Удаление параметров группы)|
| "Core Directory" (Основной каталог);| Каталог| Update Domain (Обновление домена)|
| "Core Directory" (Основной каталог);| Каталог| Remove Partner From Company (Удаление партнера из компании)|
| "Core Directory" (Основной каталог);| Пользователь| Update Role (Обновление роли)|
| "Core Directory" (Основной каталог);| Пользователь| Add Role From Template (Добавление роли на основе шаблона)|
| "Core Directory" (Основной каталог);| Группа| Add App Role Assignment To Group (Добавление назначения роли приложения в группу)|
| "Core Directory" (Основной каталог);| Группа| Start Applying Group Based License To Users (Начать назначать пользователям лицензию, назначенную группе)|
| "Core Directory" (Основной каталог);| Приложение| Add Service Principal (Добавление субъекта-службы)|
| "Core Directory" (Основной каталог);| Политика| Update Policy (Обновление политики)|
| "Core Directory" (Основной каталог);| Политика| Add Policy To Service Principal (Добавление политики субъекта-службы)|
| "Core Directory" (Основной каталог);| Устройство| Add Registered Owner To Device (Добавление зарегистрированного владельца устройства)|
| "Core Directory" (Основной каталог);| Устройство| Add Registered Users To Device (Добавление зарегистрированных пользователей устройства)|
| "Core Directory" (Основной каталог);| Устройство| Update Device Configuration (Обновление конфигурации устройства)|
| "Self-service Password Management" (Самостоятельное управление паролями);| Пользователь| Reset Password (Self-Service) (Сброс пароля (самостоятельный))|
| "Self-service Password Management" (Самостоятельное управление паролями);| Пользователь| Unlock User Account (Self-Service) (Разблокирование учетной записи пользователя (самостоятельное))|
| "Self-service Password Management" (Самостоятельное управление паролями);| Пользователь| Reset Password (By Admin) (Сброс пароля (администратор))|
| "Self-service Group Management" (Самостоятельное управление группами);| Группа| Delete A Pending Request To Join A Group (Удаление ожидающего запроса на присоединение к группе)|
| "Account Provisioning" (Подготовка учетных записей).| Приложение| Process Escrow (Обработка эскроу)|
| "Automated Password Rollover" (Автоматическая смена пароля)| Приложение| "Automated Password Rollover" (Автоматическая смена пароля)|
| Invited Users (Приглашаемые пользователи)| Другие| Batch Invites Processed (Обработанный пакет приглашений)|
| "Core Directory" (Основной каталог);| Каталог| Remove Verified Domain (Удаление проверенного домена)|
| "Core Directory" (Основной каталог);| Каталог| Add Unverified Domain (Добавление непроверенного домена)|
| "Core Directory" (Основной каталог);| Каталог| Add Verified Domain (Добавление проверенного домена)|
| "Core Directory" (Основной каталог);| Каталог| Set Directory Feature On Tenant (Установка поддержки каталога в клиенте)|
| "Core Directory" (Основной каталог);| Каталог| Set Dirsyncenabled Flag (Установка флага Dirsyncenabled)|
| "Core Directory" (Основной каталог);| Каталог| Create Company Settings (Создание параметров организации)|
| "Core Directory" (Основной каталог);| Каталог| Update Company Settings (Обновление параметров организации)|
| "Core Directory" (Основной каталог);| Каталог| Delete Company Settings (Удаление параметров организации)|
| "Core Directory" (Основной каталог);| Каталог| Set Company Allowed Data Location (Задание расположения данных организации)|
| "Core Directory" (Основной каталог);| Каталог| Set Company Multinational Feature Enabled (Включение функции многонациональности для организации)|
| "Core Directory" (Основной каталог);| Пользователь| Обновление пользователя|
| "Core Directory" (Основной каталог);| Пользователь| Удаление пользователя|
| "Core Directory" (Основной каталог);| Группа| Remove Member From Group (Удаление участника группы)|
| "Core Directory" (Основной каталог);| Группа| Set Group License (Настройка лицензии для группы)|
| "Core Directory" (Основной каталог);| Группа| Create Group Settings (Создание параметров группы)|
| "Core Directory" (Основной каталог);| Приложение| Update Service Principal (Обновление субъекта-службы)|
| "Core Directory" (Основной каталог);| Приложение| Удалить приложение|
| "Core Directory" (Основной каталог);| Приложение| Update Application (Обновление приложения)|
| "Core Directory" (Основной каталог);| Приложение| Remove Service Principal (Удаление субъекта-службы)|
| "Core Directory" (Основной каталог);| Приложение| Add Service Principal Credentials (Добавление учетных данных субъекта-службы)|
| "Core Directory" (Основной каталог);| Приложение| Remove App Role Assignment From Service Principal (Удаление назначения роли приложения из субъекта-службы)|
| "Core Directory" (Основной каталог);| Приложение| Remove Owner From Application (Удаление владельца приложения)|
| "Core Directory" (Основной каталог);| Устройство| Remove Registered Owner From Device (Удаление зарегистрированного владельца устройства)|
| "Self-service Password Management" (Самостоятельное управление паролями);| Пользователь| Self-Serve Password Reset Flow Activity Progress (Ход выполнения процесса самостоятельного сброса пароля)|
| "Account Provisioning" (Подготовка учетных записей).| Приложение| Администрирование|
| "Account Provisioning" (Подготовка учетных записей).| Приложение| Directory Operation (Операция с каталогом)|
| MIM Service (Служба MIM)| Группа| Remove Member (Удаление участника)|
| "Core Directory" (Основной каталог);| Политика| Удаление политики|
| Invited Users (Приглашаемые пользователи)| Пользователь| Viral Tenant Creation (Создание вирусного клиента)|
| "Core Directory" (Основной каталог);| Каталог| Update External Secrets (Обновление внешних секретов)|
| "Core Directory" (Основной каталог);| Каталог| Set Rights Management Properties (Установка свойств управления правами)|
| "Core Directory" (Основной каталог);| Каталог| Update Company (Обновление организации)|
| "Core Directory" (Основной каталог);| Пользователь| Добавить пользователя|
| "Core Directory" (Основной каталог);| Пользователь| Convert Federated User To Managed (Преобразование федеративного пользователя в управляемого)|
| "Core Directory" (Основной каталог);| Пользователь| Create Application Password For User (Создание пароля приложения для пользователя)|
| "Core Directory" (Основной каталог);| Группа| Add Member To Group (Добавление участника группы)|
| "Core Directory" (Основной каталог);| Группа| Добавить группу|
| "Core Directory" (Основной каталог);| Приложение| Consent To Application (Разрешение на приложение)|
| "Core Directory" (Основной каталог);| Приложение| Добавление приложения|
| "Core Directory" (Основной каталог);| Приложение| Add Owner To Service Principal (Добавление владельца субъекта-службы)|
| "Core Directory" (Основной каталог);| Приложение| Remove Oauth2Permissiongrant (Удаление записи Oauth2Permissiongrant)|
| "Core Directory" (Основной каталог);| Политика| Remove Policy Credentials (Удаление учетных данных политики)|
| "Core Directory" (Основной каталог);| Устройство| Delete Device Configuration (Удаление конфигурации устройства)|
| "Self-service Group Management" (Самостоятельное управление группами);| Группа| Set Dynamic Group Properties (Задание свойств динамической группы)|
| "Self-service Group Management" (Самостоятельное управление группами);| Группа| Update Lifecycle Management Policy (Обновление политики управления жизненным циклом)|
| "Account Provisioning" (Подготовка учетных записей).| Приложение| Synchronization Rule Action (Действие правила синхронизации)|
| Invited Users (Приглашаемые пользователи)| Другие| Batch Invites Uploaded (Отправленный пакет приглашений)|
| MIM Service (Служба MIM)| Группа| Добавить участника|
| "Core Directory" (Основной каталог);| Пользователь| Set License Properties (Задание свойств лицензии)|
| "Core Directory" (Основной каталог);| Пользователь| Восстановление учетной записи пользователя|
| "Core Directory" (Основной каталог);| Пользователь| Remove Member From Role (Удаление участника из роли)|
| "Core Directory" (Основной каталог);| Пользователь| Remove App Role Assignment From User (Удаление назначения роли приложения из учетной записи пользователя)|
| "Core Directory" (Основной каталог);| Пользователь| Remove Scoped Member From Role (Удаление участника с заданной областью из роли)|
| "Core Directory" (Основной каталог);| Группа| Обновление группы|
| "Core Directory" (Основной каталог);| Группа| Add Owner To Group (Добавление владельца группы)|
| "Core Directory" (Основной каталог);| Группа| Finish Applying Group Based License To Users (Завершить назначать пользователям лицензию, назначенную группе)|
| "Core Directory" (Основной каталог);| Группа| Remove App Role Assignment From User (Удаление назначения роли приложения из группы)|
| "Core Directory" (Основной каталог);| Группа| Set Group To Be Managed By User (Задание пользователя для управления группой)|
| "Core Directory" (Основной каталог);| Приложение| Add Oauth2Permissiongrant (Добавление записи Oauth2Permissiongrant)|
| "Core Directory" (Основной каталог);| Приложение| Add App Role Assignment To Service Principal (Добавление назначения роли приложения в субъект-службу)|
| "Core Directory" (Основной каталог);| Приложение| Remove Service Principal Credentials (Удаление учетных данных субъекта-службы)|
| "Core Directory" (Основной каталог);| Политика| Remove Policy From Service Principal (Удаление политики субъекта-службы)|
| "Core Directory" (Основной каталог);| Устройство| Update Device (Обновление устройства)|
| "Core Directory" (Основной каталог);| Устройство| Add Device (Добавление устройства)|
| "Core Directory" (Основной каталог);| Устройство| Add Device Configuration (Добавление конфигурации устройства)|
| "Self-service Password Management" (Самостоятельное управление паролями);| Пользователь| Change Password (Self-Service) (Изменение пароля (самостоятельное))|
| "Self-service Password Management" (Самостоятельное управление паролями);| Пользователь| User Registered For Self-Service Password Reset (Регистрация пользователей для самостоятельного сброса пароля)|
| "Self-service Group Management" (Самостоятельное управление группами);| Группа| Approve A Pending Request To Join A Group (Подтверждение ожидающего запроса на присоединение к группе)|
| "Core Directory" (Основной каталог);| Каталог| Remove Unverified Domain (Удаление непроверенного домена)|
| "Core Directory" (Основной каталог);| Каталог| Проверка домена|
| "Core Directory" (Основной каталог);| Каталог| Set Domain Authentication (Установка проверки подлинности домена)|
| "Core Directory" (Основной каталог);| Каталог| Установка политики паролей|
| "Core Directory" (Основной каталог);| Каталог| Add Partner To Company (Добавление партнера для компании)|
| "Core Directory" (Основной каталог);| Каталог| Promote Company To Partner (Продвижение компании до партнера)|
| "Core Directory" (Основной каталог);| Каталог| Set Partnership (Настройка партнерства)|
| "Core Directory" (Основной каталог);| Каталог| Set Accidental Deletion Threshold (Задание порогового значения случайного удаления)|
| "Core Directory" (Основной каталог);| Каталог| Demote Partner (Изменение типа партнера)|
| Invited Users (Приглашаемые пользователи)| Пользователь| Invite External User (Приглашение внешнего пользователя)|
| "Account Provisioning" (Подготовка учетных записей).| Приложение| Импорт|
| "Core Directory" (Основной каталог);| Приложение| Remove Owner From Service Principal (Удаление владельца субъекта-службы)|
| "Core Directory" (Основной каталог);| Устройство| Remove Registered Users From Device (Удаление зарегистрированных пользователей устройства)|
| "Core Directory" (Основной каталог);| Каталог| Установка информации о компании|
| "Core Directory" (Основной каталог);| Каталог| Set Federation Settings On Domain (Установка параметров федерации для домена)|
| "Core Directory" (Основной каталог);| Каталог| Create Company (Создать организацию)|
| "Core Directory" (Основной каталог);| Каталог| Purge Rights Management Properties (Очистка свойств управления правами)|
| "Core Directory" (Основной каталог);| Каталог| Set Dirsync Feature (Установка компонента Dirsync)|
| "Core Directory" (Основной каталог);| Каталог| Verify Email Verified Domain (Проверка домена с помощью электронной почты)|
| "Core Directory" (Основной каталог);| Пользователь| Change User License (Изменение лицензии пользователя)|
| "Core Directory" (Основной каталог);| Пользователь| Change User Password (Смена пароля пользователя)|
| "Core Directory" (Основной каталог);| Пользователь| Сбросить пароль пользователя|
| "Core Directory" (Основной каталог);| Пользователь| Add App Role Assignment Grant To User (Добавление назначения роли приложения пользователю)|
| "Core Directory" (Основной каталог);| Пользователь| Add Member To Role (Добавление участника роли)|
| "Core Directory" (Основной каталог);| Пользователь| Delete Application Password For User (Удаление пароля приложения для пользователя)|
| "Core Directory" (Основной каталог);| Пользователь| Update User Credentials (Обновление учетных данных пользователя)|
| "Core Directory" (Основной каталог);| Пользователь| Set User Manager (Настройка диспетчера пользователей)|
| "Core Directory" (Основной каталог);| Пользователь| Add Scoped Member To Role (Добавление участника с заданной областью в роль)|
| "Core Directory" (Основной каталог);| Группа| Удалить группу|
| "Core Directory" (Основной каталог);| Группа| Remove Owner From Group (Удаление владельца группы)|
| "Core Directory" (Основной каталог);| Группа| Update Group Settings (Обновление параметров группы)|
| "Core Directory" (Основной каталог);| Приложение| Add Owner To Application (Добавление владельца приложения)|
| "Core Directory" (Основной каталог);| Приложение| Отозвать согласие|
| "Core Directory" (Основной каталог);| Политика| Добавить политику|
| "Core Directory" (Основной каталог);| Устройство| Удалить устройство|
| "Self-service Password Management" (Самостоятельное управление паролями);| Пользователь| Blocked From Self-Service Password Reset (Блокировка от самостоятельного сброса пароля)|
| "Self-service Group Management" (Самостоятельное управление группами);| Группа| Request To Join A Group (Запрос на присоединение к группе)|
| "Self-service Group Management" (Самостоятельное управление группами);| Группа| Create Lifecycle Management Policy (Создание политики управления жизненным циклом)|
| "Self-service Group Management" (Самостоятельное управление группами);| Группа| Reject A Pending Request To Join A Group (Отклонение ожидающего запроса на присоединение к группе)|
| "Self-service Group Management" (Самостоятельное управление группами);| Группа| Cancel A Pending Request To Join A Group (Отмена ожидающего запроса на присоединение к группе)|
| "Self-service Group Management" (Самостоятельное управление группами);| Группа| Renew Group (Возобновление группы)|
| "Account Provisioning" (Подготовка учетных записей).| Приложение| экспорт.|
| "Account Provisioning" (Подготовка учетных записей).| Приложение| Другие|
| Invited Users (Приглашаемые пользователи)| Пользователь| Активация приглашения внешнего пользователя|
| Invited Users (Приглашаемые пользователи)| Пользователь| Viral User Creation (Создание вирусного пользователя)|
| Invited Users (Приглашаемые пользователи)| Пользователь| Assign External User To Application (Назначение внешнего пользователя для приложения)|




## <a name="audit-logs-shortcuts"></a>Ярлыки журналов аудита

Помимо **Azure Active Directory** портал Azure предоставляет две дополнительные точки входа для данных аудита:

- Пользователи и группы
- корпоративные приложения.

Полный список событий отчета аудита см. в разделе [Список событий отчета аудита](active-directory-reporting-audit-events.md#list-of-audit-report-events).


### <a name="users-and-groups-audit-logs"></a>Журналы аудита пользователей и групп

Отчеты аудита, касающиеся пользователей и групп, дают возможность ответить на такие вопросы:

- Обновления каких типов были применены к пользователям?

- Сколько пользователей было изменено?

- Сколько паролей было изменено?

- Что делал администратор в каталоге?

- Какие группы были добавлены?

- В каких группах произошли изменения членства?

- Изменены ли владельцы групп?

- Какие лицензии были назначены пользователю или группе?

Если нужно только просмотреть данные аудита, связанные с пользователями и группами, отфильтрованное представление вы можете найти, щелкнув элемент **Журналы аудита** в разделе **Действие** колонки **Пользователи и группы**. Для этой точки в качестве **типа ресурса действия** предварительно выбрано значение **Пользователи и группы**.

![Журналы аудита](./media/active-directory-reporting-activity-audit-logs/93.png "Журналы аудита")

### <a name="enterprise-applications-audit-logs"></a>Журналы аудита корпоративных приложений

Отчеты аудита, касающиеся приложений, дают возможность ответить на такие вопросы:

* Какие приложения были добавлены или обновлены?
* Какие приложения были удалены?
* Изменился ли участник-служба для приложения?
* Изменены ли имена приложений?
* Кто дал согласие на использование приложения?

Если нужно только просмотреть данные аудита, связанные с приложениями, отфильтрованное представление вы можете найти, щелкнув элемент **Журналы аудита** в разделе **Действие** колонки **Корпоративные приложения**. Для этой точки в качестве **типа ресурса действия** предварительно выбрано значение **Корпоративные приложения**.

![Журналы аудита](./media/active-directory-reporting-activity-audit-logs/134.png "Журналы аудита")

Вы также можете отфильтровать это представление по **группам** или **пользователям**.

![Журналы аудита](./media/active-directory-reporting-activity-audit-logs/25.png "Журналы аудита")


## <a name="next-steps"></a>Дальнейшие действия
См. статью [Руководство по отчетам Azure Active Directory](active-directory-reporting-guide.md).


