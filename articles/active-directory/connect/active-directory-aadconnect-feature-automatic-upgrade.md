---
title: "Azure AD Connect: автоматическое обновление | Документация Майкрософт"
description: "В этом разделе описывается встроенная функция автоматического обновления в Azure AD Connect."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 9541cd195e8afa6e692a5a0214b0d614e429c33b
ms.openlocfilehash: 234f06a858987b03ec2ed0bbe0899423d8f0bcdd
ms.contentlocale: ru-ru
ms.lasthandoff: 12/20/2016

---
# Azure AD Connect: автоматическое обновление
<a id="azure-ad-connect-automatic-upgrade" class="xliff"></a>
Эта функция появилась в сборке 1.1.105.0 (выпущенной в феврале 2016 года).

## Обзор
<a id="overview" class="xliff"></a>
С функцией **автоматического обновления** контроль за обновлением установки Azure AD Connect максимально упростился. Эта функция по умолчанию включена для быстрой установки и обновления DirSync. При выпуске новой версии установка обновляется автоматически.

Автоматическое обновление по умолчанию включено в следующих случаях:

* Экспресс-установка параметров и обновление DirSync.
* Использование SQL Express LocalDB (всегда используется при экспресс-установке). DirSync с SQL Express также использует LocalDB.
* Учетной записью AD является учетная запись MSOL_ по умолчанию, создаваемая при экспресс-установке и обновлении DirSync.
* В метавселенной меньше 100 000 объектов.

Текущее состояние автоматического обновления можно узнать с помощью командлета PowerShell `Get-ADSyncAutoUpgrade`. Возможны следующие состояния:

| Состояние | Комментарий |
| --- | --- |
| Включено |Автоматическое обновление включено. |
| Приостановлено |Устанавливается только системой. Система больше не может получать автоматические обновления. |
| Отключено |Автоматическое обновление отключено. |

Переключаться между состояниями **Включено** и **Отключено** позволяет командлет `Set-ADSyncAutoUpgrade`. Состояние **Приостановлено**может устанавливаться только системой.

В качестве инфраструктуры обновлений при автоматическом обновлении используется Azure AD Connect Health. Чтобы работало автоматическое обновление, откройте в прокси-сервере URL-адреса для **Azure AD Connect Health** , указанные в статье [URL-адреса и диапазоны IP-адресов Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Если на сервере запущен пользовательский интерфейс **диспетчера службы синхронизации** , обновление будет отложено до тех пор, пока он не будет закрыт.

## Устранение неполадок
<a id="troubleshooting" class="xliff"></a>
Если установка Connect не обновляется автоматически, выполните следующие действия, чтобы узнать, в чем может быть проблема.

Во-первых, не следует ожидать попыток автоматического обновления в первый день выпуска новой версии. Попытки обновления выполняются в случайном порядке намеренно, поэтому не стоит беспокоиться, если обновление установки не начинается немедленно.

Если вы считаете, что возникла проблема, сначала запустите `Get-ADSyncAutoUpgrade` , чтобы убедиться, что автоматическое обновление включено.

Затем убедитесь, что вы открыли нужные URL-адреса на прокси-сервере или в брандмауэре. Автоматическое обновление использует Azure AD Connect Health, как описано в [обзоре](#overview). При использовании прокси-сервера убедитесь, что служба Health настроена для работы с [прокси-сервером](../connect-health/active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Кроме того, проверьте [подключение Health](../connect-health/active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) к Azure AD.

После проверки подключения к Azure AD можно изучить журналы событий. Запустите средство просмотра событий и найдите журнал событий **Приложение** . Добавьте фильтр журнала событий для источника **Azure AD Connect Upgrade** и диапазона идентификаторов событий **300-399**.  
![Фильтр журнала событий для автоматического обновления](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

Откроется список журналов событий, связанных с состоянием автоматического обновления.  
![Фильтр журнала событий для автоматического обновления](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

Итоговый код содержит префикс и общие сведения о состоянии.

| Префикс итогового кода | Description (Описание) |
| --- | --- |
| Успешно |Установка успешно обновлена. |
| UpgradeAborted |Временное состояние привело к остановке обновления. Будет выполнена повторная попытка установки, и ожидается, что она будет успешной. |
| UpgradeNotSupported |Конфигурация системы блокирует автоматическое обновление. Будет выполнена повторная попытка для проверки изменения состояния, однако ожидается, что систему потребуется обновить вручную. |

Ниже приведен список наиболее распространенных сообщений. В списке приведены не все сообщения, однако сообщение о результате должно явно указывать на проблему.

| Сообщение о результате | Description (Описание) |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Не удалось сделать запись в реестре. |
| UpgradeAbortedInsufficientDatabasePermissions |Встроенная группа администраторов не имеет разрешений в базе данных. Для решения этой проблемы необходимо установить обновление до новейшей версии Azure AD Connect вручную. |
| UpgradeAbortedInsufficientDiskSpace |Недостаточно дискового пространства для поддержки обновления. |
| UpgradeAbortedSecurityGroupsNotPresent |Не удалось найти и разрешить все группы безопасности, используемые модулем синхронизации. |
| UpgradeAbortedServiceCanNotBeStarted |Не удалось запустить **синхронизацию Microsoft Azure AD** для службы NT. |
| UpgradeAbortedServiceCanNotBeStopped |Не удалось остановить **синхронизацию Microsoft Azure AD** для службы NT. |
| UpgradeAbortedServiceIsNotRunning |**Синхронизация Microsoft Azure AD** для службы NT не выполняется. |
| UpgradeAbortedSyncCycleDisabled |Параметр SyncCycle в [планировщике](active-directory-aadconnectsync-feature-scheduler.md) был отключен. |
| UpgradeAbortedSyncExeInUse |На сервере открыт [пользовательский интерфейс Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) . |
| UpgradeAbortedSyncOrConfigurationInProgress |Выполняется мастер установки, или синхронизация была запланирована вне планировщика. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedCustomizedSyncRules |Пользователь добавил собственные правила в конфигурацию. |
| UpgradeNotSupportedDeviceWritebackEnabled |Включена функция [обратной записи устройства](active-directory-aadconnect-feature-device-writeback.md) . |
| UpgradeNotSupportedGroupWritebackEnabled |Включена функция [обратной записи групп](active-directory-aadconnect-feature-preview.md#group-writeback) . |
| UpgradeNotSupportedInvalidPersistedState |Установка не является экспресс-установкой параметров или обновлением DirSync. |
| UpgradeNotSupportedMetaverseSizeExceeeded |В метавселенной больше 100 000 объектов. |
| UpgradeNotSupportedMultiForestSetup |Выполняется подключение к нескольким лесам. Экспресс-установка подключается только к одному лесу. |
| UpgradeNotSupportedNonLocalDbInstall |Вы не используете базу данных SQL Server Express LocalDB. |
| UpgradeNotSupportedNonMsolAccount |[Учетная запись соединителя AD](active-directory-aadconnect-accounts-permissions.md#active-directory-account) больше не является учетной записью MSOL_ по умолчанию. |
| UpgradeNotSupportedStagingModeEnabled |Сервер настроен для работы в [промежуточном режиме](active-directory-aadconnectsync-operations.md#staging-mode). |
| UpgradeNotSupportedUserWritebackEnabled |Включена функция [обратной записи пользователей](active-directory-aadconnect-feature-preview.md#user-writeback) . |

## Дальнейшие действия
<a id="next-steps" class="xliff"></a>
Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

