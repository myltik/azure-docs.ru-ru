---
title: Конфиденциальность пользователей и сквозная аутентификация Azure Active Directory | Документация Майкрософт
description: В этой статье описывается сквозная аутентификация Azure Active Directory (Azure AD) и соответствие GDPR.
services: active-directory
keywords: Azure AD Connect Pass-through Authentication, GDPR, required components for Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/28/2018
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 910eb5bdd1b9d4a2a27a27c89812584bb068bec0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150629"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Конфиденциальность пользователей и сквозная аутентификация Azure Active Directory


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Обзор

Сквозная аутентификация Azure AD создает следующие типы журналов, которые могут содержать EUII:

- файлы журнала трассировки Azure AD Connect;
- файлы журнала трассировки агента проверки подлинности;
- файлы журнала событий Windows.

Конфиденциальность пользователей для сквозной аутентификации можно обеспечить двумя способами:

1.  по запросу извлекать данные для пользователя и удалять эти данные из установленных решений;
2.  Не хранить данные больше 48 часов.

Настоятельно рекомендуем использовать второй вариант, так как его проще реализовать и обслуживать. См. приведенные ниже инструкции по каждому типу журналов.

### <a name="delete-azure-ad-connect-trace-log-files"></a>Удаление файлов журнала трассировки Azure AD Connect

Проверьте содержимое папки **%ProgramData%\AADConnect** и удалите содержимое журнала трассировки (файлы **trace-\*.log**) из этой папки в течение 48 часов после установки или обновления Azure AD Connect либо изменения конфигурации для сквозной аутентификации, так как при этих действиях создаются данные, на которые распространяются требования GDPR.

>[!IMPORTANT]
>Не удаляйте файл **PersistedState.xml** из этой папки. Он нужен для хранения состояния предыдущей установки Azure AD Connect и используется при установке обновлений. В этом файле никогда не хранятся личные данные о пользователях, и его не следует удалять.

Вы можете просмотреть и удалить файлы журналов трассировки с помощью проводника Windows или использовать приведенный ниже скрипт PowerShell.

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Сохраните скрипт в файле с расширением PS1. Запускайте его по мере необходимости.

Дополнительные сведения о требованиях GDPR для Azure AD Connect см. [в этой статье](active-directory-aadconnect-gdpr.md).

### <a name="delete-authentication-agent-event-logs"></a>Удаление журналов событий агента аутентификации

Этот продукт может также создавать **журналы событий Windows**. Дополнительные сведения см. в [этой статье](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Чтобы посмотреть журналы, связанные с агентом сквозной аутентификации, откройте на сервере приложение **Просмотр событий** и проверьте расположение **Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

### <a name="delete-authentication-agent-trace-log-files"></a>Удаление файлов журнала трассировки агента проверки подлинности

Следует регулярно проверять содержимое каталога **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace\** и очищать его каждые 48 часов. 

>[!IMPORTANT]
>Если запущена служба агента аутентификации, вы не сможете удалить текущий файл журнала в этой папке. Остановите службу и повторите попытку. Чтобы избежать ошибок со входом пользователей, следует заранее настроить [высокий уровень доступности](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability) для сквозной аутентификации.

Вы можете просмотреть и удалить эти файлы с помощью проводника Windows или использовать приведенный ниже сценарий.

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Запланируйте выполнение этого сценария через каждые 48 часов, используя следующие шаги:

1.  Сохраните скрипт в файл с расширением .PS1.
2.  Откройте **Панель управления** и выберите элемент **Система и безопасность**.
3.  В разделе **Администрирование** щелкните **Расписание выполнения задач**.
4.  В **планировщике задач** нажмите правой кнопкой мыши команду **Библиотека расписания задач** и щелкните **Создать простую задачу**.
5.  Введите имя для новой задачи и нажмите кнопку **Далее**.
6.  Выберите **Ежедневно** в поле **Триггер задачи** и щелкните **Далее**.
7.  Установите значение периодичности "2 дня" и щелкните **Далее**.
8.  Выберите действие **Запуск программы** и щелкните **Далее**.
9.  В поле программы или сценария введите **PowerShell** и в поле **Добавить аргументы (необязательно)** введите полный путь к созданному ранее сценарию, а затем щелкните **Далее**.
10. На следующем экране показана сводка задачи, которую вы собираетесь создать. Проверьте значения и щелкните **Готово**, чтобы запустить создание задачи.
 
### <a name="note-about-domain-controller-logs"></a>Примечание о журналах контроллеров домена

Если включено ведение журнала аудита, этот продукт может создавать журналы безопасности для контроллеров домена. Дополнительные сведения о настройке политик аудита см. в [этой статье](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Дополнительная информация
* [Просмотр политики конфиденциальности корпорации Майкрософт в центре управления безопасностью](https://www.microsoft.com/trustcenter)
- [**Устранение неполадок**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md). Узнайте, как устранить самые распространенные проблемы с этой функцией.
