---
title: "Доменные службы Azure Active Directory — часто задаваемые вопросы | Документация Майкрософт"
description: "Часто задаваемые вопросы о доменных службах Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 5e6bab265b2b6eabd1a878492588c4eb39d1b332
ms.openlocfilehash: 89dfabb8feafffee2ed8143c372b53d02033d582
ms.lasthandoff: 01/14/2017


---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Доменные службы Azure Active Directory: часто задаваемые вопросы
На этой странице вы найдете ответы на часто задаваемые вопросы о доменных службах Azure Active Directory. Следите за обновлениями.

### <a name="troubleshooting-guide"></a>Руководство по устранению неполадок
Обратитесь к нашему [Руководству по устранению неполадок](active-directory-ds-troubleshooting.md) для решения распространенных проблем при настройке или администрировании доменных служб Azure AD.

### <a name="configuration"></a>Конфигурация
#### <a name="can-i-create-multiple-domains-for-a-single-azure-ad-directory"></a>Можно ли создать несколько доменов для одного каталога Azure AD?
Нет. Для одного каталога Azure AD можно создать только один домен, поддерживаемый доменными службами Azure AD.  

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Можно ли включить доменные службы Azure AD в виртуальной сети Azure Resource Manager?
Нет. Доменные службы Azure AD можно включить только в классической виртуальной сети Azure. Классическую виртуальную сеть можно подключить к виртуальной сети Resource Manager с помощью пиринговой связи между виртуальными сетями. Это позволит использовать управляемый домен в виртуальной сети Resource Manager.

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Можно ли сделать доменные службы AD Azure доступными в нескольких виртуальных сетях в рамках подписки?
Сама служба напрямую не поддерживает этот сценарий. Доменные службы AD Azure доступны в только одной виртуальной сети одновременно. Однако можно настроить подключение между несколькими виртуальными сетями, чтобы предоставить доступ к доменным службам Azure AD другим виртуальным сетям. В этой статье описывается, как можно [подключить виртуальные сети в Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Можно ли включить доменные службы Azure AD с помощью PowerShell?
Автоматическое развертывание доменных служб AD Azure или развертывание с помощью PowerShell в настоящее время недоступны.

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal"></a>Доступны ли доменные службы Azure AD на новом портале Azure?
Нет. Доменные службы Azure AD можно настроить только на [классическом портале Azure](https://manage.windowsazure.com). В будущем мы планируем расширить поддержку [портала Azure](https://portal.azure.com) .

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Могу ли я добавить контроллеры домена в управляемый домен доменных служб Azure AD?
Нет. Домен, предоставленный доменными службами Azure AD — это управляемый домен. Вам не нужно подготавливать, настраивать или иным образом обрабатывать контроллеры домена для этого домена. Корпорация Майкрософт предоставляет эти операции управления как услугу. Поэтому вы не можете добавить дополнительные контроллеры домена (ни контроллеры чтения и записи, ни контроллеры только для чтения) для управляемого домена.

### <a name="administration-and-operations"></a>Администрирование и операции
#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Можно ли подключиться к контроллеру управляемого домена с помощью удаленного рабочего стола?
Нет. У вас нет разрешений на подключение к контроллерам доменов для управляемого домена с помощью удаленного рабочего стола. Члены группы «Администраторы контроллера домена AAD» могут администрировать управляемый домен, используя средства администрирования AD, такие как центр администрирования Active Directory (ADAC) или AD PowerShell. Эти средства устанавливаются с помощью функции «Средства удаленного администрирования сервера» на сервере Windows, присоединенном к управляемому домену.

#### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Я включил доменные службы Azure AD. Какой учетной записью пользователя нужно воспользоваться для подключения компьютеров к этому домену?
Пользователи, добавленные в группу администрирования (например, "Администраторы контроллера домена AAD"), могут присоединять компьютеры к домену. Кроме того, пользователям в этой группе предоставляется доступ с помощью удаленного рабочего стола к компьютерам, которые были подключены к домену.

#### <a name="can-i-wield-domain-administrator-privileges-for-the-domain-provided-by-azure-ad-domain-services"></a>Можно ли воспользоваться привилегиями администратора домена для домена, предоставляемого доменными службами AD Azure?
Нет. Вам не предоставляются права администратора для управляемого домена. Вам не будут доступны привилегии "Администратор домена" и "Администратор предприятия" в этом домене. Существующим группам администраторов домена и администраторов предприятия в каталоге Azure AD также не предоставляются права администратора домена или администратора предприятия в домене.

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-domains-provided-by-azure-ad-domain-services"></a>Можно ли изменить членство в группах с помощью LDAP или других средств администрирования AD для доменов, предоставляемых доменными службами Azure AD?
Нет. В доменах, поддерживаемых доменными службами Azure AD, членство в группах изменить нельзя. То же касается и атрибутов пользователей. Однако членство в группах или атрибуты пользователей можно изменить в Azure AD или в локальном домене. Такие изменения автоматически синхронизируются с доменными службами Azure AD.

#### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Через какое время изменения в каталоге Azure AD будут отображаться в управляемом домене?
Изменения, внесенные в каталог Azure AD с помощью пользовательского интерфейса Azure AD или PowerShell, синхронизируются с управляемым доменом. Процесс синхронизации происходит в фоновом режиме. После однократной начальной синхронизации каталога обычно необходимо около 20 минут, чтобы внесенные в Azure AD изменения отобразились в управляемом домене.

#### <a name="can-i-extend-the-schema-of-the-domain-provided-by-azure-ad-domain-services"></a>Могу ли я расширить схему доменом, предоставляемым доменными службами Azure AD?
Нет. Схемой управляемого домена управляет корпорация Майкрософт. Расширения схемы не поддерживаются в доменных службах Azure AD.

#### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Можно ли изменять или добавлять записи DNS в управляемом домене?
Да. Пользователи, принадлежащие к группе "Администраторы контроллера домена AAD", получают права администратора DNS, позволяющие изменять записи DNS в управляемом домене. Эти пользователи могут использовать консоль диспетчера DNS на компьютере под управлением Windows Server, присоединенном к управляемому домену, для управления службами DNS. Чтобы использовать консоль диспетчера DNS, установите "Средства DNS-сервера", которые входят в необязательный компонент "Средства удаленного администрирования сервера" на сервере. Дополнительные сведения о [служебных программам для администрирования, мониторинга и устранения неполадок DNS](https://technet.microsoft.com/library/cc753579.aspx) доступны в библиотеке TechNet.

### <a name="billing-and-availability"></a>Выставление счетов и доступность
#### <a name="is-azure-ad-domain-services-a-paid-service"></a>Являются ли доменные службы Azure AD платной службой?
Да. Дополнительные сведения см. на [странице с расценками](https://azure.microsoft.com/pricing/details/active-directory-ds/).

#### <a name="is-there-a-free-trial-for-the-service"></a>Существует ли бесплатная пробная версия для службы?
Эта служба включена в бесплатную пробную версию Azure. Вы можете зарегистрировать [бесплатную пробную версию Azure на один месяц](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Можно ли получить доменные службы Azure AD в составе Enterprise Mobility Suite (EMS)? Требуется ли Azure AD Premium для использования доменных служб Azure AD?
Нет. Доменные службы Azure — это служба Azure с оплатой по мере использования, не входящая в предложение EMS. Доменные службы Azure AD можно использовать со всеми выпусками Azure AD ("Бесплатный", "Базовый" и "Премиум"). Счет выставляется каждый час в зависимости от использования.

#### <a name="what-azure-regions-is-the-service-available-in"></a>В каких регионах Azure доступна служба?
Перейдите на страницу [служб Azure по регионам](https://azure.microsoft.com/regions/#services/), чтобы просмотреть список регионов Azure, в которых доступны доменные службы Azure AD.

