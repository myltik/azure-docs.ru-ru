<properties
	pageTitle="Azure AD Connect: интеграция локальных удостоверений с Azure Active Directory | Microsoft Azure"
	description="Azure AD Connect — это средство для интеграции локальной системы удостоверений, например Windows Server Active Directory, с Azure Active Directory и подключения пользователей к Office 365, Azure и тысячам приложений SaaS. В этом разделе рассматриваются функции и принципы работы Azure AD Connect. Кроме того, объясняется, как устанавливать и настраивать этот компонент."
    keywords="Введение в Azure AD Connect, обзор функций Azure AD Connect, что такое Azure AD Connect, установка Аctive Directory."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/29/2016"
	ms.author="andkjell;billmath"/>

# Интеграция локальных удостоверений с Azure Active Directory
Azure AD Connect — это средство для интеграции локальной системы удостоверений, например Windows Server Active Directory, с Azure Active Directory и подключения пользователей к Office 365, Azure и тысячам приложений SaaS. Этот раздел содержит полное руководство по подготовке и развертыванию необходимых компонентов, позволяющих конечным пользователям получать доступ к облачным службам с использованием того же удостоверения, которое они сегодня используют для доступа к существующим приложениям компании.

![Что такое Azure AD Connect?](./media/active-directory-aadconnect/arch.png)

## Почему Azure AD Connect?
Интеграция локальных каталогов с Azure AD помогает повысить продуктивность пользователей, так как используется единая идентификация для доступа к облачным и локальным ресурсам. Благодаря этой интеграции пользователи и организации получат следующие преимущества:

- Пользователи могут использовать единое удостоверение для доступа к локальным приложениям и облачным службам, например Office 365.

- Единое средство, обеспечивающее удобное развертывание для синхронизации и входа в систему.

- Предоставляет новые возможности для ваших сценариев. Azure AD Connect заменяет старые версии средств интеграции удостоверений, такие как DirSync и Azure AD Sync. Дополнительные сведения см. в статье со [сравнением средств интеграции для каталогов гибридных удостоверений](active-directory-hybrid-identity-design-considerations-tools-comparison.md).


### Принципы работы Azure AD Connect

Azure Active Directory Connect состоит из трех основных компонентов. Это службы синхронизации, службы федерации Active Directory (необязательный компонент) и компонент мониторинга, реализованный с помощью [Azure AD Connect Health](active-directory-aadconnect-health.md).

<center>![Azure AD Connect Stack](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png) </center>

- Синхронизация — эта часть состоит из компонентов и функций, ранее выпущенных как [Dirsync и Azure AD Sync](active-directory-hybrid-identity-design-considerations-tools-comparison.md). Эта часть отвечает за создание пользователей и групп. Она также отвечает за согласование сведений о пользователях и группах в локальной среде и в облаке.
- AD FS — это необязательная часть Azure AD Connect, которая может использоваться для настройки гибридной среды с помощью локальной инфраструктуры AD FS. Ее можно применять при сложном развертывании, в котором используется единый вход с присоединением к домену, принудительное применение политики входа AD и смарт-карты или сторонняя многофакторная проверка подлинности.
- Мониторинг работоспособности — компонент Azure AD Connect Health реализует надежный мониторинг и предоставляет центральное расположение на портале Azure для просмотра связанных действий. Дополнительные сведения см. в разделе [Azure Active Directory Connect Health](active-directory-aadconnect-health.md).

## Установка Azure AD Connect

