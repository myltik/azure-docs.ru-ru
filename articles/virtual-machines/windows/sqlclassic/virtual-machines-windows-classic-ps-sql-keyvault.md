---
title: "Интеграция Key Vault с SQL Server на виртуальных машинах Windows в Azure (классическая модель) | Документация Майкрософт"
description: "Узнайте, как автоматизировать настройку шифрования SQL Server для использования с хранилищем ключей Azure. В этой статье описываются способы использования интеграции хранилища ключей Azure с виртуальными машинами SQL Server, созданными в классической модели развертывания."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 2a9ac5763bb934bd0646e47c3936f7bdd0d603b1
ms.contentlocale: ru-ru
ms.lasthandoff: 03/25/2017

---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Настройка интеграции хранилища ключей Azure для SQL Server на виртуальных машинах Azure (классическая модель)
> [!div class="op_single_selector"]
> * [Диспетчер ресурсов](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Классический](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Обзор
Существует несколько функций шифрования SQL Server, например [прозрачное шифрование данных (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [шифрование на уровне столбцов (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) и [шифрование резервной копии](https://msdn.microsoft.com/library/dn449489.aspx). Эти формы шифрования требуют хранить используемые для шифрования ключи и управлять ими. Хранилище ключей Azure (AKV) предназначено для повышения безопасности и управления этими ключами в расположении высокой надежности и безопасности. [Соединитель SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) позволяет SQL Server использовать эти ключи из хранилища ключей Azure.

> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

Если вы используете SQL Server с локальными машинами, выполните [шаги для доступа к хранилищу ключей Azure с локальной виртуальной машины SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Однако в случае SQL Server на виртуальных машинах Azure можно сэкономить время, воспользовавшись функцией *интеграции хранилища ключей Azure* . С помощью нескольких командлетов Azure PowerShell для включения этой функции можно автоматизировать настройки, необходимые виртуальной машине SQL для доступа к вашему хранилищу ключей.

Если эта функция включена, она автоматически устанавливает соединитель SQL Server, настраивает поставщик расширенного управления ключами для доступа к хранилищу ключей Azure и создает учетные данные, которые позволяют получить доступ к вашему хранилищу. Если вы изучили шаги в ранее упомянутой документации для локального компьютера, то увидели, что эта функция автоматизирует шаги 2 и 3. Единственное, что останется сделать вручную, — создать хранилище ключей и ключи. Далее вся настройка виртуальной машины SQL выполняется автоматически. После завершения установки этой функции можно выполнять инструкции T-SQL, чтобы начать шифрование базы данных или резервное копирование как обычно.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Настройка интеграции AKV
Используйте PowerShell для настройки интеграции хранилища ключей Azure. В следующих разделах приведены общие сведения о требуемых параметрах, а также пример сценария PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Установка расширения IaaS для SQL Server
Сначала [установите расширение IaaS для SQL Server](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Описание входных параметров
В следующей таблице перечислены параметры, необходимые для выполнения сценария PowerShell в следующем разделе.

| Параметр | Описание | Пример |
| --- | --- | --- |
| **$akvURL** |**URL-адрес хранилища ключей** |https://contosokeyvault.vault.azure.net/ |
| **$spName** |**Имя субъекта-службы** |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **$spSecret** |**Секрет субъекта-службы** |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **$credName** |**Учетное имя**: интеграция AKV создает учетные данные в рамках SQL Server, позволяя виртуальной машине иметь доступ к хранилищу ключей. Выберите имя для этих учетных данных. |mycred1 |
| **$vmName** |**Имя виртуальной машины**: имя ранее созданной виртуальной машины с SQL. |myvmname |
| **$serviceName** |**Имя службы**: имя облачной службы, связанной с виртуальной машиной с SQL. |mycloudservicename |

### <a name="enable-akv-integration-with-powershell"></a>Включение интеграции AKV с помощью PowerShell
Командлет **New-AzureVMSqlServerKeyVaultCredentialConfig** создает объект конфигурации для интеграции хранилища ключей Azure. **Set-AzureVMSqlServerExtension** настраивает эту интеграцию с помощью параметра **KeyVaultCredentialSettings**. Ниже показано, как использовать эти команды.

1. В Azure PowerShell сначала настройте входные параметры, указав необходимые значения, как описано в предыдущих разделах этой статьи. Следующий сценарий является примером.
   
        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Выполните следующий сценарий для настройки и включения интеграции AKV.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Расширение агента SQL IaaS обновит виртуальную машину SQL до этой новой конфигурации.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]


