---
title: Подключение Active Directory к Azure Active Directory | Документация Майкрософт
description: Azure AD Connect интегрирует локальные каталоги с Azure Active Directory. Таким образом вы сможете предоставить пользователям возможность получать доступ с использованием одного удостоверения для Office 365, Azure и SaaS, интегрированных с Azure AD.
keywords: Введение в Azure AD Connect, обзор функций Azure AD Connect, что такое Azure AD Connect, установка Аctive Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6d866a6b068e9f6b8a46e53d60653d55e35a2ffd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594029"
---
# <a name="integrate-your-on-premises-directories-with-azure-active-directory"></a>Интеграция локальных каталогов с Azure Active Directory
Azure AD Connect интегрирует локальные каталоги с Azure Active Directory. Таким образом вы сможете предоставить пользователям возможность получать доступ с использованием одного удостоверения для приложений Office 365, Azure и программного обеспечения как услуги (SaaS), интегрированных с Azure AD. В этом разделе описаны шаги планирования, развертывания и использования. Здесь представлена коллекция ссылок на статьи, относящиеся к этой области.

> [!IMPORTANT]
> [Azure AD Connect — это лучший способ для подключения локального каталога к Azure AD и Office 365. Если вы используете Windows Azure Active Directory Sync (DirSync) или Azure AD Sync, настало время перейти на Azure AD Connect, так как эти продукты являются нерекомендуемыми и не поддерживаются с 13 апреля 2017 года.](active-directory-aadconnect-dirsync-deprecated.md)  См. также:



> 
> - Синхронизация пользователей в Azure AD — это **бесплатная функция**, для использования которой подписка не требуется.
>- Синхронизированным пользователям *лицензии* **автоматически не предоставляются**. Администраторы по-прежнему имеют полный контроль над процессом назначения лицензий. 
> - Корпорация Майкрософт рекомендует ИТ-администраторам синхронизировать всех своих пользователей. Это не только откроет пользователям доступ к любому интегрированному ресурсу Azure AD, но также предоставит ИТ-администраторам более подробные сведения о том, к каким приложениям их пользователи осуществляют доступ. 

![Что такое Azure AD Connect?](media/active-directory-aadconnect/arch.png)

## <a name="why-use-azure-ad-connect"></a>Почему Azure AD Connect?
Интеграция локальных каталогов с Azure AD помогает повысить продуктивность пользователей, так как используется единая идентификация для доступа к облачным и локальным ресурсам. Пользователи и организации получат следующие преимущества:

* Пользователи могут использовать единое удостоверение для доступа к локальным приложениям и облачным службам, например Office 365.
* Единое средство, обеспечивающее удобное развертывание для синхронизации и входа в систему.
* Предоставляет новые возможности для ваших сценариев. Azure AD Connect заменяет старые версии средств интеграции удостоверений, такие как DirSync и Azure AD Sync. Дополнительные сведения см. в статье [Сравнение инструментов интеграции каталогов гибридной идентификации](../active-directory-hybrid-identity-design-considerations-tools-comparison.md).

### <a name="how-azure-ad-connect-works"></a>Принципы работы Azure AD Connect
Azure Active Directory Connect состоит из трех основных компонентов: службы синхронизации, необязательного компонента служб федерации Active Directory и компонента для мониторинга, который называется [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).

<center>![Стек Azure AD Connect](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png)
</center>

* Синхронизация. Cлужбы синхронизации отвечают за создание пользователей, групп и других объектов. Они также отвечают за согласованность сведений о пользователях и группах в локальной среде и в облаке.
* AD FS. Это необязательная часть Azure AD Connect, которую можно использовать для настройки гибридной среды с помощью локальной инфраструктуры AD FS. Ее можно применять при сложном развертывании, например если нужно обеспечить единый вход с присоединением к домену, принудительное применение политик входа AD и использование смарт-карты или сторонней MFA.
* Мониторинг работоспособности — компонент Azure AD Connect Health реализует надежный мониторинг и предоставляет центральное расположение на портале Azure для просмотра связанных действий. Дополнительные сведения см. в статье [Мониторинг локальной инфраструктуры идентификации и служб синхронизации в облаке](../connect-health/active-directory-aadconnect-health.md).

