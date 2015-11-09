<properties 
	pageTitle="Настройка интеграции хранилища ключей Azure для SQL Server на виртуальных машинах Azure"
	description="Узнайте, как автоматизировать настройку шифрования SQL Server для использования с хранилищем ключей Azure. В этом разделе описываются способы использования интеграции хранилища ключей Azure с виртуальными машинами SQL Server." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="10/23/2015"
	ms.author="jroth"/>

# Настройка интеграции хранилища ключей Azure для SQL Server на виртуальных машинах Azure

## Обзор
Существует несколько функций шифрования SQL Server, например [прозрачное шифрование данных (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [шифрование на уровне столбцов (CLE)](https://msdn.microsoft.com/library/ms173744.aspx), и [шифрование резервной копии](https://msdn.microsoft.com/library/dn449489.aspx). Эти формы шифрования требуют хранить используемые для шифрования ключи и управлять ими. Хранилище ключей Azure (AKV) предназначено для повышения безопасности и управления этими ключами в расположении высокой надежности и безопасности. [Соединитель SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) позволяет SQL Server использовать эти ключи из хранилища ключей Azure.

Если вы используете SQL Server с локальными машинами, выполните [шаги для доступа к хранилищу ключей Azure с локального компьютера под управлением SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Однако в случае SQL Server на виртуальных машинах Azure можно сэкономить время, воспользовавшись функцией *интеграции хранилища ключей Azure*. С помощью нескольких командлетов Azure PowerShell для включения этой функции можно автоматизировать настройки, необходимые виртуальной машине SQL для доступа к вашему хранилищу ключей.

Если эта функция включена, она автоматически устанавливает соединитель SQL Server, настраивает поставщик расширенного управления ключами для доступа к хранилищу ключей Azure и создает учетные данные, которые позволяют получить доступ к вашему хранилищу. Если вы изучили шаги в ранее упомянутой документации для локального компьютера, то увидели, что эта функция автоматизирует шаги 2 и 3. Единственное, что останется сделать вручную, — создать хранилище ключей и ключи. Далее вся настройка виртуальной машины SQL выполняется автоматически. После завершения установки этой функции можно выполнять инструкции T-SQL, чтобы начать шифрование базы данных или резервное копирование как обычно.

## Подготовка к интеграции AKV
Чтобы использовать интеграцию хранилища ключей Azure для настройки виртуальной машины с SQL Server, необходимо выполнить несколько предварительных условий:

1.	[Установите Azure PowerShell](#install-azure-powershell)
2.	[Создайте Azure Active Directory](#create-an-azure-active-directory)
3.	[Создайте хранилище ключей.](#create-a-key-vault)

В следующих разделах описаны эти предварительные условия и представлены сведения, необходимые для использования командлетов PowerShell в дальнейшем.

### Установка Azure PowerShell
Убедитесь, что установлен последний пакет SDK Azure PowerShell. (Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).)

### Создайте Azure Active Directory
Во-первых, в вашей подписке должна быть служба [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD). Среди прочих преимуществ она дает возможность предоставить доступ к вашему хранилищу ключей для определенных пользователей и приложений.

Далее необходимо зарегистрировать приложение в AAD. Это позволит получить учетную запись субъекта-службы, имеющую доступ к вашему хранилищу ключей, которое потребуется виртуальной машине. Вы найдете эти действия в статье о хранилище ключей Azure в разделе [Регистрация приложения в Azure Active Directory](../key-vault/key-vault-get-started.md#register) , или вы можете просмотреть действия на снимках экранов в разделе **Получение удостоверения для приложения** [этой записи блога](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Перед выполнением этих шагов обратите внимание, что во время этой регистрации необходимо собрать следующие сведения, которые понадобятся в дальнейшем при включении интеграции хранилища ключей Azure на виртуальной машине с SQL.

- После добавления приложения найдите **ИДЕНТИФИКАТОР КЛИЕНТА** на вкладке **НАСТРОЙКА**. ![Идентификатор клиента Azure Active Directory](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-client-id.png)
	
	Идентификатор клиента позже назначается параметру **$spName** (имя субъекта-службы) в сценарии PowerShell для обеспечения интеграции хранилища ключей Azure. 
- Кроме того, во время выполнения этой процедуры при создании ключа скопируйте секрет ключа, как показано на следующем снимке экрана. Этот секрет ключа позже назначается параметру **$spSecret** (секрет субъекта-службы) в сценарии PowerShell. ![Секрет Azure Active Directory](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-sp-secret.png)
- Необходимо авторизовать этот новый идентификатор клиента, предоставив следующие разрешения на доступ: **encrypt**, **decrypt**, **wrapKey**, **unwrapKey**, **sign** и **verify**. Это делается с помощью командлета [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607%28v=azure.98%29.aspx). Дополнительные сведения см. в разделе [Авторизация приложения на использование ключа или секрета](../key-vault/key-vault-get-started.md#authorize).

### Создайте хранилище ключей.
Чтобы использовать хранилище ключей Azure для хранения ключей, которые будут использоваться для шифрования на виртуальной машине, необходим доступ к хранилищу ключей. Если вы еще не настроили ваше хранилище ключей, создайте его, выполнив следующие действия, описанные в разделе [Приступая к работе с хранилищем ключей Azure](../key-vault/key-vault-get-started.md). Перед выполнением этих шагов обратите внимание, что во время этой настройки необходимо собрать некоторые сведения, которые понадобятся в дальнейшем при включении интеграции хранилища ключей Azure на виртуальной машине с SQL.

При создании хранилища ключей запишите возвращенное свойство **vaultUri**, которое является URL-адресом хранилища ключей. В примере, приведенном на этом шаге и показанном ниже, именем хранилища ключей является ContosoKeyVault, поэтому URL-адрес хранилища ключей будет https://contosokeyvault.vault.azure.net/.

![Секрет Azure Active Directory](./media/virtual-machines-sql-server-azure-key-vault-integration/new-azurekeyvault.png)
 
URL-адрес хранилища ключей позже назначается параметру **$akvURL** в сценарии PowerShell для включения интеграции хранилища ключей Azure.

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
		Get-AzureVM –ServiceName $serviceName –Name $vmName | Set-AzureVMSqlServerExtension –KeyVaultCredentialSettings $akvs | Update-AzureVM

Расширение агента SQL IaaS обновит виртуальную машину SQL до этой новой конфигурации.

## Дальнейшие действия
После включения интеграции хранилища ключей Azure вы сможете включить шифрование SQL Server на своей виртуальной машине с SQL. Во-первых, необходимо создать асимметричный ключ в вашем хранилище ключей и симметричный ключ в SQL Server на виртуальной машине. После этого вы сможете выполнять инструкции T-SQL для включения шифрования базы данных и резервных копий.

Существует несколько способов шифрования, преимуществами которых вы можете воспользоваться.

- [Прозрачное шифрование данных (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
- [Зашифрованные резервные копии](https://msdn.microsoft.com/library/dn449489.aspx)
- [Шифрование на уровне столбцов (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Следующие сценарии Transact-SQL содержат примеры для каждого из этих вариантов.

>[AZURE.NOTE]Каждый пример основан на двух вещах: асимметричном ключе из хранилища ключей **CONTOSO\_KEY** и учетных данных, созданных интеграцией AKV, с именем **Azure\_EKM\_TDE\_cred**.

### Прозрачное шифрование данных (TDE)
1. Создайте имя входа SQL Server для использования компонентом Database Engine для прозрачного шифрования данных, а затем добавьте учетные данные.
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it loads a database 
		-- encrypted by TDE.
		CREATE LOGIN TDE_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the TDE Login to add the credential for use by the 
		-- Database Engine to access the key vault
		ALTER LOGIN TDE_Login 
		ADD CREDENTIAL Azure_EKM_TDE_cred;
		GO
	
2. Создайте ключ шифрования базы данных, который будет использоваться для прозрачного шифрования данных.
	
		USE ContosoDatabase;
		GO
		
		CREATE DATABASE ENCRYPTION KEY 
		WITH ALGORITHM = AES_128 
		ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the database to enable transparent data encryption.
		ALTER DATABASE ContosoDatabase 
		SET ENCRYPTION ON;
		GO

### Зашифрованные резервные копии
1. Создайте имя входа SQL Server для использования компонентом Database Engine для шифрования резервных копий, а затем добавьте учетные данные.
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it is encrypting the backup.
		CREATE LOGIN Backup_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO 
		
		-- Alter the Encrypted Backup Login to add the credential for use by 
		-- the Database Engine to access the key vault
		ALTER LOGIN Backup_Login 
		ADD CREDENTIAL Azure_EKM_Backup_cred ;
		GO
	
2. Выполните резервное копирование базы данных, указав шифрование с помощью асимметричного ключа, хранящегося в хранилище ключей.
	
		USE master;
		BACKUP DATABASE [DATABASE_TO_BACKUP]
		TO DISK = N'[PATH TO BACKUP FILE]' 
		WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD, 
		ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
		GO

### Шифрование на уровне столбцов (CLE)
Этот скрипт создает симметричный ключ, защищенный асимметричным ключом в хранилище ключей, и затем использует симметричный ключ для шифрования данных в базе данных.

	CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
	WITH ALGORITHM=AES_256
	ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	DECLARE @DATA VARBINARY(MAX);
	
	--Open the symmetric key for use in this session
	OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY 
	DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	--Encrypt syntax
	SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));
	
	-- Decrypt syntax
	SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));
	
	--Close the symmetric key
	CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;

## Дополнительные ресурсы
Дополнительные сведения об использовании этих возможностей шифрования см. в разделе [Использование расширенного управления ключами с функциями шифрования SQL Server](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Обратите внимание, что действия, описанные в этой статье, предполагают, что у вас уже есть SQL Server на виртуальной машине Azure. Если нет, см. раздел [Подготовка виртуальной машины с SQL Server в Azure](virtual-machines-provision-sql-server.md). Другие темы, связанные с запуском SQL Server на виртуальных машинах Azure, рассматриваются в статье [Общие сведения об SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=Nov15_HO1-->