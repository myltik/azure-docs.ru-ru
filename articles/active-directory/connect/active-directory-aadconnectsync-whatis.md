---
title: 'Синхронизация Azure AD Connect: общие сведения о синхронизации и ее настройка | Документация Майкрософт'
description: В этой статье описываются принципы работы и настройка служб синхронизации Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.component: hybrid
ms.author: markvi
ms.openlocfilehash: 6b2724f4c9511d606ab8eeac2dedea8759283883
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595263"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Службы синхронизации Azure AD Connect: общие сведений о синхронизации и ее настройка
Службы синхронизации Azure Active Directory Connect (службы синхронизации Azure AD Connect) являются основным компонентом Azure AD Connect. Они отвечают за все операции, относящиеся к синхронизации идентификационных данных между локальной средой и Azure AD. Служба синхронизации Azure AD Connect — это преемник таких решений, как DirSync, Azure AD Sync и Forefront Identity Manager с настроенным соединителем Azure Active Directory.

В этой статье описывается **служба синхронизации Azure AD Connect** (также называемая **модулем синхронизации**) и приводятся ссылки на другие статьи на эту тему. Ссылки на ресурсы, посвященные Azure AD Connect, приведены в статье [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

Служба синхронизации состоит из двух компонентов — локального компонента **синхронизации Azure AD Connect** и компонента на стороне службы в Azure AD, который называется **службой синхронизации Azure AD Connect**.

## <a name="azure-ad-connect-sync-topics"></a>Статьи, имеющие отношение к службе синхронизации Azure AD Connect
| Раздел | Содержание и целевая аудитория |
| --- | --- |
| **Знакомство со службой синхронизации Azure AD Connect** | |
| [Understanding the architecture (Основные сведения об архитектуре)](active-directory-aadconnectsync-understanding-architecture.md) |Для пользователей без опыта работы с модулем синхронизации, которые хотят узнать о его архитектуре и используемых терминах. |
| [Технические концепции](active-directory-aadconnectsync-technical-concepts.md) |Сокращенная версия статьи об архитектуре с краткими пояснениями по используемым терминам. |
| [Топологии Azure AD Connect.](active-directory-aadconnect-topologies.md) |Описание разных топологий и сценариев, поддерживаемых модулем синхронизации. |
| **Настраиваемая конфигурация** | |
| [Повторный запуск мастера установки](active-directory-aadconnectsync-installation-wizard.md) |Объясняет, какие есть варианты действий при повторном запуске мастера установки Azure AD Connect. |
| [Знакомство с декларативной подготовкой](active-directory-aadconnectsync-understanding-declarative-provisioning.md) |Описание модели конфигурации, которая называется декларативной подготовкой. |
| [Знакомство с выражениями декларативной подготовки.](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |Описание синтаксиса для выражений языка, используемого в декларативной подготовке. |
| [Общие сведения о конфигурации по умолчанию](active-directory-aadconnectsync-understanding-default-configuration.md) |Описание стандартных правил и конфигурации по умолчанию. Также описывается, как правила используются при работе со стандартными сценариями. |
| [Синхронизация Azure AD Connect: общее представление о пользователях и контактах](active-directory-aadconnectsync-understanding-users-and-contacts.md) |Это продолжение предыдущей статьи, где описано взаимодействие конфигураций для пользователей и контактов, в частности в среде с несколькими лесами. |
| [How to make a change to the default configuration (Изменение конфигурации по умолчанию)](active-directory-aadconnectsync-change-the-configuration.md) |Описывает типичные изменения конфигурации для потоков атрибутов. |
| [Рекомендации по изменению конфигурации по умолчанию](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |Ограничения поддержки и изменение стандартной конфигурации. |
| [Настройка фильтрации](active-directory-aadconnectsync-configure-filtering.md) |Описывает возможности установки ограничений для синхронизации объектов с Azure AD. Содержит пошаговые рекомендации по настройке этих ограничений. |
| **Функции и сценарии** | |
| [Предотвращение случайного удаления](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |Описание функции *предотвращения случайного удаления* и рекомендации по ее настройке. |
| [Планировщик](active-directory-aadconnectsync-feature-scheduler.md) |Описывает встроенный планировщик, который импортирует, синхронизирует и экспортирует данные. |
| [Реализация синхронизации хэшированных паролей в службе синхронизации Azure AD Connect](active-directory-aadconnectsync-implement-password-hash-synchronization.md) |Описано, как работает синхронизация паролей, как реализовать и использовать эту функцию, а также как устранять связанные с ней неполадки. |
| [Обратная запись устройств](active-directory-aadconnect-feature-device-writeback.md) |Описывается, как работает обратная запись устройства в Azure AD Connect. |
| [Расширения каталогов](active-directory-aadconnectsync-feature-directory-extensions.md) |Описывается, как расширить схему Azure AD с помощью собственных пользовательских атрибутов. |
| [Office 365 PreferredDataLocation](active-directory-aadconnectsync-feature-preferreddatalocation.md) |Объясняется, как разместить ресурсы Office 365 пользователя в том же регионе, в котором находится пользователь. |
| **Служба синхронизации** | |
| [Функции службы синхронизации Azure AD Connect](active-directory-aadconnectsyncservice-features.md) |Описание компонента на стороне службы синхронизации и действий по изменению параметров синхронизации в Azure AD. |
| [Устойчивость повторяющихся атрибутов](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) |Описание включения и применения функции устойчивости значений повторяющихся атрибутов **userPrincipalName** и **proxyAddresses**. |
| **Операции и пользовательский интерфейс** | |
| [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) |Описание пользовательского интерфейса Synchronization Service Manager, включая вкладки [Operations](active-directory-aadconnectsync-service-manager-ui-operations.md) (Операции), [Connectors](active-directory-aadconnectsync-service-manager-ui-connectors.md) (Соединители), [Metaverse Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) (Конструктор метавселенной) и [Metaverse Search](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) (Поиск в метавселенной). |
| [Службы синхронизации Azure AD Connect: рабочие задачи и рекомендации](active-directory-aadconnectsync-operations.md) |Описание рабочих задач, например аварийного восстановления. |
| **Практическое руководство.** | |
| [Сброс учетной записи Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md) |Как сбросить учетные данные учетной записи службы, используемые для подключения службы синхронизации Azure AD Connect к Azure AD. |
| **Дополнительные сведения и ссылки** | |
| [Порты](active-directory-aadconnect-ports.md) |Список портов, которые необходимо открыть между модулем синхронизации, локальными каталогами и Azure AD. |
| [Службы синхронизации Azure AD Connect: атрибуты, синхронизируемые с Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) |Список всех атрибутов, которые синхронизируются между локальной средой AD и Azure AD. |
| [Справочник по функциям](active-directory-aadconnectsync-functions-reference.md) |Список всех функций, доступных в рамках декларативной подготовки. |

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)
