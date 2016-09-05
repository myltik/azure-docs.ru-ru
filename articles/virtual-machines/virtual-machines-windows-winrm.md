<properties
	pageTitle="Настройка доступа WinRM для виртуальных машин в Azure Resource Manager | Microsoft Azure"
	description="Узнайте, как настроить доступ WinRM для использования с виртуальной машиной Azure Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkays"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="singhkay"/>

# Настройка доступа WinRM для виртуальных машин в Azure Resource Manager

## WinRM в управлении службами Azure или Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] Классическая модель развертывания

* Общие сведения об Azure Resource Manager см. в этой [статье](../resource-group-overview.md).
* Сведения о различиях между управлением службами Azure и Azure Resource Manager см. в этой [статье](../resource-manager-deployment-model.md).

Основное различие в настройке конфигурации WinRM между двумя стеками — это как на виртуальной машине устанавливается сертификат. В стеке Azure Resource Manager сертификаты моделируются как ресурсы, управляемые поставщиком ресурсов хранилища ключей. Поэтому пользователь должен предоставить свой собственный сертификат и передать его в хранилище ключей, прежде чем использовать на виртуальной машине.

Ниже приведены шаги, которые необходимо выполнить для настройки виртуальной машины с возможностью подключения WinRM:

1. создать хранилище ключей;
2. создать самозаверяющий сертификат;
3. передать самозаверяющий сертификат в хранилище ключей;
4. получить URL-адрес для самозаверяющего сертификата в хранилище ключей;
5. сослаться на URL-адрес самозаверяющего сертификата при создании виртуальной машины.

## Шаг 1. Создание хранилища ключей

Для создания хранилища ключей можно воспользоваться следующей командой:

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## Шаг 2. Создание самозаверяющего сертификата
Можно создать самозаверяющий сертификат с помощью этого сценария PowerShell.

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## Шаг 3. Передача самозаверяющего сертификата в хранилище ключей

Перед передачей сертификата в хранилище ключей, созданное на шаге 1, необходимо преобразовать его в формат, который будет понятен для поставщика ресурсов Microsoft.Compute. Следующий сценарий PowerShell позволит это сделать:

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## Шаг 4. Получение URL-адреса для самозаверяющего сертификата в хранилище ключей

При подготовке виртуальной машины поставщику ресурсов Microsoft.Compute требуется URL-адрес для секрета в хранилище ключей. Это позволяет поставщику ресурсов Microsoft.Compute скачать секрет и создать эквивалент сертификата на виртуальной машине.

>[AZURE.NOTE]URL-адрес секрета также должен включать в себя версию. Пример URL-адреса выглядит так: https://contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve


#### Шаблоны

Получить ссылку на URL-адрес в шаблоне можно с помощью следующего кода:

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### PowerShell

Также для получения этого URL-адреса можно воспользоваться следующей командой PowerShell:

	$secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## Шаг 5. Создание ссылки на URL-адрес самозаверяющего сертификата при создании виртуальной машины

#### Шаблоны Azure Resource Manager

При создании виртуальной машины с помощью шаблонов ссылки на сертификат задаются в разделе секретов и разделе winRM, как показано ниже.

	"osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Пример шаблона для описанного выше сценария можно найти здесь: [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows).

Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows).

#### PowerShell

	$vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
	$credential = Get-Credential
	$secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
	$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
	$sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
	$CertificateStore = "My"
	$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## Шаг 6. Подключение к виртуальной машине
Перед подключением к виртуальной машине убедитесь, что машина настроена для удаленного управления WinRM. Запустите PowerShell от имени администратора и выполните следующую команду, чтобы убедиться в правильности настроек:

    Enable-PSRemoting -Force

>[AZURE.NOTE] Если вышеописанные действия не дали результата, убедитесь, что служба WinRM запущена. Это можно сделать с помощью команды `Get-Service WinRM`.

Когда настройки выполнены, можно подключиться к виртуальной машине с помощью следующей команды:

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate

<!---HONumber=AcomDC_0824_2016-->