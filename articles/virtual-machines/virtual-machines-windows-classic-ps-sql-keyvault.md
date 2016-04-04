<properties 
	pageTitle="Настройка интеграции хранилища ключей Azure для SQL Server на виртуальных машинах Azure (классическое развертывание)"
	description="Узнайте, как автоматизировать настройку шифрования SQL Server для использования с хранилищем ключей Azure. В этом разделе описываются способы использования интеграции хранилища ключей Azure с виртуальными машинами SQL Server, созданными в классической модели развертывания." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="12/17/2015"
	ms.author="jroth"/>

# Настройка интеграции хранилища ключей Azure для SQL Server на виртуальных машинах Azure (классическое развертывание)

> [AZURE.SELECTOR]
- [Диспетчер ресурсов](virtual-machines-windows-ps-sql-keyvault.md)
- [Классический](virtual-machines-windows-classic-ps-sql-keyvault.md)

## Обзор
Существует несколько функций шифрования SQL Server, например [прозрачное шифрование данных (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [шифрование на уровне столбцов (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) и [шифрование резервной копии](https://msdn.microsoft.com/library/dn449489.aspx). Эти формы шифрования требуют хранить используемые для шифрования ключи и управлять ими. Хранилище ключей Azure (AKV) предназначено для повышения безопасности и управления этими ключами в расположении высокой надежности и безопасности. [Соединитель SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) позволяет SQL Server использовать эти ключи из хранилища ключей Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.

Если вы используете SQL Server с локальными машинами, выполните [шаги для доступа к хранилищу ключей Azure с локального компьютера под управлением SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Однако в случае SQL Server на виртуальных машинах Azure можно сэкономить время, воспользовавшись функцией *интеграции хранилища ключей Azure*. С помощью нескольких командлетов Azure PowerShell для включения этой функции можно автоматизировать настройки, необходимые виртуальной машине SQL для доступа к вашему хранилищу ключей.

Если эта функция включена, она автоматически устанавливает соединитель SQL Server, настраивает поставщик расширенного управления ключами для доступа к хранилищу ключей Azure и создает учетные данные, которые позволяют получить доступ к вашему хранилищу. Если вы изучили шаги в ранее упомянутой документации для локального компьютера, то увидели, что эта функция автоматизирует шаги 2 и 3. Единственное, что останется сделать вручную, — создать хранилище ключей и ключи. Далее вся настройка виртуальной машины SQL выполняется автоматически. После завершения установки этой функции можно выполнять инструкции T-SQL, чтобы начать шифрование базы данных или резервное копирование как обычно.

[AZURE.INCLUDE [Подготовка к интеграции AKV](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## Настройка интеграции AKV
Используйте PowerShell для настройки интеграции хранилища ключей Azure. В следующих разделах приведены общие сведения о требуемых параметрах, а также пример сценария PowerShell.

### Входные параметры
В следующей таблице перечислены параметры, необходимые для выполнения сценария PowerShell в следующем разделе.

|Параметр|Описание|Пример|
|---|---|---|
|**$akvURL**|**URL-адрес хранилища ключей**|https://contosokeyvault.vault.azure.net/|
|**$spName**|**Имя субъекта-службы**|fde2b411-33d5-4e11-af04eb07b669ccf2|
|**$spSecret**|**Секрет субъекта-службы**|9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=|
|**$credName**|**Учетное имя**: интеграция AKV создает учетные данные в рамках SQL Server, позволяя виртуальной машине иметь доступ к хранилищу ключей. Выберите имя для этих учетных данных.|mycred1|
|**$vmName**|**Имя виртуальной машины**: имя ранее созданной виртуальной машины с SQL.|myvmname|
|**$serviceName**|**Имя службы**: имя облачной службы, связанной с виртуальной машиной с SQL.|mycloudservicename|

### Включение интеграции AKV с помощью PowerShell
Командлет **New-AzureVMSqlServerKeyVaultCredentialConfig** создает объект конфигурации для интеграции хранилища ключей Azure. **Set-AzureVMSqlServerExtension** настраивает эту интеграцию с помощью параметра **KeyVaultCredentialSettings**. Ниже показано, как использовать эти команды.

1. В Azure PowerShell сначала настройте входные параметры, указав конкретные значения, как описано в предыдущих подразделах этого раздела. Следующий сценарий является примером.
	
		$akvURL = "https://contosokeyvault.vault.azure.net/"
		$spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
		$spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
		$credName = "mycred1"
		$vmName = "myvmname"
		$serviceName = "mycloudservicename"
2.	Выполните следующий сценарий для настройки и включения интеграции AKV.
	
		$secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
		$akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
		Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Расширение агента SQL IaaS обновит виртуальную машину SQL до этой новой конфигурации.

[AZURE.INCLUDE [Интеграция AKV: дальнейшие действия](../../includes/virtual-machines-sql-server-akv-next-steps.md)]

<!---HONumber=AcomDC_0323_2016-->