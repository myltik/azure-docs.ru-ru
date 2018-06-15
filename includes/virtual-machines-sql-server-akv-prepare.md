---
title: включение файла
description: включение файла
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 19be449528481b4e35cad4418f82f2250917966b
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32787422"
---
## <a name="prepare-for-akv-integration"></a>Подготовка к интеграции AKV
Чтобы использовать интеграцию хранилища ключей Azure для настройки виртуальной машины с SQL Server, необходимо выполнить несколько предварительных условий: 

1. [Установите Azure PowerShell](#install)
2. [Создайте Azure Active Directory](#register)
3. [Создайте хранилище ключей.](#createkeyvault)

В следующих разделах описаны эти предварительные условия и представлены сведения, необходимые для использования командлетов PowerShell в дальнейшем.

### <a id="install"></a> Установка Azure PowerShell
Убедитесь, что установлен последний пакет SDK Azure PowerShell. Подробнее: [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a id="register"></a> Регистрация приложения в Azure Active Directory

Во-первых, в вашей подписке должна быть служба [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD). Среди прочих преимуществ она дает возможность предоставить доступ к вашему хранилищу ключей для определенных пользователей и приложений.

Далее необходимо зарегистрировать приложение в AAD. Это позволит получить учетную запись субъекта-службы, имеющую доступ к вашему хранилищу ключей, которое потребуется виртуальной машине. Вы найдете эти действия в статье о хранилище ключей Azure в разделе [Регистрация приложения в Azure Active Directory](../articles/key-vault/key-vault-get-started.md#register), или вы можете просмотреть действия на снимках экранов в разделе **Получение удостоверения для приложения** [этой записи блога](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Перед выполнением этих шагов учтите, что во время этой регистрации необходимо собрать следующие сведения, которые понадобятся в дальнейшем при включении интеграции Azure Key Vault на виртуальной машине SQL.

* После добавления приложения найдите **идентификатор приложения** в колонке **Зарегистрированное приложение**.
    Идентификатор приложения позже назначается параметру **$spName** (имя субъекта-службы) в сценарии PowerShell, чтобы обеспечить интеграцию Azure Key Vault.

   ![Идентификатор приложения](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Во время выполнения этой процедуры при создании ключа скопируйте секрет ключа, как показано на приведенном ниже снимке экрана. Этот секрет ключа позже назначается параметру **$spSecret** (секрет субъекта-службы) в сценарии PowerShell.

   ![Секрет AAD](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* Идентификатор и секрет приложения также будут использоваться для создания учетных данных в SQL Server.

* Необходимо авторизовать этот новый идентификатор клиента, предоставив следующие разрешения на доступ: **encrypt**, **decrypt**, **wrapKey**, **unwrapKey**, **sign** и **verify**. Это делается с помощью командлета [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx). Дополнительные сведения см. в статье [Авторизация приложения на использование ключа или секрета](../articles/key-vault/key-vault-get-started.md#authorize).

### <a id="createkeyvault"></a> Создание хранилища ключей
Чтобы использовать хранилище ключей Azure для хранения ключей, которые будут использоваться для шифрования на виртуальной машине, необходим доступ к хранилищу ключей. Если вы еще не настроили ваше хранилище ключей, создайте его, выполнив следующие действия, описанные в статье [Приступая к работе с хранилищем ключей Azure](../articles/key-vault/key-vault-get-started.md). Перед выполнением этих шагов учтите, что во время этой настройки необходимо собрать некоторые сведения, которые понадобятся в дальнейшем при включении интеграции Azure Key Vault на виртуальной машине SQL.

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

При создании хранилища ключей запишите возвращенное свойство **vaultUri** , которое является URL-адресом хранилища ключей. В примере, приведенном на этом шаге и показанном ниже, именем хранилища ключей является ContosoKeyVault, поэтому URL-адрес хранилища ключей — https://contosokeyvault.vault.azure.net/.

URL-адрес хранилища ключей позже назначается параметру **$akvURL** в сценарии PowerShell для включения интеграции хранилища ключей Azure.

После создания хранилища ключей нужно добавить в него ключ. На этот ключ в дальнейшем будет добавлена ссылка при создании асимметричного ключа SQL Server.
