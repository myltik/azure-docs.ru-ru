---
title: Устранение ошибок пакетов содержимого, зарегистрированных в журналах действий Azure Active Directory | Документация Майкрософт
description: Содержит список сообщений об ошибках пакета содержимого действий Azure Active Directory и инструкции по их исправлению.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 801c425d0d467f8df63f981a1a03c6d202b4c3c5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589225"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Устранение ошибок пакетов содержимого, зарегистрированных в журналах действий Azure Active Directory 


При работе с пакетом содержимого Power BI для предварительной версии Azure Active Directory могут произойти приведенные ниже ошибки. 

- [Сбой обновления](active-directory-reporting-troubleshoot-content-pack.md#refresh-failed) 
- [Не удалось обновить учетные данные источников данных](active-directory-reporting-troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Importing of data is taking too long](active-directory-reporting-troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) (Импорт данных занимает слишком много времени) 
 
Данный раздел содержит сведения о возможных причинах и способах устранения этих ошибок.
 
## <a name="refresh-failed"></a>"Сбой обновления" 
 
**Как отображается эта ошибка**: электронное сообщение от Power BI или состояние ошибки в журнале обновлений. 


| Причина: | Как устранить |
| ---   | ---        |
| Сбои обновления могут возникнуть, если учетные данные пользователей, подключающихся к пакету содержимого, были сброшены, но не были обновлены в параметры подключения пакета содержимого. | В Power BI найдите набор данных, соответствующий панели мониторинга журналов действий Azure Active Directory (журналы действий Azure Active Directory), выберите "Запланировать обновление" и введите учетные данные Azure AD. |
| Обновление может завершиться сбоем из-за проблем с данными в базовом пакете содержимого. | Отправьте запрос в службу поддержки. Дополнительные сведения см. в разделе [Как получить поддержку для Azure Active Directory](active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>"Не удалось обновить учетные данные источников данных" 
 
**Как отображается эта ошибка**: в Power BI, при подключении к пакету содержимого журналов действий Azure Active Directory (предварительная версия). 

| Причина: | Как устранить |
| ---   | ---        |
| Подключающийся пользователь не является глобальным администратором, читателем безопасности или администратором безопасности. | Используйте учетную запись глобального администратора, читателя безопасности или администратора безопасности для доступа к пакетам содержимого. |
| Клиент не имеет лицензии Premium или по крайней мере одного пользователя с файлом лицензии Premium. | Отправьте запрос в службу поддержки. Дополнительные сведения см. в разделе [Как получить поддержку для Azure Active Directory](active-directory-troubleshooting-support-howto.md).|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>"Importing of data is taking too long" (Импорт данных занимает слишком много времени) 
 
**Как отображается эта ошибка**: в Power BI после подключения к пакету содержимого процесс импорта данных приступает к подготовке панели мониторинга для журнала действий Azure Active Directory. Отображается сообщение: *Importing of data…* (Импорт данных…)  

| Причина: | Как устранить |
| ---   | ---        |
| В зависимости от размера клиента шаг может длиться от нескольких минут до получаса. | Просто подождите. Если в течение часа сообщение не исчезнет и не отобразится панель мониторинга, отправьте запрос в службу поддержки. Дополнительные сведения см. в разделе [Как получить поддержку для Azure Active Directory](active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Дополнительная информация

Чтобы установить пакет содержимого Power BI для предварительной версии Azure Active Directory, щелкните [здесь](https://powerbi.microsoft.com/en-us/blog/azure-active-directory-meets-power-bi/).


