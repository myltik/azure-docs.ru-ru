<properties
   pageTitle="Защита кластера Service Fabric с помощью сертификатов | Microsoft Azure"
   description="Описывается, как защитить кластер Service Fabric с помощью сертификатов X.509."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/27/2016"
   ms.author="chackdan"/>

# Защита кластера Service Fabric в Azure с помощью сертификатов

Кластер Azure Service Fabric — это ресурс, владельцем которого вы являетесь. Во избежание несанкционированного доступа его следует защитить, особенно если на нем выполняются рабочие нагрузки в рабочей среде. Чтобы настроить безопасный кластер Service Fabric с помощью сертификатов X.509, нужен по крайней мере один сертификат X.509 сервера, который необходимо передать в хранилище ключей Azure и использовать в процессе создания кластера.

Эта статья соответствует этапу [Шаг 3. Настройка безопасности](service-fabric-cluster-creation-via-portal.md#step-3--configure-security) процесса создания кластера. Дополнительные сведения об использовании сертификатов X.509 в Service Fabric см. в разделе [Сценарии обеспечения безопасности кластера](service-fabric-cluster-security.md).

Процесс состоит из трех шагов.

1. Получение сертификата x509.
2. Отправка сертификата в хранилище ключей Azure.
3. Предоставление сведений о сертификате и его расположении в ходе создания кластера Service Fabric.

## Шаг 1. Получение сертификатов X.509

Для защиты кластеров, на которых выполняются рабочие нагрузки в рабочей среде, необходимо использовать сертификат X.509, подписанный [центром сертификации](https://en.wikipedia.org/wiki/Certificate_authority). Сведения о получении этих сертификатов приведены в разделе [Как получить сертификат (WCF)](http://msdn.microsoft.com/library/aa702761.aspx).

Для кластеров, которые используются только для тестирования, можно использовать самозаверяющие сертификаты. На шаге 2.5 ниже объясняется, как это сделать.

## Шаг 2. Отправка сертификата X.509 в хранилище ключей

Это сложный процесс, поэтому для его выполнения мы передали в репозиторий Git модуль PowerShell.

### Шаг 2.1
Убедитесь, что на компьютере установлен Azure PowerShell версии 1.0 или более поздней версии. Если вы не сделали этого ранее, выполните инструкции в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

### Шаг 2.2
Скопируйте папку *ServiceFabricRPHelpers* из этого [репозитория Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers) на свой компьютер.

### Шаг 2.3
Откройте окно PowerShell и перейдите в каталог, куда был скачан модуль. Затем импортируйте модуль, используя следующую команду.

```powershell
Import-Module .\ServiceFabricRPHelpers.psm1
```

### Шаг 2.4
Если вы используете уже полученный сертификат, то выполните действия, описанные в этом шаге. В противном случае перейдите к шагу 2.5, в котором объясняется, как создать и развернуть самозаверяющий сертификат в хранилище ключей.

Можно использовать существующую группу ресурсов и хранилище ключей для хранения сертификата. Можно также создать новую группу ресурсов или хранилище ключей, если они отсутствуют. Существующее хранилище ключей должно быть предварительно настроено для поддержки развертывания с помощью этого сценария.

```powershell
Login-AzureRmAccount

Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

Чтобы передать сертификат в группу ресурсов и хранилище ключей, выполните следующий сценарий. Группа ресурсов и хранилище ключей будут созданы, если они не существуют.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
Вот пример заполненного сценария.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

Если сценарий успешно выполнен, вы получите следующие выходные данные, которые потребуются для выполнения шага 3 (Настройка безопасного кластера).

```
Certificate Thumbprint: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A

SourceVault /Resource ID of the key vault :  /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea
```

Теперь у вас есть сведения, необходимые для настройки безопасности кластера. Перейдите к шагу 3.

### Шаг 2.5
Если у вас *нет* сертификата и вы хотите создать самозаверяющий сертификат и передать его в хранилище ключей, выполните описанные ниже действия. Самозаверяющие сертификаты следует использовать только для тестовых, а не рабочих кластеров.

Можно использовать существующую группу ресурсов и хранилище ключей для хранения сертификата. Можно также создать новую группу ресурсов или хранилище ключей, если они отсутствуют. Существующее хранилище ключей должно быть предварительно настроено для поддержки развертывания с помощью этого сценария.

```powershell
Login-AzureRmAccount
Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

Следующий сценарий создаст новую группу ресурсов и (или) хранилище ключей, если они отсутствуют, создаст самозаверяющий сертификат и передаст его в хранилище ключей, а затем выведет новый сертификат в *OutputPath*.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```
В строке *DnsName* содержится одно или несколько DNS-имен, которые необходимо включить в расширение альтернативного имени субъекта сертификата, если копируемый сертификат не задан в параметре CloneCert. Первое DNS-имя также сохраняется как имя субъекта. Если сертификат для подписи не указан, первое DNS-имя также сохраняется как имя издателя. Командлет *Invoke-AddCertToKeyVault* использует командлет [New-SelfSignedCertificate](https://technet.microsoft.com/library/hh848633.aspx) для создания самозаверяющего сертификата.

Вот пример заполненного сценария.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

Если сценарий успешно выполнен, вы получите следующие выходные данные. Они понадобятся для выполнения шага 3.

```
Certificate Thumbprint: 64881409F4D86498C88EEC3697310C15F8F1540F

SourceVault /Resource ID of the key vault : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea
```

## Шаг 3. Настройка безопасного кластера

После передачи ваших сертификатов в хранилище ключей Azure можно создать кластер, защищенных с помощью этих сертификатов. Этот шаг соответствует этапу [Шаг 3. Настройка безопасности](service-fabric-cluster-creation-via-portal.md#step-3--configure-security) процесса создания кластера и показывает, как задать настройки безопасности.

>[AZURE.NOTE]
Необходимые сертификаты указываются в поле "Тип узла" в разделе "Настройки безопасности". Их нужно указывать для каждого типа узла в кластере. Хотя в этой статье описано, как выбрать сертификат на портале, это также можно сделать с помощью шаблона диспетчера ресурсов Azure.

![Снимок экрана: раздел настройки безопасности на портале Azure][SecurityConfigurations_01]

### Обязательные параметры

- **Режим безопасности**. Выберите **Сертификат X509**, чтобы настроить кластер, защищенных с помощью сертификатов X.509.
- **Cluster protection level** (Уровень защиты кластера). Пояснение всех значений см. в статье [Основные сведения об уровне защиты](https://msdn.microsoft.com/library/aa347692.aspx). Несмотря на то, что можно использовать три значения (EncryptAndSign, Sign и None), если вы недостаточно знакомы с этим процессом, лучше оставить значение по умолчанию (EncryptAndSign).
- **Source Vault** (Исходное хранилище). Это идентификатор ресурса хранилища ключей. Он должен быть представлен в следующем формате:

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **URL-адрес сертификата**. Это URL-адрес расположения в хранилище ключей, в которое передан сертификат. Он должен быть представлен в следующем формате:

    ```
    https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- **Отпечаток сертификата**. Это отпечаток сертификата, который можно найти по URL-адресу, указанному ранее.

### Необязательные параметры

 При необходимости вы также можете указать дополнительные сертификаты, которые используются клиентскими компьютерами для выполнения операций в кластере. По умолчанию указанный в обязательных параметрах отпечаток добавляется в список авторизованных отпечатков, разрешенных для операций клиента.

**Admin Client** (Клиент администрирования). С помощью сведений в этом разделе можно проверить, предоставляет ли клиент, подключаемый к конечной точке управления кластера, действительные учетные данные для выполнения операций администрирования и операций только для чтения в кластере. Можно указать несколько сертификатов, которые требуется авторизовать для выполнения операций администрирования.

- **Authorize By** (Авторизация по). Указывает способ поиска сертификата в Service Fabric (по имени субъекта или по отпечатку). Хотя использование имени субъекта для авторизации нежелательно, оно обеспечивает большую гибкость.
- **Имя субъекта**. Требуется только в том случае, если задана авторизация по имени субъекта.
- **Отпечаток издателя**. Обеспечивает дополнительный уровень проверки, которую сервер может выполнить, получив учетные данные клиента.

**Read Only Client** (Клиент только для чтения). С помощью сведений в этом разделе можно проверить, предоставляет ли клиент, подключаемый к конечной точке управления кластера, действительные учетные данные для выполнения операций только для чтения в кластере. Можно указать несколько сертификатов, которые требуется авторизовать для выполнения операций только для чтения.

- **Authorize By** (Авторизация по). Указывает способ поиска сертификата в Service Fabric (по имени субъекта или по отпечатку). Хотя использование имени субъекта для авторизации нежелательно, оно обеспечивает большую гибкость.
- **Имя субъекта**. Требуется только в том случае, если задана авторизация по имени субъекта.
- **Отпечаток издателя**. Обеспечивает дополнительный уровень проверки, которую сервер может выполнить, получив учетные данные клиента.

## Дальнейшие действия
После настройки безопасности на основе сертификатов для кластера возобновите процесс его создания, перейдя к этапу [Шаг 4. Завершение создания кластера](service-fabric-cluster-creation-via-portal.md#step-4--complete-the-cluster-creation).

После создания кластера с защитой на основе сертификатов вы сможете [обновить сертификат](service-fabric-cluster-security-update-certs-azure.md).


<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_02.png

<!---HONumber=AcomDC_0608_2016-->