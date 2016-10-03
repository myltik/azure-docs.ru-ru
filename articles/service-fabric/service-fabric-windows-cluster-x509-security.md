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
   ms.date="07/08/2016"
   ms.author="dkshir"/>

# Защита автономного кластера под управлением Windows с помощью сертификатов X.509

В этой статье описывается, как защитить обмен данными между различными узлами автономного кластера под управлением Windows, а также как аутентифицировать клиентов, подключающихся к этому кластеру, с помощью сертификатов X.509. Это гарантирует, что только авторизованные пользователи могут получить доступ к кластеру для развертывания приложений и выполнения задач управления. Безопасность на основе сертификатов необходимо включить в кластере при его создании.

Дополнительные сведения о безопасности кластера, в том числе безопасности обмена данными между узлами, между клиентом и узлом, и об управлении доступом на основе ролей см. в статье [Сценарии защиты кластера Service Fabric](service-fabric-cluster-security.md).

## Необходимые сертификаты

Сначала нужно [скачать пакет автономного кластера](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) на один из узлов в кластере. В скачанном пакете вы найдете файл **ClusterConfig.X509.MultiMachine.json**. Откройте его и ознакомьтесь с разделом **security** в разделе **properties**.

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

В этом разделе описываются сертификаты, необходимые для обеспечения безопасности автономного кластера Windows. Чтобы включить безопасность на основе сертификатов, присвойте параметрам **ClusterCredentialType** и **ServerCredentialType** значение *X509*.