Вы можете загрузить Azure AD Connect из [Центра загрузки Майкрософт](http://go.microsoft.com/fwlink/?LinkId=615771).


| Решение | Сценарий |
| ----- | ----- |
| Предварительная подготовка: [необходимые условия для Azure AD Connect](active-directory-aadconnect-prerequisites.md) | <li>Действия, которые нужно выполнить перед началом установки Azure AD Connect.</li> |
| [Стандартные параметры](active-directory-aadconnect-get-started-express.md) | <li>Рекомендуемый вариант по умолчанию при наличии одного леса AD.</li> <li>Пользователи входят в систему с одним и тем же паролем, используя синхронизацию паролей.</li>
| [Настраиваемые параметры](active-directory-aadconnect-get-started-custom.md) | <li>Используется при наличии нескольких лесов. Поддерживает многие локальные [топологии](active-directory-aadconnect-topologies.md).</li> <li>Настройка режима входа, например ADFS для федерации или использования стороннего поставщика удостоверений.</li> <li>Настройка функций синхронизации, таких как фильтрация и обратная запись.</li>
| [Обновление из DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>Если есть уже работающий сервер DirSync.</li>
| [Переход с Azure AD Sync на Azure AD Connect](active-directory-aadconnect-upgrade-previous-version.md)| <li>Существует несколько разных способов на выбор.</li>


[После установки](active-directory-aadconnect-whats-next.md) следует проверить правильность работы системы и назначить пользователям лицензии.

### Следующие шаги по установке Azure AD Connect

| Раздел | |
| --------- | --------- |
| Загрузка Azure AD Connect | [Загрузка Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) |
| Установка с помощью стандартных параметров | [Экспресс-установка Azure AD Connect](active-directory-aadconnect-get-started-express.md) |
| Установка с помощью настроенных параметров | [Выборочная установка Azure AD Connect](active-directory-aadconnect-get-started-custom.md) |
| Обновление из DirSync | [Обновление из средства синхронизации Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| Действия после установки | [Проверка установки и назначение лицензий ](active-directory-aadconnect-whats-next.md) |

### Дополнительные сведения об установке Azure AD Connect

Также требуется подготовиться к [рабочим](active-directory-aadconnectsync-operations.md) вопросам. Может потребоваться наличие резервного сервера, на который можно будет легко перейти в случае [аварии](active-directory-aadconnectsync-operations.md#disaster-recovery). Если планируются частые изменения конфигурации, следует предусмотреть сервер для [резервного режима](active-directory-aadconnectsync-operations.md#staging-mode).

| Раздел | |
| --------- | --------- |
| Поддерживаемые топологии | [Топологии Azure AD Connect.](active-directory-aadconnect-topologies.md) |
| Принципы проектирования | [Принципы проектирования Azure AD Connect](active-directory-aadconnect-design-concepts.md) |
| Учетные записи, используемые для установки | [Дополнительные сведения об учетных данных и разрешениях Azure AD Connect](active-directory-aadconnect-accounts-permissions.md) |
| Операционное планирование | [Службы синхронизации Azure AD Connect: рабочие задачи и рекомендации](active-directory-aadconnectsync-operations.md) |
| Параметры входа пользователя | [Параметры входа в Azure AD Connect](active-directory-aadconnect-user-signin.md) |

## Настройка функций
Azure AD Connect поставляется с несколькими функциями, которые можно при необходимости включить или они включены по умолчанию. Для некоторых функций иногда может потребоваться дополнительная конфигурация в некоторых сценариях и топологиях.

[Фильтрация](active-directory-aadconnectsync-configure-filtering.md) используется, если требуется ограничить количество объектов, синхронизируемых с Azure AD. По умолчанию синхронизируются все пользователи, контакты, группы и компьютеры с Windows 10, но их количество можно ограничить на основе доменов, подразделений или атрибутов.

[Синхронизация паролей](active-directory-aadconnectsync-implement-password-synchronization.md) синхронизирует хэш пароля из Active Directory в Azure AD. Это позволяет пользователям использовать один и тот же пароль и локально, и в облаке, управляя им из одного расположения. Поскольку синхронизация паролей будет использовать локальную службу Active Directory, она также позволит вам применять собственную политику паролей.

[Компонент обратной записи паролей](active-directory-passwords-getting-started.md) позволит вашим пользователям изменять и сбрасывать пароли в облаке, а также применять вашу локальную политику паролей.

[Обратная запись устройств](active-directory-aadconnect-feature-device-writeback.md) позволит записать устройство, зарегистрированное в Azure AD, в локальную службу Active Directory, чтобы его можно было использовать для условного доступа.

Функция [предотвращения случайного удаления](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) включена по умолчанию и защитит ваш облачный каталог от множества одновременных удалений. По умолчанию она позволяет 500 операций удаления на один запуск. Этот параметр можно изменить с учетом размера организации.

[Автоматическое обновление](active-directory-aadconnect-feature-automatic-upgrade.md), которое включено по умолчанию для установок со стандартными параметрами, гарантирует наличие последней версии Azure AD Connect.

### Дальнейшие действия по настройке функций

| Раздел | |
| --------- | --------- |
| Настройка фильтрации | [Синхронизация Azure AD Connect: настройка фильтрации](active-directory-aadconnectsync-configure-filtering.md) |
| Синхронизация паролей | [Службы синхронизации Azure AD Connect: реализация синхронизации паролей](active-directory-aadconnectsync-implement-password-synchronization.md) |
| Обратная запись паролей | [Приступая к работе с компонентами управления паролями](active-directory-passwords-getting-started.md) |
| Обратная запись устройств | [Включение обратной записи устройств в службе Azure AD Connect](active-directory-aadconnect-feature-device-writeback.md) |
| Предотвращение случайного удаления | [Синхронизация Azure AD Connect: предотвращение случайного удаления](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |
| Автоматическое обновление | [Azure AD Connect: автоматическое обновление](active-directory-aadconnect-feature-automatic-upgrade.md)|

## Настройка синхронизации Azure AD Connect
Синхронизация Azure AD Connect поставляется с конфигурацией по умолчанию, которая ориентирована на работу с большинством заказчиков и топологий. Однако всегда существуют ситуации, когда конфигурация по умолчанию не будет работать и должна быть скорректирована. Поддерживается внесение изменений, документированных в этом разделе и связанных статьях.

Если вы не работали ранее с топологией синхронизации, вам необходимо ознакомиться с основными сведениями и используемыми терминами, описанными в [технических концепциях](active-directory-aadconnectsync-technical-concepts.md). Azure AD Connect является следующим этапом развития MIIS2003, ILM2007 и FIM2010. Даже если некоторые элементы идентичны, многое также изменилось.

[Конфигурация, используемая по умолчанию](active-directory-aadconnectsync-understanding-default-configuration.md), может включать несколько лесов. В таких топологиях объект пользователя может быть представлен как контакт в другом лесу. У пользователя также может быть связанный почтовый ящик в другом лесу ресурсов. Поведение конфигурации по умолчанию описано в разделе [пользователей и контактов](active-directory-aadconnectsync-understanding-users-and-contacts.md).

Модель конфигурации в синхронизации называется [декларативной подготовкой](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md). Потоки дополнительных атрибутов используют [функции](active-directory-aadconnectsync-functions-reference.md) для выражения преобразований атрибутов. Всю конфигурацию можно просмотреть и изучить с помощью средств, поставляемых вместе с Azure AD Connect. Если в конфигурацию необходимо внести изменения, убедитесь, что вы придерживаетесь [рекомендаций](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), облегчающих переход на новые версии по мере их доступности.

### Следующие действия по настройке синхронизации Azure AD Connect

| Раздел | |
| --------- | --------- |
| Все статьи о синхронизации Azure AD Connect | [Службы синхронизации Azure AD Connect](active-directory-aadconnectsync-whatis.md) |
| Технические концепции | [Синхронизация Azure AD Connect: технические концепции](active-directory-aadconnectsync-technical-concepts.md) |
| Общие сведения о конфигурации по умолчанию | [Службы синхронизации Azure AD Connect: общие сведения о конфигурации по умолчанию](active-directory-aadconnectsync-understanding-default-configuration.md) |
| Общее представление о пользователях и контактах | [Синхронизация Azure AD Connect: общее представление о пользователях и контактах](active-directory-aadconnectsync-understanding-users-and-contacts.md) |
| Декларативная подготовка | [Azure AD Connect Sync: общие сведения о выражениях декларативной подготовки](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| Изменение конфигурации по умолчанию | [Рекомендации по изменению конфигурации по умолчанию](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |

## Дополнительные сведения и ссылки

| Раздел | |
| --------- | --------- |
| Журнал версий | [Журнал версий](active-directory-aadconnect-version-history.md) |
| Сравнение DirSync, Azure ADSync и Azure AD Connect | [Сравнение инструментов интеграции каталогов](active-directory-hybrid-identity-design-considerations-tools-comparison.md) |
| Синхронизированные атрибуты | [Синхронизированные атрибуты](active-directory-aadconnectsync-attributes-synchronized.md) |
| Мониторинг с помощью Azure AD Connect Health | [Azure AD Connect Health](active-directory-aadconnect-health.md) |
| Часто задаваемые вопросы | [Azure AD Connect: вопросы и ответы](active-directory-aadconnect-faq.md) |


**Дополнительные ресурсы**


Презентация по интеграции локальных и облачных каталогов с конференции Ignite 2015.

[AZURE.VIDEO microsoft-ignite-2015-extending-on-premises-directories-to-the-cloud-made-easy-with-azure-active-directory-connect]

<!---HONumber=AcomDC_0302_2016-->