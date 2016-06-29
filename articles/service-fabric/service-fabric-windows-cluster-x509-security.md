<properties
   pageTitle="Подключение к защищенному частному кластеру | Microsoft Azure"
   description="В этой статье описывается, как защитить связь в автономном или частном кластере, а также между клиентами и кластером."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/10/2016"
   ms.author="dkshir"/>

# Защита автономного кластера под управлением Windows с помощью сертификатов

В этой статье описывается, как защитить обмен данными между различными узлами автономного кластера под управлением Windows, а также как аутентифицировать клиентов, подключающихся к этому кластеру, с помощью сертификатов X.509. Это гарантирует, что только авторизованные пользователи могут получить доступ к кластеру для развертывания приложений и выполнения задач управления. Безопасность на основе сертификатов необходимо включить в кластере при его создании. Дополнительные сведения о безопасности обмена данными между узлами, между клиентом и узлом и об управлении доступом на основе ролей см. в разделе [Сценарии обеспечения безопасности кластера](service-fabric-cluster-security.md).

## Необходимые сертификаты

Сначала нужно [скачать пакет автономного кластера](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) на один из узлов в кластере. В скачанном пакете вы найдете файл **ClusterConfig.X509.json**. Откройте его в режиме *редактирования* и ознакомьтесь с разделом **security** на вкладке **properties**.

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
			"ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
        }
    },

В этом разделе описываются все сертификаты, необходимые для обеспечения безопасности автономного кластера Windows. Чтобы включить безопасность на основе сертификатов, присвойте параметрам **ClusterCredentialType** и **ServerCredentialType** значение *X509*.

>[AZURE.NOTE] [Отпечаток](https://en.wikipedia.org/wiki/Public_key_fingerprint) — это основной идентификатор сертификата. Сведения о том, как получить отпечаток создаваемых сертификатов, см. [здесь] \(https://msdn.microsoft.com/library/ms734695(v=vs.110).aspx).

В следующей таблице перечислены фактические сертификаты, которые понадобятся при настройке кластера.

|**Параметры CertificateInformation**|**Описание**|
|-----------------------|--------------------------|
|ClusterCertificate|Этот сертификат нужен, чтобы защитить связь между узлами кластера. Вы можете использовать два разных сертификата для отработки отказа — основной и дополнительный. Укажите отпечаток основного сертификата в разделе **Thumbprint**, а отпечаток дополнительного сертификата — в переменных **ThumbprintSecondary**.|
|ServerCertificate|Этот сертификат предоставляется клиенту при попытке подключиться к этому кластеру. Для удобства можно использовать один сертификат для параметров *ClusterCertificate* и *ServerCertificate*. Вы можете использовать два разных сертификата сервера для отработки отказа — основной и дополнительный. Укажите отпечаток основного сертификата в разделе **Thumbprint**, а отпечаток дополнительного сертификата — в переменных **ThumbprintSecondary**. |
|ClientCertificateThumbprints|Это набор сертификатов, которые требуется установить на клиентских компьютерах, прошедших аутентификацию. На компьютерах, которым нужно предоставить доступ к кластеру и выполняющимся на нем приложениям, можно установить несколько различных клиентских сертификатов. Укажите отпечаток каждого сертификата в переменной **CertificateThumbprint**. Если для параметра **IsAdmin** задано значение *true*, то с клиентского компьютера с установленным сертификатом можно выполнять различные действия управления кластером. Если для параметра **IsAdmin** задано значение *false*, клиент может получить доступ только к приложениям, выполняемым на кластере.|
|ClientCertificateCommonNames|Укажите общее имя первого сертификата клиента для параметра **CertificateCommonName**. **CertificateIssuerThumbprint** — это отпечаток издателя сертификата. Дополнительные сведения об общих именах и издателе см. в статье [Работа с сертификатами] (https://msdn.microsoft.com/library/ms731899(v=vs.110).aspx).|


## Установка сертификатов

Чтобы защитить связь между кластерами, сначала необходимо получить сертификаты X.509 для узлов кластера. Кроме того, чтобы ограничить подключение к этому кластеру для авторизованных компьютеров и пользователей, необходимо получить и установить сертификаты для этих потенциальных клиентских компьютеров. Сведения о том, как получить сертификаты X.509, см. в статье [Как получить сертификат (WCF)](https://msdn.microsoft.com/library/aa702761.aspx). Необходимо создать сертификат **PFX**, позволяющий сохранить закрытый ключ. Кроме того, если у вас есть подписка Azure, вы можете создать сертификаты, следуя инструкциям в разделе о [получении сертификатов X.509](service-fabric-secure-azure-cluster-with-certs.md#acquirecerts). После создания сертификат можно установить на узлах кластера, выполнив приведенные ниже шаги. Обратите внимание, чтобы выполнить эти шаги, на узлах должна быть установлена последняя версия Windows PowerShell 3.x. Эти шаги нужно повторить на каждом узле для сертификатов кластера и сервера, а также для всех дополнительных сертификатов.

- Скопируйте PFX-файл на узел.

- Откройте окно PowerShell с правами администратора и выполните следующие команды:
	
		Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My `
		-FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $password -AsPlainText -Force)

Замените *$password* на пароль, который использовался при создании этого сертификата. Замените $PfxFilePath на полный путь к PFX-файлу, скопированному на этот узел.

- Задайте контроль доступа для этого сертификата, чтобы служба Service Fabric могла использовать его, выполняя следующий сценарий:

		$cert = get-item Cert:\LocalMachine\My<CertificateThumbprint>
		$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

		#Note down the string output by this command, this is the container name for your private key. 

		$privateKeyFilePath = 'C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys<PrivateKeyContainerName>'
	
		#Observe the access rights currently assigned to this certificate.
		get-acl $privateKeyFilePath | fl

		#Set the ACL so that the Service Fabric process can access it.
		$acl = get-acl $privateKeyFilePath
		$acl.SetAccessRule((New-Object System.Security.Accesscontrol.FileSystemAccessRule("NT AUTHORITY\NetworkService","FullControl","Allow")))
		Set-Acl $privateKeyFilePath $acl -ErrorAction Stop 
	

Воспользуйтесь этими шагами, чтобы установить сертификаты клиента на компьютерах, которым нужно предоставить доступ к кластеру.


## Дальнейшие действия

После настройки раздела **security** в файле ClusterConfig.X509.json можно приступать к разделу [Создание кластера](service-fabric-cluster-creation-for-windows-server.md#createcluster), чтобы настроить узлы и создать автономный кластер. Используйте файл **ClusterConfig.X509.json** при создании кластера. Например, команда может выглядеть следующим образом:

	cd $ServiceFabricDeployAnywhereFolder
	.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true -Verbose


После защиты автономного кластера под управлением Windows и настройки аутентифицированных клиентов, которые могут к нему подключаться, перейдите к разделу о [подключении к защищенному кластеру с помощью PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster).

<!---HONumber=AcomDC_0615_2016-->