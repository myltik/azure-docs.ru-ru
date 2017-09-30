---
title: "Вопросы и ответы, а также известные проблемы с управляемым удостоверением службы (MSI) для Azure Active Directory"
description: "Известные проблемы с управляемым удостоверением службы для Azure Active Directory."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 09/22/2017
ms.author: skwan
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: 3cfd1eb55a031696635270a56ed5028e3b249543
ms.contentlocale: ru-ru
ms.lasthandoff: 09/22/2017

---

# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Вопросы и ответы, а также известные проблемы с управляемым удостоверением службы (MSI) для Azure Active Directory

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Часто задаваемые вопросы (FAQ)

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Можно ли использовать MSI с библиотекой аутентификации Active Directory (ADAL) или библиотекой аутентификации Microsoft (MSAL)?

Нет, MSI не еще интегрирован с ADAL или MSAL.

### <a name="what-are-the-supported-linux-distributions"></a>Какие дистрибутивы Linux поддерживаются?

MSI поддерживает следующие дистрибутивы Linux: 

- CoreOS Stable
- CentOS 7.1;
- RedHat 7.2;
- Ubuntu 15.04.

Другие дистрибутивы Linux в настоящее время не поддерживаются, поэтому при их использовании установка расширения может завершиться ошибкой.

Расширение работает для CentOS 6.9. Однако из-за отсутствия поддержки системы версии 6.9 при сбое или завершении работы расширения, автоматического перезапуска не произойдет. Оно повторно запустится после перезапуска виртуальной машины. Чтобы перезапустить расширение вручную, см. сведения в разделе о [перезапуске расширения MSI](#how-do-you-restart-the-msi-extension).

### <a name="how-do-you-restart-the-msi-extension"></a>Как перезапустить расширение MSI?
При завершении работы расширения в Windows и некоторых версиях Linux вы можете перезапустить его вручную с помощью следующего командлета:

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Описание 
- Для Windows используется имя расширения и тип ManagedIdentityExtensionForWindows.
- Для Linux используется имя расширения и тип ManagedIdentityExtensionForLinux.

## <a name="known-issues"></a>Известные проблемы

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>На портале Azure не отображается колонка "Конфигурация"

Если колонка "Конфигурация виртуальной машины" для виртуальной машины не отображается, значит, функция MSI еще не включена на портале в вашем регионе.  Повторите попытку позже.  Можно также включить MSI для виртуальной машины с помощью [PowerShell](msi-qs-configure-powershell-windows-vm.md) или [Azure CLI](msi-qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Не удается назначить доступ виртуальным машинам в колонке "Управление доступом (IAM)"

Если на портале Azure в списке **Назначение доступа к** на странице **Управление доступом (IAM)** > **Добавление разрешений** недоступен для выбора пункт **Виртуальная машина**, то функция управляемого удостоверения службы еще не включена на портале в вашем регионе. Повторите попытку позже.  Вы все равно можете выбрать управляемое удостоверение службы для назначения ролей, выполнив поиск субъекта-службы MSI.  Введите имя виртуальной машины в поле **Выбор**, и субъект-служба отобразится в результатах поиска.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Виртуальная машина не запускается после перемещения из группы ресурсов или подписки

Если перемещается запущенная виртуальная машина, то она продолжает работать во время перемещения. Однако если после перемещения виртуальная машина останавливается и перезапускается, то ее запуск завершается сбоем. Эта проблема возникает из-за того, что виртуальная машина не обновляет ссылку на удостоверение MSI и продолжает указывать на него в старой группе ресурсов.

**Возможное решение** 
 
Активируйте обновление на виртуальной машине, чтобы она могла получить правильные значения для MSI. Можно изменить свойства виртуальной машины, чтобы обновить ссылку на удостоверение MSI. Например, можно задать новое значение тега виртуальной машины с помощью следующей команды.

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Эта команда задает новый тег fixVM со значением 1 для виртуальной машины. 
 
После установки этого свойства виртуальная машина обновится и будет использовать правильный универсальный код ресурса (URI) MSI, и вы сможете запустить ее. 
 
После запуска виртуальной машины этот тег можно будет удалить, выполнив следующую команду.

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

