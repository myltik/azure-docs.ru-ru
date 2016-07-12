<properties
	pageTitle="Функции и конфигурация службы синхронизации Azure AD Connect | Microsoft Azure"
	description="Описываются функциональные возможности службы синхронизации Azure AD Connect."
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
	ms.date="06/27/2016"
	ms.author="andkjell;markvi"/>

# Функции службы синхронизации Azure AD Connect

Средство синхронизации Azure AD Connect состоит из двух компонентов.

- Локальный компонент под названием **Синхронизация Azure AD Connect**, который также называется **модулем синхронизации**.
- Размещенная в Azure AD служба, которая также называется **службой синхронизации Azure AD Connect**.

В этом разделе описаны следующие функции **службы синхронизации Azure AD Connect** и их настройка с помощью Windows PowerShell.

Для настройки этих параметров используется [модуль Azure Active Directory для Windows PowerShell](http://aka.ms/aadposh), и его необходимо скачать и установить отдельно от Azure AD Connect. Описанные в этом разделе командлеты появились в [версии за март 2016 г. (сборка 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Если у вас отсутствуют командлеты, описанные в этом разделе, или они дают другой результат, проверьте, используете ли вы последнюю версию.

Для просмотра конфигурации каталога Azure AD выполните следующую команду: `Get-MsolDirSyncFeatures`. ![Результат вызова командлета Get-MsolDirSyncFeatures](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png).

Многие из этих параметров могут быть изменены только службой Azure AD Connect.

Следующие параметры можно настроить с помощью `Set-MsolDirSyncFeature`:

DirSyncFeature | Комментарий
--- | ---
 [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) | Позволяет поместить атрибут, являющийся копией другого объекта, в карантин вместо того, чтобы прерывать весь процесс экспорта для этого объекта.
[EnableSoftMatchOnUpn](#userprincipalname-soft-match) | Позволяет присоединять объекты по атрибуту userPrincipalName в дополнение к основному адресу SMTP.
[SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) | Позволяет модулю синхронизации обновлять атрибут userPrincipalName для управляемых или лицензированных пользователей (не являющихся федеративными).

После включения функции отключить ее нельзя.

Следующие параметры настраиваются службой Azure AD Connect и не могут изменяться командлетом `Set-MsolDirSyncFeature`:

DirSyncFeature | Комментарий
--- | ---
DeviceWriteback | [Azure AD Connect: включение обратной записи устройств](active-directory-aadconnect-feature-device-writeback.md)
DirectoryExtensions | [Синхронизация Azure AD Connect: расширения каталогов](active-directory-aadconnectsync-feature-directory-extensions.md)
PasswordSync | [Реализация синхронизации паролей с помощью службы Azure AD Connect Sync](active-directory-aadconnectsync-implement-password-synchronization.md)
UnifiedGroupWriteback | [Предварительная версия. Обратная запись групп](active-directory-aadconnect-feature-preview.md#group-writeback)
UserWriteback | Сейчас не поддерживается.

## Устойчивость повторяющихся атрибутов
Чтобы избежать сбоя при подготовке объектов с повторяющимися именами участников-пользователей (UPN) или адресами прокси-сервера (proxyAddress), повторяющийся атрибут помещается в карантин, и при необходимости ему присваивается временное значение. После разрешения конфликта временное имя участника-пользователя (UPN) будет автоматически исправлено на соответствующее значение. Такой механизм можно включить отдельно для имени участника-пользователя (UPN) и адреса прокси-сервера (proxyAddress). Дополнительные сведения см. в разделе [Синхронизация удостоверений и устойчивость повторяющихся атрибутов](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## Мягкое сопоставление атрибута userPrincipalName
При включении этой функции к имени участника-пользователя, а также к [основному адресу SMTP](https://support.microsoft.com/kb/2641663), который всегда активен, будет применяться мягкое сопоставление. Оно используется для сопоставления существующих облачных пользователей в Azure AD с локальными пользователями.

Эта функция особенно удобна для сопоставления локальных учетных записей AD с существующими учетными записями, созданными в облаке, в том случае, если вы не используете Exchange Online. В такой ситуации устанавливать атрибут SMTP для облака обычно не нужно.

Эта функция по умолчанию включена для создаваемых каталогов Azure AD. Чтобы увидеть, включена ли эта функция, выполните следующую команду:
```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Если эта функция отключена для вашего каталога Azure AD, ее можно включить, выполнив следующую команду:
```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## Синхронизация обновлений атрибута userPrincipalName
Обновления атрибута userPrincipalName с помощью службы синхронизации из локальной среды блокируются за исключением тех случаев, когда выполняются два следующих условия:

- пользователь является управляемым (нефедеративным);
- пользователю не назначена лицензия.

Дополнительные сведения см. в разделе [Имена пользователей в Office 365, Azure или Intune не совпадают с локальным именем участника-пользователя или альтернативным именем для входа](https://support.microsoft.com/kb/2523192).

Включение этой функции позволяет модулю синхронизации обновлять атрибут userPrincipalName при его изменении в локальной среде и при использовании синхронизации паролей. Если используется федерация, эта функция не будет работать.

Эта функция по умолчанию включена для создаваемых каталогов Azure AD. Чтобы увидеть, включена ли эта функция, выполните следующую команду:
```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Если эта функция отключена для вашего каталога Azure AD, ее можно включить, выполнив следующую команду:
```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

После включения этой функции существующие значения атрибут userPrincipalName останутся неизменными. Если в дальнейшем атрибут userPrincipalName изменится в локальной среде, то имя участника-пользователя будет обновлено при обычной синхронизации изменений для пользователей.

## Будущие изменения
В будущем эти параметры будут включены для всех каталогов Azure AD.

## См. также

- [Службы синхронизации Azure AD Connect](active-directory-aadconnectsync-whatis.md)

- [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0629_2016-->