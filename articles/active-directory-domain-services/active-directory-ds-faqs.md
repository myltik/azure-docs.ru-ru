---
title: Доменные службы Azure Active Directory — часто задаваемые вопросы | Документация Майкрософт
description: Часто задаваемые вопросы о доменных службах Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: maheshu
ms.openlocfilehash: 1cfd0570315d5a1c6587ade164edf0a837453406
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2018
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Доменные службы Azure Active Directory: часто задаваемые вопросы
На этой странице вы найдете ответы на часто задаваемые вопросы о доменных службах Azure Active Directory. Следите за обновлениями.

## <a name="troubleshooting-guide"></a>Руководство по устранению неполадок
См. наше [руководство по устранению неполадок](active-directory-ds-troubleshooting.md) для решения распространенных проблем при настройке или администрировании доменных служб Azure AD.

## <a name="configuration"></a>Параметр Configuration
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Можно ли создать несколько управляемых доменов для одного каталога Azure AD?
Нет. Для одного каталога Azure AD можно создать только один управляемый домен, поддерживаемый доменными службами Azure AD.  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Можно ли включить доменные службы Azure AD в виртуальной сети Azure Resource Manager?
Да. Доменные службы Azure AD можно включить в виртуальной сети Azure Resource Manager. Классические виртуальные сети Azure больше не поддерживаются для создания управляемых доменов.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Можно ли перенести мой существующий управляемый домен из классической виртуальной сети в виртуальную сеть Azure Resource Manager?
В настоящее время нет. В будущем корпорация Майкрософт предоставит механизм переноса существующего управляемого домена из классической виртуальной сети в виртуальную сеть Azure Resource Manager.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Можно ли включить доменные службы Azure AD в подписку Azure CSP (поставщик облачных решений)?
Да. Узнайте, как включить [доменные служб Azure AD в подписках Azure CSP](active-directory-ds-csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Можно ли включить доменные службы Azure AD в федеративном каталоге Azure AD? Хэши паролей не синхронизируются с Azure AD. Можно включить доменные службы Azure AD для этого каталога?
Нет. Доменным службам Azure AD необходим доступ к хэшам паролей учетных записей пользователей для аутентификации пользователей с помощью NTLM или Kerberos. При использовании федеративного каталога хэши паролей не хранятся в каталоге Azure AD. Поэтому доменные службы Azure AD не работает с такими каталогами Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Можно ли сделать доменные службы AD Azure доступными в нескольких виртуальных сетях в рамках подписки?
Сама служба напрямую не поддерживает этот сценарий. В определенный момент времени управляемый домен доступен только в одной виртуальной сети. Однако можно настроить подключение между несколькими виртуальными сетями, чтобы предоставить доступ к доменным службам Azure AD другим виртуальным сетям. См. дополнительные сведения о [подключении виртуальных сетей в Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Можно ли включить доменные службы Azure AD с помощью PowerShell?
Да. Узнайте, [как включить доменные службы Azure AD с помощью PowerShell](active-directory-ds-enable-using-powershell.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Можно ли включить доменные службы Azure AD с помощью шаблона Resource Manager?
Да. Узнайте, [как включить доменные службы Azure AD с помощью PowerShell](active-directory-ds-enable-using-powershell.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Могу ли я добавить контроллеры домена в управляемый домен доменных служб Azure AD?
Нет. Домен, предоставленный доменными службами Azure AD — это управляемый домен. Вам не нужно подготавливать, настраивать или иным образом обрабатывать контроллеры домена для этого домена. Корпорация Майкрософт предоставляет эти операции управления как услугу. Поэтому вы не можете добавить дополнительные контроллеры домена (ни контроллеры чтения и записи, ни контроллеры только для чтения) для управляемого домена.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Могут ли гостевые пользователи, приглашенные в каталог, использовать доменные службы Azure AD?
Нет. Гостевые пользователи, приглашенные в каталог Azure AD с помощью процесса [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md), синхронизируются в управляемом домене доменных служб Azure AD. Однако пароли этих пользователей не хранятся в каталоге Azure AD. Таким образом доменные службы Azure AD не имеют возможности синхронизировать хэши NTLM и Kerberos этих пользователей в управляемом домене. В результате такие пользователи не могут войти в управляемый домен или подключить к нему компьютеры.

## <a name="administration-and-operations"></a>Администрирование и операции
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Можно ли подключиться к контроллеру управляемого домена с помощью удаленного рабочего стола?
Нет. У вас нет разрешений на подключение к контроллерам доменов для управляемого домена с помощью удаленного рабочего стола. Члены группы «Администраторы контроллера домена AAD» могут администрировать управляемый домен, используя средства администрирования AD, такие как центр администрирования Active Directory (ADAC) или AD PowerShell. Эти средства устанавливаются с помощью функции «Средства удаленного администрирования сервера» на сервере Windows, присоединенном к управляемому домену.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Я включил доменные службы Azure AD. Какой учетной записью пользователя нужно воспользоваться для подключения компьютеров к этому домену?
Участники административной группы (например, "Администраторы контроллера домена AAD"), могут присоединять компьютеры к домену. Кроме того, участникам этой группы предоставляется доступ с помощью удаленного рабочего стола к компьютерам, которые были подключены к домену.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Есть ли у меня привилегии администратора домена для управляемого домена, предоставляемого доменными службами AD Azure?
Нет. Вам не предоставляются права администратора для управляемого домена. Вам не будут доступны привилегии "Администратор домена" и "Администратор предприятия" в этом домене. Существующим группам администраторов домена и администраторов предприятия в каталоге Azure AD также не предоставляются права администратора домена или администратора предприятия в домене.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Можно ли изменить членство в группах с помощью LDAP или других инструментов администрирования AD для управляемых доменов?
Нет. В доменах, поддерживаемых доменными службами Azure AD, членство в группах изменить нельзя. То же касается и атрибутов пользователей. Однако членство в группах или атрибуты пользователей можно изменить в Azure AD или в локальном домене. Такие изменения автоматически синхронизируются с доменными службами Azure AD.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Через какое время изменения в каталоге Azure AD будут отображаться в управляемом домене?
Изменения, внесенные в каталог Azure AD с помощью пользовательского интерфейса Azure AD или PowerShell, синхронизируются с управляемым доменом. Процесс синхронизации происходит в фоновом режиме. После однократной начальной синхронизации каталога обычно необходимо около 20 минут, чтобы внесенные в Azure AD изменения отобразились в управляемом домене.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Могу ли я расширить схему управляемого домена, предоставляемого доменными службами Azure AD?
Нет. Схемой управляемого домена управляет корпорация Майкрософт. Расширения схемы не поддерживаются в доменных службах Azure AD.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Можно ли изменять или добавлять записи DNS в управляемом домене?
Да. Участники группы "Администраторы контроллера домена AAD" получают привилегии администратора DNS, позволяющие изменять записи DNS в управляемом домене. Они могут использовать консоль диспетчера DNS на компьютере под управлением Windows Server, присоединенном к управляемому домену, для управления службами DNS. Чтобы использовать консоль диспетчера DNS, установите "Средства DNS-сервера", которые входят в необязательный компонент "Средства удаленного администрирования сервера" на сервере. Дополнительные сведения о [служебных программам для администрирования, мониторинга и устранения неполадок DNS](https://technet.microsoft.com/library/cc753579.aspx) доступны в библиотеке TechNet.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Что из себя представляет политика времени существования пароля в управляемом домене?
Время существования пароля по умолчанию в управляемом домене доменных служб Azure AD составляет 90 дней. Время существования этого пароля не синхронизировано со временем жизни пароля, настроенным в Azure AD. Таким образом может возникнуть ситуация, когда срок действия паролей пользователей заканчивается в управляемом домене, но они все еще действуют в Azure AD. В таких случаях пользователям требуется сменить пароль в Azure AD, после чего новый пароль синхронизируется с управляемым доменом. Кроме того, атрибуты password-does-not-expire и user-must-change-password-at-next-logon учетных записей пользователей не синхронизируются с управляемым доменом.

## <a name="billing-and-availability"></a>Выставление счетов и доступность
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Являются ли доменные службы Azure AD платной службой?
Да. Дополнительные сведения см. на [странице с расценками](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Существует ли бесплатная пробная версия для службы?
Эта служба включена в бесплатную пробную версию Azure. Вы можете зарегистрировать [бесплатную пробную версию Azure на один месяц](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Можно ли приостановить управляемый домен доменных служб Azure AD? 
Нет. Когда вы включите управляемый домен доменных служб Azure AD, служба будет доступной в пределах выбранной виртуальной сети, пока не отключите или удалите управляемый домен. Приостановить работу службы нельзя. Счета выставляются на почасовой основе, пока вы не удалите управляемый домен.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Можно ли получить доменные службы Azure AD в составе Enterprise Mobility Suite (EMS)? Требуется ли Azure AD Premium для использования доменных служб Azure AD?
Нет. Доменные службы Azure — это служба Azure с оплатой по мере использования, не входящая в предложение EMS. Доменные службы Azure AD можно использовать со всеми выпусками Azure AD ("Бесплатный", "Базовый" и "Премиум"). Счет выставляется каждый час в зависимости от использования.

### <a name="what-azure-regions-is-the-service-available-in"></a>В каких регионах Azure доступна служба?
Перейдите на страницу [служб Azure по регионам](https://azure.microsoft.com/regions/#services/), чтобы просмотреть список регионов Azure, в которых доступны доменные службы Azure AD.
