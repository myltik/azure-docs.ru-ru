---
title: Обзор службы Enterprise State Roaming | Документация Майкрософт
description: Здесь содержится информация о параметрах Enterprise State Roaming для устройств Windows. Служба Enterprise State Roaming представляет собой единое решение для всех устройств Windows и сокращает процесс настройки нового устройства.
services: active-directory
keywords: что такое Enterprise State Roaming, корпоративная синхронизация, облако Windows
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.assetid: 83b3b58f-94c1-4ab0-be05-20e01f5ae3f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 2e1ebf5a9bfc4a1f0f92ff85f9406ecc7d538819
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26734082"
---
# <a name="enterprise-state-roaming-overview"></a>Обзор службы Enterprise State Roaming
Используя Windows 10, пользователи [Azure Active Directory (Azure AD)](active-directory-whatis.md) получают возможность безопасно синхронизировать свои параметры пользователя и данные параметров приложения в облаке. Служба Enterprise State Roaming представляет собой единое решение для всех устройств Windows и сокращает процесс настройки нового устройства. Enterprise State Roaming работает так же, как и стандартная [синхронизация параметров потребителя](http://windows.microsoft.com/en-US/windows-8/sync-settings-pcs) , которая впервые представлена в Windows 8. Кроме того, Enterprise State Roaming обеспечивает следующее:

* **Разделение корпоративных и потребительских данных**. Организации управляют своими данными, поэтому корпоративные данные не попадают в облачную учетную запись потребителя, а данные потребителя — в корпоративную облачную учетную запись.
* **Повышенная безопасность**. Перед отправкой с устройства Windows 10 данные шифруются с помощью службы управления правами Azure (Azure RMS) и остаются зашифрованными в облаке. Все содержимое, хранящееся в облаке, остается зашифрованным, кроме пространств имен, таких как имена параметров и приложений Windows.  
* **Улучшенное управление и наблюдение**. Благодаря интеграции с порталом Azure AD обеспечиваются контроль и видимость, позволяющие определить, кто синхронизирует параметры в организации и на каких устройствах. 

Служба Enterprise State Roaming доступна в различных регионах Azure. Обновленный список доступных регионов см. на странице [Регионы Azure — Службы по региону](https://azure.microsoft.com/regions/#services) в разделе Azure Active Directory.

| Статья | ОПИСАНИЕ |
| --- | --- |
| [Включение службы Enterprise State Roaming в Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md) |Служба Enterprise State Roaming доступна для любой компании с подпиской Azure Active Directory Premium (Azure AD). Дополнительные сведения о том, как получить подписку Azure AD, см. на странице продукта [Azure Active Directory](https://azure.microsoft.com/services/active-directory). |
| [Часто задаваемые вопросы о перемещении параметров и данных](active-directory-windows-enterprise-state-roaming-faqs.md) |В этой статье содержится информация о синхронизации параметров и данных приложений, которая может быть полезной для ИТ-администраторов. |
| [Group policy and MDM settings for settings sync (Параметры групповой политики и управления мобильными устройствами)](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) |Windows 10 предоставляет параметры групповой политики и управления мобильными устройствами (MDM) для ограничения синхронизации параметров. |
| [Справочник по перемещаемым параметрам в Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) |Ниже приведен полный список всех параметров, для которых в Windows 10 будет выполнено перемещение и/или резервное копирование. |
| [Устранение неполадок](active-directory-windows-enterprise-state-roaming-troubleshooting.md) |В этом разделе представлены основные инструкции по устранению неполадок, а также список известных проблем. |

