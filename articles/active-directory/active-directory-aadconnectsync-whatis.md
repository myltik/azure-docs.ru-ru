<properties
	pageTitle="Службы синхронизации Azure AD Connect: общие сведений о синхронизации и ее настройка | Microsoft Azure"
	description="В этой статье описываются принципы работы и настройка служб синхронизации Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="markusvi;andkjell"/>


# Службы синхронизации Azure AD Connect: общие сведений о синхронизации и ее настройка
Службы синхронизации Azure Active Directory Connect (службы синхронизации Azure AD Connect) — это основной компонент Azure AD Connect. Этот компонент отвечает за все операции, относящиеся к синхронизации данных удостоверения между локальной средой и Azure AD в облаке. Служба синхронизации Azure AD Connect — это преемник таких решений, как DirSync, Azure AD Sync и Forefront Identity Manager с настроенным соединителем Azure Active Directory.

В этом разделе описывается **служба синхронизации Azure AD Connect** (также называемая **модулем синхронизации**) и приводятся ссылки на связанные разделы. Ссылки на ресурсы, посвященные Azure AD Connect, см. в статье [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

## Разделы, посвященные службе синхронизации Azure AD Connect

| Раздел | Содержание и целевая аудитория |
| ----- | ----- |
| **Знакомство со службой синхронизации Azure AD Connect** ||
| [Understanding the architecture](active-directory-aadconnectsync-understanding-architecture.md) (Основные сведения об архитектуре) | Пользователи без опыта работы с модулем синхронизации, которые хотят узнать о ее архитектуре и используемых терминах. |
| [Технические концепции](active-directory-aadconnectsync-technical-concepts.md) | Сокращенная версия раздела с описанием архитектуры, содержащая краткое описание используемых терминов. |
| [Топологии Azure AD Connect.](active-directory-aadconnect-topologies.md) | Описание разных топологий и сценариев, поддерживаемых модулем синхронизации. |
| **Настраиваемая конфигурация** ||
| [Общие сведения о конфигурации по умолчанию](active-directory-aadconnectsync-understanding-default-configuration.md)| Описание стандартных правил и конфигурации по умолчанию. Также описывается, как правила используются при работе со стандартными сценариями. |
| [Синхронизация Azure AD Connect: общее представление о пользователях и контактах](active-directory-aadconnectsync-understanding-users-and-contacts.md) | Продолжение предыдущего раздела с описанием того, как конфигурация для пользователей и контактов используется при работе в среде с несколькими лесами. |
| [Знакомство с выражениями декларативной подготовки.](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) | Подробное описание того, как модель конфигурации и синтаксис используются в языке выражений. |
| [Рекомендации по изменению конфигурации по умолчанию](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) | Описано, как изменить стандартную конфигурацию, чтобы приспособить ее к сценарию или требованиям (на основе сведений, описанных в предыдущих разделах). |
| [Службы синхронизации Azure AD Connect: настройка фильтрации](active-directory-aadconnectsync-configure-filtering.md) | Описано, как выбирать объекты для синхронизации с Azure AD и приведены пошаговые рекомендации по их настройке. |
| **Функции** ||
| [Службы синхронизации Azure AD Connect: реализация синхронизации паролей](active-directory-aadconnectsync-implement-password-synchronization.md) | Описано, как работает синхронизация паролей, как реализовать и использовать эту функцию, а также как устранять связанные с ней неполадки. |
| [Предотвращение случайного удаления](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) | Описано, как работает функция, которая позволяет *предотвратить случайное удаление* компонентов, и приведены рекомендации по ее настройке. |
| **Операции** ||
| [Службы синхронизации Azure AD Connect: рабочие задачи и рекомендации](active-directory-aadconnectsync-operations.md) | Описание рабочих задач, например аварийного восстановления. |
| **Дополнительные сведения и ссылки** ||
| [Порты](active-directory-aadconnect-ports.md) | Список портов, которые необходимо открыть между модулем синхронизации, локальными каталогами и Azure AD. |
| [Службы синхронизации Azure AD Connect: атрибуты, синхронизируемые с Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) | Список всех атрибутов, которые синхронизируются между локальной средой AD и Azure AD. |
| [Справочник по функциям](active-directory-aadconnectsync-functions-reference.md) | Список всех функций, доступных в рамках декларативной подготовки. |

## Дополнительные ресурсы

* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0128_2016-->