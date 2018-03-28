---
title: 'Azure AD Connect: простой единый вход. Соответствие требованиям GDPR | Документация Майкрософт'
description: В этой статье рассматривается простой единый вход Azure Active Directory (Azure AD) и соответствие требованиям GDPR.
services: active-directory
keywords: what is Azure AD Connect, GDPR, required components for Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: billmath
ms.openlocfilehash: 0c7ed376accb1eed01106358491e925d3b8126c5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
# <a name="azure-ad-seamless-single-sign-on-gdpr-compliance"></a>Простой единый вход Azure AD: соответствие требованиям GDPR

## <a name="overview"></a>Обзор

В мае 2018 года в силу вступит закон о конфиденциальности ЕС, [Общий регламент по защите данных (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm). GDPR устанавливает новые правила для компаний, правительственных учреждений, некоммерческих организаций и других организаций, которые предлагают товары и услуги жителям государств Европейского Союза (ЕС) или собирают и анализируют связанные с ними данные. GDPR применяется независимо от того, где вы находитесь. 

Сегодня доступны продукты и службы Майкрософт, позволяющие обеспечить соответствие требований GDPR. Дополнительные сведения о политике конфиденциальности корпорации Майкрософт см. в [центре управления безопасностью](https://www.microsoft.com/trustcenter).

При простом едином входе Azure AD создается журнал следующего типа, который может содержать EUII:

- файлы журнала трассировки Azure AD Connect.

Соответствия требованиям GDPR для простого единого входа можно достичь двумя способами:

1.  По запросу извлекать данные для пользователя и удалять эти данные из установленных решений.
2.  Не хранить данные больше 48 часов.

Настоятельно рекомендуем использовать второй вариант, так как он проще в реализации и обслуживании. См приведенные ниже инструкции по каждому типу журналов.

### <a name="delete-azure-ad-connect-trace-log-files"></a>Удаление файлов журнала трассировки Azure AD Connect

Проверьте содержимое папки **%ProgramData%\AADConnect** и удалите содержимое журнала трассировки (файлы **trace-\*.log**) из этой папке в течение 48 часов после установки или обновления Azure AD Connect либо изменения конфигурации простого единого входа, так как при этих действиях создаются данные, на которые распространяются требования GDPR.

>[!IMPORTANT]
>Не удаляйте файл **PersistedState.xml** в этой папке — он используется для сохранения состояния предыдущей установки Azure AD Connect, а также после установки обновления. В этом файле никогда не хранятся личные данные о пользователях, и его не следует удалять.

Вы можете просмотреть и удалить файлы журналов трассировки с помощью проводника Windows или использовать приведенный ниже скрипт PowerShell.

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Сохраните скрипт в файле с расширением PS1. Запускайте его по мере необходимости.

Дополнительные сведения о требованиях GDPR для Azure AD Connect см. [в этой статье](active-directory-aadconnect-gdpr.md).

### <a name="note-about-domain-controller-logs"></a>Примечание о журналах контроллеров домена

Если включено ведение журнала аудита, этот продукт может создавать журналы безопасности для контроллеров домена. Дополнительные сведения о настройке политик аудита см. в [этой статье](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Дополнительная информация

- [**Устранение неполадок**](active-directory-aadconnect-troubleshoot-sso.md). Узнайте, как устранить самые распространенные проблемы с этой функцией.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Отправка запросов новых функций.
