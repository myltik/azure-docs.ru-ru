---
title: "Использование кластера пакета HPC 2016 в Azure | Документация Майкрософт"
description: "Узнайте, как развернуть кластер пакета HPC 2016 в Azure."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3dde6a68-e4a6-4054-8b67-d6a90fdc5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/15/2016
ms.author: danlep
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 88d1f4e29f38ba1a6bef57c2da43bee205575eee
ms.contentlocale: ru-ru
ms.lasthandoff: 03/31/2017

---
# <a name="deploy-an-hpc-pack-2016-cluster-in-azure"></a>Развертывание кластера пакета HPC 2016 в Azure

Выполните действия, описанные в этой статье, чтобы развернуть кластер [пакета Microsoft HPC 2016](https://technet.microsoft.com/library/cc514029) на виртуальных машинах Azure. Пакет HPC — это бесплатное решение Майкрософт для высокопроизводительных вычислений, созданное на основе технологий Microsoft Azure и Windows Server, которое поддерживает широкий диапазон рабочих нагрузок высокопроизводительных вычислительных систем.

Используйте один из [шаблонов Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) для развертывания кластера пакета HPC 2016. Есть несколько вариантов топологии кластера с разным числом головных узлов кластера и с вычислительными узлами на основе Linux или Windows.

## <a name="prerequisites"></a>Предварительные требования

### <a name="pfx-certificate"></a>Сертификат PFX

Для работы кластера пакета Microsoft HPC 2016 необходим сертификат PFX, позволяющий защитить связь между узлами HPC. Сертификат должен отвечать приведенным ниже требованиям.

* Он должен иметь закрытый ключ, поддерживающий обмен ключами.
* Для ключей используются цифровая подпись и шифрование.
* Расширенное использование ключей включает в себя аутентификацию клиента и сервера.

Если у вас еще нет сертификата, который отвечает этим требованиям, вы можете запросить сертификат из центра сертификации. Кроме того, можно использовать приведенные ниже команды, чтобы создать самозаверяющий сертификат на основе операционной системы, в которой выполняется команда, и экспортировать PFX-файл сертификата с закрытым ключом.

* **Для Windows 10 или Windows Server 2016** выполните встроенный командлет PowerShell **New-SelfSignedCertificate** следующим образом.

  ```PowerShell
  New-SelfSignedCertificate -Subject "CN=HPC Pack 2016 Communication" -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2") -CertStoreLocation cert:\CurrentUser\My -KeyExportPolicy Exportable -NotAfter (Get-Date).AddYears(5)
  ```
* **Для операционных систем, предшествующих Windows 10 или Windows Server 2016 Technical Preview**, скачайте [генератор самозаверяющих сертификатов](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) из Центра сценариев Microsoft. Извлеките его содержимое и выполните приведенные ниже команды в командной строке PowerShell.

    ```PowerShell 
    Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  
    New-SelfSignedCertificateEx -Subject "CN=HPC Pack 2016 Communication" -KeySpec Exchange -KeyUsage "DigitalSignature,KeyEncipherment" -EnhancedKeyUsage "Server Authentication","Client Authentication" -StoreLocation CurrentUser -Exportable -NotAfter (Get-Date).AddYears(5)
    ```

### <a name="upload-certificate-to-an-azure-key-vault"></a>Передача сертификата в хранилище ключей Azure

Перед развертыванием кластера HPC скачайте сертификат из [хранилища ключей Azure](../../key-vault/index.md) как секрет и запишите приведенные ниже данные, чтобы использовать их при развертывании: **имя хранилища**, **группа ресурсов хранилища**, **URL-адрес сертификата** и **отпечаток сертификата**.

Ниже приведен пример сценария PowerShell для передачи сертификата. Дополнительную информацию о передаче сертификата в хранилище ключей Azure см. в статье [Приступая к работе с хранилищем ключей Azure](../../key-vault/key-vault-get-started.md).

```powershell
#Give the following values
$VaultName = "mytestvault"
$SecretName = "hpcpfxcert"
$VaultRG = "myresourcegroup"
$location = "westus"
$PfxFile = "c:\Temp\mytest.pfx"
$Password = "yourpfxkeyprotectionpassword"
#Validate the pfx file
try {
    $pfxCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $PfxFile, $Password
}
catch [System.Management.Automation.MethodInvocationException]
{
    throw $_.Exception.InnerException
}
$thumbprint = $pfxCert.Thumbprint
$pfxCert.Dispose()
# Create and encode the JSON object
$pfxContentBytes = Get-Content $PfxFile -Encoding Byte
$pfxContentEncoded = [System.Convert]::ToBase64String($pfxContentBytes)
$jsonObject = @"
{
"data": "$pfxContentEncoded",
"dataType": "pfx",
"password": "$Password"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
#Create an Azure key vault and upload the certificate as a secret
$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
$rg = Get-AzureRmResourceGroup -Name $VaultRG -Location $location -ErrorAction SilentlyContinue
if($null -eq $rg)
{
    $rg = New-AzureRmResourceGroup -Name $VaultRG -Location $location
}
$hpcKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultRG -Location $location -EnabledForDeployment -EnabledForTemplateDeployment
$hpcSecret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secret
"The following Information will be used in the deployment template"
"Vault Name             :   $VaultName"
"Vault Resource Group   :   $VaultRG"
"Certificate URL        :   $($hpcSecret.Id)"
"Certificate Thumbprint :   $thumbprint"

```


## <a name="supported-topologies"></a>Поддерживаемые топологии

Выберите один из [шаблонов Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) для развертывания кластера пакета HPC 2016. Ниже в общих чертах описана архитектура трех поддерживаемых топологий кластеров. Топологии с высоким уровнем доступности содержат несколько головных узлов кластера.

1. Высокодоступный кластер с доменом Active Directory

    ![Высокодоступный кластер в домене AD](./media/hpcpack-2016-cluster/haad.png)



2. Высокодоступный кластер без домена Active Directory

    ![Высокодоступный кластер без домена AD](./media/hpcpack-2016-cluster/hanoad.png)

3. Кластер с одним головным узлом

   ![Кластер с одним головным узлом](./media/hpcpack-2016-cluster/singlehn.png)


## <a name="deploy-a-cluster"></a>Развертывание кластера

Чтобы создать кластер, выберите шаблон и щелкните **Развертывание в Azure**. На [портале Azure](https://portal.azure.com) укажите параметры шаблона, как описано ниже. Каждый шаблон создает все ресурсы Azure, необходимые для инфраструктуры кластера HPC. Эти ресурсы включают в себя виртуальную сеть Azure, общедоступный IP-адрес, балансировщик нагрузки (только для высокодоступного кластера), сетевые интерфейсы, группы доступности, учетные записи хранения и виртуальные машины.

### <a name="step-1-select-the-subscription-location-and-resource-group"></a>Шаг 1. Выбор подписки, расположения и группы ресурсов

**Подписка** и **расположение** должны совпадать с теми, которые были указаны после передачи сертификата PFX (см раздел "Предварительные требования"). Мы рекомендуем создать для развертывания **группу ресурсов**.

### <a name="step-2-specify-the-parameter-settings"></a>Шаг 2. Настройка значений параметров

Введите или измените значения параметров шаблона. Щелкните значок рядом с любым параметром, чтобы получить справочные сведения. Ознакомьтесь также с рекомендациями по [доступным размерам виртуальных машин](sizes.md).

Укажите значения, записанные на этапе выполнения предварительных требований, для следующих параметров: **Имя хранилища**, **Vault resource group** (Группа ресурсов хранилища), **URL-адрес сертификата** и **Отпечаток сертификата**.

### <a name="step-3-review-legal-terms-and-create"></a>Шаг 3. Просмотр юридических условий и создание
Щелкните **Просмотреть юридические условия**, чтобы просмотреть условия. Если вы согласны, щелкните **Приобрести**, а затем нажмите кнопку **Создать**, чтобы начать развертывание.

## <a name="connect-to-the-cluster"></a>Подключение к кластеру
1. После развертывания кластера пакета HPC перейдите на [портал Azure](https://portal.azure.com). Щелкните **Группы ресурсов** и найдите группу ресурсов, в которой был развернут кластер. Вы можете найти виртуальные машины головных узлов.

    ![Головные узлы кластера на портале](./media/hpcpack-2016-cluster/clusterhns.png)

2. Щелкните один головной узел (в высокодоступном кластере щелкните любой из головных узлов). В области **Основные компоненты** можно найти общедоступный IP-адрес или полное DNS-имя кластера.

    ![Параметры подключения кластера](./media/hpcpack-2016-cluster/clusterconnect.png)

3. Щелкните **Подключение**, чтобы войти на любой из головных узлов с помощью протокола удаленного рабочего стола с учетной записью администратора, указанной вами. Если развернутый кластер находится в домене Active Directory, то имя пользователя имеет вид <privateDomainName>\<имя_администратора> (например, hpc.local\hpcadmin).

## <a name="next-steps"></a>Дальнейшие действия
* Отправьте задания в свой кластер. Ознакомьтесь с разделами [Отправка заданий в кластер пакета HPC в Azure](hpcpack-cluster-submit-jobs.md) и [Manage an HPC Pack 2016 cluster in Azure using Azure Active Directory](hpcpack-cluster-active-directory.md) (Управление кластером пакета HPC 2016 в Azure с помощью Azure Active Directory).