>[AZURE.NOTE] [Отпечаток](https://en.wikipedia.org/wiki/Public_key_fingerprint) — это основной идентификатор сертификата. Узнайте о том, как [извлечь отпечатки создаваемых сертификатов](https://msdn.microsoft.com/library/ms734695.aspx).

В следующей таблице перечислены сертификаты, которые понадобятся при настройке кластера.

|**Параметры CertificateInformation**|**Описание**|
|-----------------------|--------------------------|
|ClusterCertificate|Этот сертификат нужен, чтобы защитить связь между узлами кластера. Для обновления можно использовать два разных сертификата — основной и дополнительный. Укажите отпечаток основного сертификата в разделе **Thumbprint**, а отпечаток дополнительного сертификата — в переменных **ThumbprintSecondary**.|
|ServerCertificate|Этот сертификат предоставляется клиенту при попытке подключиться к этому кластеру. Для удобства можно использовать один сертификат для параметров *ClusterCertificate* и *ServerCertificate*. Для обновления можно использовать два разных сертификата сервера — основной и дополнительный. Укажите отпечаток основного сертификата в разделе **Thumbprint**, а отпечаток дополнительного сертификата — в переменных **ThumbprintSecondary**. |
|ClientCertificateThumbprints|Это набор сертификатов, которые требуется установить на клиентских компьютерах, прошедших аутентификацию. На компьютерах, которым нужно предоставить доступ к кластеру, можно установить несколько различных клиентских сертификатов. Укажите отпечаток каждого сертификата в переменной **CertificateThumbprint**. Если для параметра **IsAdmin** задано значение *true*, то с клиентского компьютера с установленным сертификатом можно выполнять различные действия управления кластером от имени администратора. Если для параметра **IsAdmin** задано значение *false*, то с клиентского компьютера с установленным сертификатом можно выполнять действия, зависящие от прав доступа пользователей, обычно только операции чтения. Дополнительные сведения о ролях см. в статье [Контроль доступа на основе ролей](service-fabric-cluster-security.md/#role-based-access-control-rbac) |
|ClientCertificateCommonNames|Укажите общее имя первого сертификата клиента для параметра **CertificateCommonName**. **CertificateIssuerThumbprint** — это отпечаток издателя сертификата. Дополнительные сведения об общих именах и издателе см. в статье [Работа с сертификатами](https://msdn.microsoft.com/library/ms731899.aspx)|

Ниже приведен пример конфигурации кластера, в котором указаны сертификаты клиента, сервера и кластера.

 ```
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",
    "nodes": [{
        "nodeName": "vm0",
		"metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
      "nodeName": "vm1",
      		"metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
      "iPAddress": "10.7.0.6",
      		"metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
		"diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
		}
        "security": {
            "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ServerCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpoint": "19001",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## Получение сертификатов X.509
Чтобы защитить связь в кластере, сначала необходимо получить сертификаты X.509 для узлов кластера. Кроме того, чтобы разрешить подключение к этому кластеру только для авторизованных компьютеров и пользователей, необходимо получить и установить сертификаты для клиентских компьютеров.

Для защиты кластеров, на которых выполняются рабочие нагрузки в рабочей среде, необходимо использовать сертификат X.509, подписанный [центром сертификации](https://en.wikipedia.org/wiki/Certificate_authority). Дополнительные сведения о получении этих сертификатов см. в статье [Как получить сертификат (WCF)](http://msdn.microsoft.com/library/aa702761.aspx).

Для кластеров, которые применяются для тестирования, можно использовать самозаверяющие сертификаты.

## Создание самозаверяющего сертификата (необязательно)
Защищенный самозаверяющий сертификат создается при помощи сценария *CertSetup.ps1*, который находится в папке пакета SDK для Service Fabric в каталоге *C:\\Program Files\\Microsoft SDKs\\Service Fabric\\ClusterSetup\\Secure*. Измените этот файл и используйте его для создания сертификата с подходящим именем.

Теперь экспортируйте сертификат в PFX-файл, защищенный паролем. Сначала необходимо получить отпечаток сертификата. Запустите приложение certmgr.exe. Перейдите в папку **Local Computer\\Personal** и найдите созданный сертификат. Дважды щелкните файл сертификата, чтобы открыть его, выберите вкладку *Сведения* и прокрутите вниз до поля *Отпечаток*. Вставьте значение отпечатка в команду PowerShell ниже, удалив в нем пробелы. Укажите для параметра *$pswd* безопасный пароль и запустите команду PowerShell.

```   
$pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
Get-ChildItem -Path cert:\localMachine\my<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
```

Чтобы просмотреть подробные сведения об установленном на компьютере сертификате, выполните следующую команду PowerShell:

```
$cert = Get-Item Cert:\LocalMachine\My<Thumbprint>
Write-Host $cert.ToString($true)
```

Кроме того, если у вас есть подписка Azure, вы можете создать сертификаты, следуя инструкциям в разделе о [получении сертификатов X.509](service-fabric-secure-azure-cluster-with-certs.md#acquirecerts).

## Установка сертификатов
После создания сертификаты можно установить на узлах кластера. На узлах должна быть установлена последняя версия Windows PowerShell 3.x. Эти шаги нужно повторить на каждом узле для сертификатов кластера и сервера, а также для дополнительных сертификатов.

1. Скопируйте PFX-файлы на узел.
2. Откройте окно PowerShell с правами администратора и выполните следующие команды: Замените значение *$password* паролем, который использовался при создании этого сертификата, и укажите в качестве значения *$PfxFilePath* полный путь к PFX-файлу, скопированному на этот узел.

    ```
    $pswd = "1234"
    $PfcFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```

3. Затем задайте контроль доступа для этого сертификата, чтобы служба Service Fabric, которая выполняется под учетной записью сетевой службы, могла использовать его, выполняя следующий сценарий. Укажите отпечаток сертификата и имя сетевой службы для учетной записи службы. Чтобы убедиться, что в сертификате используются соответствующие списки ACL, можно воспользоваться средством certmgr.exe и выбрать раздел "Управление закрытыми ключами" в сертификате.

    ```
    param
    (
        [Parameter(Position=1, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$pfxThumbPrint,

        [Parameter(Position=2, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$serviceAccount
        )

        $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; };

        # Specify the user, the permissions and the permission type
        $permission = "$($serviceAccount)","FullControl","Allow"
        $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission;

        # Location of the machine related keys
        $keyPath = $env:ProgramData + "\Microsoft\Crypto\RSA\MachineKeys";
        $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName;
        $keyFullPath = $keyPath + $keyName;

        # Get the current acl of the private key
        $acl = (Get-Item $keyFullPath).GetAccessControl('Access')

        # Add the new ace to the acl of the private key
        $acl.SetAccessRule($accessRule);

        # Write back the new acl
        Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop

        #Observe the access rights currently assigned to this certificate.
        get-acl $keyFullPath| fl
        ```

4. Эти действия необходимо выполнить для каждого сертификата сервера. Воспользуйтесь этими шагами, чтобы установить сертификаты клиента на компьютерах, которым нужно предоставить доступ к кластеру.

## Создание защищенного кластера
После настройки раздела **security** в файле **ClusterConfig.X509.MultiMachine.json** можно приступать к разделу [Создание кластера](service-fabric-cluster-creation-for-windows-server.md#createcluster), чтобы настроить узлы и создать автономный кластер. Используйте файл **ClusterConfig.X509.MultiMachine.json** при создании кластера. Например, команда может выглядеть следующим образом:

```
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```

После защиты автономного кластера под управлением Windows и настройки аутентифицированных клиентов, которые могут к нему подключаться, перейдите к разделу [Подключение к защищенному кластеру с помощью PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster). Например:

```
Connect-ServiceFabricCluster -ConnectionEndpoint 10.7.0.4:19000 -KeepAliveIntervalInSec 10 -X509Credential -ServerCertThumbprint 057b9544a6f2733e0c8d3a60013a58948213f551 -FindType FindByThumbprint -FindValue 057b9544a6f2733e0c8d3a60013a58948213f551 -StoreLocation CurrentUser -StoreName My
```

Если вы выполнили вход на компьютер в кластере с локально установленным сертификатом, подключиться к кластеру и отобразить список узлов можно с помощью следующей команды Powershell:

```
Connect-ServiceFabricCluster
Get-ServiceFabricNode
```
Чтобы удалить кластер, используйте следующую команду:

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json   -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
```

<!---HONumber=AcomDC_0921_2016-->