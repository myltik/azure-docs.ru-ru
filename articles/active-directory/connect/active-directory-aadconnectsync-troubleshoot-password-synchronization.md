---
title: "Устранение неполадок синхронизации паролей с помощью службы синхронизации Azure AD Connect | Документация Майкрософт"
description: "Содержит сведения об устранении неполадок, связанных с синхронизацией паролей."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: e7ad2cb4c464c7095f704cc137f1c42422fbb40e
ms.openlocfilehash: 4c42821b95d666721b84d4976966b4886e517193
ms.lasthandoff: 03/01/2017


---
# <a name="troubleshoot-password-synchronization-with-azure-ad-connect-sync"></a>Устранение неполадок синхронизации паролей с помощью службы синхронизации Azure AD Connect
В этой статье приводятся пошаговые инструкции для устранения неполадок, связанных с синхронизацией паролей. Неполадки синхронизации паролей могут возникать либо в подмножестве пользователей, либо у всех.

* Если пароли не синхронизируются, см. раздел [Устранение неполадок, связанных с синхронизацией паролей](#no-passwords-are-synchronized).
* Если проблемы возникли с отдельными объектами, см. раздел [Устранение неполадок синхронизации паролей, связанных с одним объектом](#one-object-is-not-synchronizing-passwords).

## <a name="no-passwords-are-synchronized"></a>Пароли не синхронизируются
Чтобы выяснить, почему не происходит синхронизация паролей, выполните следующие действия:

1. Переведен ли сервер Connect в [промежуточный режим](active-directory-aadconnectsync-operations.md#staging-mode)? Сервер, находящийся в промежуточном режиме, не синхронизирует пароли.
2. Выполните сценарий, описанный в разделе [Получение состояния параметров синхронизации паролей](#get-the-status-of-password-sync-settings). В этом разделе рассматривается конфигурация синхронизации паролей.  
![Выходные данные сценария PowerShell из параметров синхронизации паролей](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/psverifyconfig.png)  
3. Если в Azure AD не включена функция синхронизации паролей или не добавлено состояние канала синхронизации, запустите мастер установки Azure AD Connect. Выберите **Настроить параметры синхронизации** и снимите флажок "Синхронизация паролей". Это позволит временно отключить функцию. Затем снова запустите мастер установки и включите синхронизацию паролей. Запустите скрипт снова и проверьте правильность конфигурации.
4. Проверьте журнал событий на наличие ошибок. Найдите следующие события, которые могут указывать на проблему:
    1. Source: "Directory synchronization" ID: 0, 611, 652, 655. Если в журнале есть такой текст, то это свидетельствует о проблеме с подключением. Сообщение в журнале событий содержит сведения о лесе, в котором возникла проблема. Дополнительные сведения см. в разделе [Проблема подключения](#connectivity problem).
5. Если нет пульса или не сработали другие методы, то [запустите полную синхронизацию всех паролей](#trigger-a-full-sync-of-all-passwords). Этот сценарий нужно выполнить только один раз.
6. Ознакомьтесь с разделом [Пароли не синхронизируются одним объектом](#one-object-is-not-synchronizing-passwords).

### <a name="connectivity-problem"></a>Проблема подключения

1. Проверьте, есть ли подключение к Azure AD.
2. Имеет ли учетная запись необходимые разрешения для чтения хэшей паролей во всех доменах? Если при установке Connect вы использовали стандартные параметры, то у вас уже должны быть необходимые разрешения. Если вы использовали выборочную установку, то необходимо задать разрешения вручную.
    1. Чтобы найти учетную запись, используемую соединителем Active Directory, запустите **Synchronization Service Manager**. Перейдите в раздел **Connectors** (Соединители) и найдите локальный лес Active Directory, в котором устраняются неполадки. Выберите соединитель и щелкните **Properties** (Свойства). Выберите **Подключиться к лесу Active Directory**.  
    ![Учетная запись, используемая соединителем AD](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/connectoraccount.png)  
    Запишите имя пользователя и домен, в котором размещена учетная запись.
    2. Запустите оснастку **Пользователи и компьютеры Active Directory**. Убедитесь, что для учетной записи, найденной на предыдущем шаге, в корне всех доменов в лесу заданы следующие разрешения:
        * Репликация изменений каталога
        * Репликация всех изменений каталога
3. Доступны ли контроллеры домена для Azure AD Connect? Если серверу Connect не удается подключиться ко всем контроллерам домена, то следует настроить параметр **Only use preferred domain controllers** (Использовать только предпочтительные контроллеры домена).  
    ![Контроллер домена, используемый соединителем AD](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/preferreddc.png)  
    Вернитесь в меню **Synchronization Service Manager** и выберите **Configure Directory Partitions** (Настройка разделов каталога). В списке **Select directory partitions** (Выбор разделов каталога) выберите свой домен, установите флажок **Only use preferred domain controllers** (Использовать только предпочтительные контроллеры домена) и нажмите кнопку **Configure** (Настроить). В списке введите контроллеры домена Connect, которые должны использоваться для синхронизации паролей. Этот же список используется для импорта и экспорта. Выполните эти действия для всех доменов.
4. Если конфигурация недопустима, тогда запустите скрипт, описанный в разделе [Запуск полной синхронизации всех паролей](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords"></a>Пароли не синхронизируются одним объектом
Проблемы, связанные с синхронизацией паролей, можно легко устранить, просмотрев состояние объекта.

1. Запустите оснастку **Пользователи и компьютеры Active Directory**. Найдите пользователя и снимите флажок **Потребовать смены пароля пользователя при следующем входе в систему** .  
![Повышение производительности в Active Directory с помощью синхронизации паролей](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/adprodpassword.png)  
Если флажок установлен, пользователь должен войти в систему и сменить пароль. Временные пароли не синхронизируются с Azure AD.
2. Если в Active Directory этот флажок снят, далее необходимо отследить пользователя в модуле синхронизации. Отследив пользователя от локального Active Directory до Azure AD, вы сможете убедиться в отсутствии ошибок в объекте.
    1. Запустите **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)**.
    2. Щелкните **Соединители**.
    3. Выберите **соединитель Active Directory** , к которому относится пользователь.
    4. Выберите **Search Connector Space**(Поиск пространства соединителя).
    5. В поле **Scope** (Область) выберите **DN or anchor** (Различающееся имя или привязка). Введите полное различающееся имя пользователя, для которого выполняется устранение неполадок.
    ![Поиск пользователя в пространстве соединителя с помощью различающегося имени](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/searchcs.png)  
    6. Найдите нужного пользователя и нажмите кнопку **Properties** (Свойства), чтобы просмотреть все атрибуты. Если в результатах поиска нет нужного пользователя, то проверьте [правила фильтрации](active-directory-aadconnectsync-configure-filtering.md) и выполните действия, описанные в разделе [Примените и проверьте изменения](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes). После этого пользователь должен отобразиться в Connect.
    7. Чтобы просмотреть сведения о синхронизации паролей объекта за прошедшую неделю, щелкните **Журнал**.  
    ![Сведения журнала объекта](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/csobjectlog.png)  
    Если журнал объекта пуст, то Azure AD Connect не сможет считать хэш паролей из Active Directory. Перейдите к устранению неполадок, описанных в разделе [Проблема подключения](#connectivity-errors). Если состояние имеет значение, отличное от **Success** (Успешно), то воспользуйтесь таблицей в разделе [Журнал синхронизации паролей](#password-sync-log).
    8. Откройте вкладку **Журнал обращений и преобразований** и убедитесь, что по крайней мере для одного правила синхронизации параметр **Синхронизация паролей** имеет значение **True**. В конфигурации по умолчанию правило синхронизации имеет имя **In from AD - User AccountEnabled**.  
    ![Сведения о журнале обращений и преобразований пользователя](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync.png)  
    9. Нажмите кнопку **Metaverse Object Properties** (Свойства объекта метавселенной). Отобразится список атрибутов пользователя.  
    ![Сведения метавселенной](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvpasswordsync.png)  
    Поверьте, чтобы в списке не было атрибута **cloudFiltered**. Убедитесь, что атрибуты домена (domainFQDN и domainNetBios) имеют ожидаемые значения.
    10. Перейдите на вкладку **Connectors** (Соединители). Убедитесь, что в списке отображаются соединители для локального каталога AD и для Azure AD.
    ![Сведения метавселенной](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvconnectors.png)  
    11. Выберите строку, представляющую Azure AD, и нажмите кнопку **Properties** (Свойства). Перейдите на вкладку **Lineage** (Журнал преобразований). Объект пространства соединителя должен содержать правило исходящих подключений, в котором параметр **Синхронизация паролей** имеет значение **True**. В конфигурации по умолчанию это будет правило синхронизации с именем **Out to AAD - User Join**.  
    ![Свойства пространства соединителя пользователя](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Журнал синхронизации паролей
В столбце "Состояние" могут содержаться перечисленные ниже значения.

| Состояние | Description (Описание) |
| --- | --- |
| Успешно |Пароль успешно синхронизирован |
| FilteredByTarget |Для пароля установлено значение **Пользователь должен изменить пароль при следующем входе**. Пароль не синхронизирован. |
| NoTargetConnection |Объект отсутствует в метавселенной или в пространстве соединителя Azure AD. |
| SourceConnectorNotPresent |Объект не найден в локальном пространстве соединителя Active Directory. |
| TargetNotExportedToDirectory |Объект в пространстве соединителя Azure AD еще не экспортирован. |
| MigratedCheckDetailsForMoreInfo |Запись журнала создана до выхода версии 1.0.9125.0 и отображается в исходном состоянии. |

## <a name="scripts-to-help-troubleshooting"></a>Сценарии, помогающие при устранении неполадок

### <a name="get-the-status-of-password-sync-settings"></a>Получение состояния параметров синхронизации паролей
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Запуск полной синхронизации всех паролей
> [!NOTE]
> Этот сценарий нужно выполнить только один раз. Если требуется его повторное выполнение, то это значит, что проблема в чем-то другом. Для устранения этой проблемы обратитесь в службу поддержки Майкрософт.

Полную синхронизацию всех паролей можно активировать с помощью следующего скрипта:

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Дальнейшие действия
* [Реализация синхронизации паролей с помощью службы Azure AD Connect Sync](active-directory-aadconnectsync-implement-password-synchronization.md)
* [Azure AD Connect Sync: настройка параметров синхронизации](active-directory-aadconnectsync-whatis.md)
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)