## <a name="install-azure-ad-connect"></a>Установка Azure AD Connect

> [!IMPORTANT]
> Мы поддерживаем изменение или использование служб синхронизации Azure AD Connect только в контексте официально задокументированных действий. Любые иные действия могут привести к несогласованному или неподдерживаемому состоянию служб синхронизации Azure AD Connect. Для таких развертываний Майкрософт не предоставляет техническую поддержку.

Вы можете загрузить Azure AD Connect из [Центра загрузки Майкрософт](http://go.microsoft.com/fwlink/?LinkId=615771).

| Решение | Сценарий |
| --- | --- |
| Перед началом работы: [оборудование и предварительные требования](active-directory-aadconnect-prerequisites.md) |<li>Шаги, которые нужно выполнить до установки Azure AD Connect.</li> |
| [Стандартные параметры](active-directory-aadconnect-get-started-express.md) |<li>Рекомендуемый вариант при наличии AD с одним лесом.</li> <li>Пользователи входят в систему с одним и тем же паролем, используя синхронизацию паролей.</li> |
| [Настраиваемые параметры](active-directory-aadconnect-get-started-custom.md) |<li>Используется при наличии нескольких лесов. Поддерживает многие локальные [топологии](active-directory-aadconnect-topologies.md).</li> <li>Настройка режима входа (например, с помощью сквозной аутентификации, AD FS для федерации или стороннего поставщика удостоверений).</li> <li>Настройка функций синхронизации, таких как фильтрация и обратная запись.</li> |
| [Обновление из DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>Используется при наличии уже работающего сервера DirSync.</li> |
| [Переход с Azure AD Sync на Azure AD Connect](active-directory-aadconnect-upgrade-previous-version.md) |<li>Есть несколько разных способов на выбор.</li> |

[После установки](active-directory-aadconnect-whats-next.md) следует проверить правильность работы системы и назначить пользователям лицензии.

### <a name="next-steps-to-install-azure-ad-connect"></a>Следующие шаги по установке Azure AD Connect
|Раздел |Ссылка|  
| --- | --- |
|Загрузка Azure AD Connect | [Загрузка Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Установка с помощью стандартных параметров | [Экспресс-установка Azure AD Connect](./active-directory-aadconnect-get-started-express.md)|
|Установка с помощью настроенных параметров | [Выборочная установка Azure AD Connect](./active-directory-aadconnect-get-started-custom.md)|
|Обновление из DirSync | [Azure AD Connect: обновление DirSync](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|После установки | [Проверка установки и назначение лицензий ](active-directory-aadconnect-whats-next.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Дополнительные сведения об установке Azure AD Connect
Также требуется подготовиться к [рабочим](active-directory-aadconnectsync-operations.md) вопросам. Может потребоваться наличие резервного сервера, на который можно будет легко перейти в случае [аварии](active-directory-aadconnectsync-operations.md#disaster-recovery). Если планируется часто изменять конфигурацию, следует предусмотреть сервер для [промежуточного режима](active-directory-aadconnectsync-operations.md#staging-mode) .

|Раздел |Ссылка|  
| --- | --- |
|Поддерживаемые топологии | [Топологии Azure AD Connect.](active-directory-aadconnect-topologies.md)|
|Принципы проектирования | [Принципы проектирования Azure AD Connect](active-directory-aadconnect-design-concepts.md)|
|Учетные записи, используемые для установки | [Azure AD Connect: учетные записи и разрешения](./active-directory-aadconnect-accounts-permissions.md)|
|Операционное планирование | [Службы синхронизации Azure AD Connect: рабочие задачи и рекомендации](active-directory-aadconnectsync-operations.md)|
|Параметры входа пользователя | [Параметры входа в Azure AD Connect](active-directory-aadconnect-user-signin.md)|

## <a name="configure-sync-features"></a>Настройка функций синхронизации
Azure AD Connect поставляется с несколькими функциями, которые можно при необходимости включить или они включены по умолчанию. В некоторых сценариях и топологиях может потребоваться дополнительная конфигурация некоторых функций.

[Фильтрация](active-directory-aadconnectsync-configure-filtering.md) используется, если требуется ограничить количество объектов, синхронизируемых с Azure AD. По умолчанию все пользователи, контакты, группы и компьютеры с ОС Windows 10 синхронизируются. Фильтрацию можно изменить в зависимости от доменов, подразделений и атрибутов.

[Синхронизация хэша паролей](active-directory-aadconnectsync-implement-password-hash-synchronization.md) подразумевает синхронизацию хэша паролей из Active Directory в Azure AD. Пользователи могут использовать один и тот же пароль локально и в облаке, но управление им осуществляется только из одного расположения. Так как при синхронизации паролей в качестве главного источника используется локальная служба Active Directory, вы также можете применить собственную политику паролей.

[Компонент обратной записи паролей](../authentication/quickstart-sspr.md) позволит вашим пользователям изменять и сбрасывать пароли в облаке, а также применять вашу локальную политику паролей.

[Обратная запись устройств](active-directory-aadconnect-feature-device-writeback.md) позволит записать устройство, зарегистрированное в Azure AD, в локальную службу Active Directory, чтобы его можно было использовать для условного доступа.

Функция [предотвращения случайного удаления](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) включена по умолчанию и защищает облачный каталог от множества одновременных удалений. По умолчанию она позволяет сделать 500 удалений за сеанс. Этот параметр можно изменить в зависимости от размера вашей организации.

[Автоматическое обновление](active-directory-aadconnect-feature-automatic-upgrade.md) , которое включено по умолчанию для установок со стандартными параметрами, гарантирует использование последней версии Azure AD Connect.

### <a name="next-steps-to-configure-sync-features"></a>Дальнейшие действия по настройке функций синхронизации
|Раздел |Ссылка|  
| --- | --- |
|Настройка фильтрации | [Синхронизация Azure AD Connect: настройка фильтрации](active-directory-aadconnectsync-configure-filtering.md)|
|Синхронизация хэша паролей | [Служба синхронизации Azure AD Connect: реализация синхронизации хэша паролей](active-directory-aadconnectsync-implement-password-hash-synchronization.md)|
|Компонент обратной записи паролей | [Приступая к работе с компонентами управления паролями](../authentication/quickstart-sspr.md)|
|Обратная запись устройств | [Включение обратной записи устройств в службе Azure AD Connect](active-directory-aadconnect-feature-device-writeback.md)|
|предотвращения случайного удаления | [Синхронизация Azure AD Connect: предотвращение случайного удаления](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)|
|Автоматическое обновление | [Azure AD Connect: автоматическое обновление](active-directory-aadconnect-feature-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Настройка синхронизации Azure AD Connect
Синхронизация Azure AD Connect поставляется с конфигурацией по умолчанию, ориентированной на работу с большинством заказчиков и топологий. Тем не менее, возникают ситуации, когда конфигурация по умолчанию не будет работать и ее необходимо скорректировать. Поддерживается внесение изменений, документированных в этом разделе и связанных статьях.

Если вы не работали ранее с топологией синхронизации, вам необходимо ознакомиться с основными сведениями и используемыми терминами, описанными в [технических концепциях](active-directory-aadconnectsync-technical-concepts.md). Azure AD Connect является следующим этапом развития MIIS2003, ILM2007 и FIM2010. Даже если некоторые элементы идентичны, многое также изменилось.

[Конфигурация, используемая по умолчанию](active-directory-aadconnectsync-understanding-default-configuration.md) , может включать несколько лесов. В таких топологиях объект пользователя может быть представлен как контакт в другом лесу. У пользователя также может быть связанный почтовый ящик в другом лесу ресурсов. Поведение конфигурации по умолчанию описано в разделе [пользователей и контактов](active-directory-aadconnectsync-understanding-users-and-contacts.md).

Модель конфигурации в синхронизации называется [декларативной подготовкой](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md). Потоки дополнительных атрибутов используют [функции](active-directory-aadconnectsync-functions-reference.md) для выражения преобразований атрибутов. Всю конфигурацию можно просмотреть и изучить с помощью средств, поставляемых вместе с Azure AD Connect. Если в конфигурацию необходимо внести изменения, убедитесь, что вы придерживаетесь [рекомендаций](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) , упрощающих переход на новые выпуски.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Следующие действия по настройке синхронизации Azure AD Connect
|Раздел |Ссылка|  
| --- | --- |
|Все статьи о синхронизации Azure AD Connect | [Службы синхронизации Azure AD Connect](active-directory-aadconnectsync-whatis.md)|
|технических концепциях | [Синхронизация Azure AD Connect: технические концепции](active-directory-aadconnectsync-technical-concepts.md)|
|Общие сведения о конфигурации по умолчанию | [Службы синхронизации Azure AD Connect: общие сведения о конфигурации по умолчанию](active-directory-aadconnectsync-understanding-default-configuration.md)|
|Общее представление о пользователях и контактах | [Синхронизация Azure AD Connect: общее представление о пользователях и контактах](active-directory-aadconnectsync-understanding-users-and-contacts.md)|
|декларативной подготовкой | [Azure AD Connect Sync: общие сведения о выражениях декларативной подготовки](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)|
|Изменение конфигурации по умолчанию | [Рекомендации по изменению конфигурации по умолчанию](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>Настройка функций федерации

Служба Azure AD Connect предоставляет несколько функций, которые упрощают федерацию с Azure AD с помощью AD FS и управление доверием федерации. Она поддерживает AD FS в Windows Server 2012 R2 или более поздней версии.

[Обновите SSL-сертификат фермы AD FS](active-directory-aadconnectfed-ssl-update.md), даже если вы не используете Azure AD Connect для управления доверием федерации.

[Добавьте сервер AD FS](active-directory-aadconnect-federation-management.md#addadfsserver) в ферму, чтобы расширить ее в соответствии с потребностями.

[Восстановите отношение доверия](active-directory-aadconnect-federation-management.md#repairthetrust) с Azure AD несколькими щелчками мыши.

В AD FS можно настроить поддержку [нескольких доменов](active-directory-aadconnect-multiple-domains.md). Например, у вас может быть несколько доменов верхнего уровня, которые необходимо использовать для федерации.

Если на сервере AD FS не настроено автоматическое обновление сертификатов из Azure AD или используется решение, отличное от AD FS, при необходимости [обновления сертификатов](active-directory-aadconnect-o365-certs.md) появится соответствующее уведомление.

### <a name="next-steps-to-configure-federation-features"></a>Дальнейшие действия по настройке функций федерации
|Раздел |Ссылка|  
| --- | --- |
|Все статьи, посвященные AD FS | [Azure AD Connect и федерация](active-directory-aadconnectfed-whatis.md)|
|Настройка служб AD FS с поддоменами | [Поддержка нескольких доменов для федерации с Azure AD](active-directory-aadconnect-multiple-domains.md)|
|Управление фермой AD FS | [Управление службами федерации Active Directory и их настройка с помощью Azure AD Connect](active-directory-aadconnect-federation-management.md)|
|Обновление сертификатов федерации вручную | [Обновление сертификатов федерации для Office 365 и Azure AD](active-directory-aadconnect-o365-certs.md)|

## <a name="more-information-and-references"></a>Дополнительные сведения и ссылки
|Раздел |Ссылка|  
| --- | --- |
|Журнал версий | [Журнал версий](active-directory-aadconnect-version-history.md)|
|Сравнение DirSync, Azure ADSync и Azure AD Connect | [Сравнение инструментов интеграции каталогов](../active-directory-hybrid-identity-design-considerations-tools-comparison.md)|
|Список совместимости решений, отличных от AD FS, для Azure AD | [Список совместимости с федерацией Azure AD](active-directory-aadconnect-federation-compatibility.md)|
|Настройка IdP SAML 2.0|[Использование поставщика удостоверений (IdP) SAML 2.0 для единого входа](active-directory-aadconnect-federation-saml-idp.md)|
|Синхронизированные атрибуты | [Синхронизированные атрибуты](active-directory-aadconnectsync-attributes-synchronized.md)|
|Мониторинг с помощью Azure AD Connect Health | [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)|
|Часто задаваемые вопросы | [Azure AD Connect: вопросы и ответы](active-directory-aadconnect-faq.md)|

**Дополнительные ресурсы**

Презентация по интеграции локальных и облачных каталогов с конференции Ignite 2015.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3862/player]
> 
> 

