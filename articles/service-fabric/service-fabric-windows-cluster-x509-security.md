---
title: "Защита кластера Azure Service Fabric в Windows с использованием сертификатов | Документация Майкрософт"
description: "Безопасный обмен данными в автономном или частном кластере Azure Service Fabric, а также между клиентами и кластером."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: fe0ed74c-9af5-44e9-8d62-faf1849af68c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 3715b9dfaa98b83bbb8efffbe5db0090ea5fabe0
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2017
---
# <a name="secure-a-standalone-cluster-on-windows-using-x509-certificates"></a>Защита автономного кластера под управлением Windows с помощью сертификатов X.509
В этой статье описывается, как защитить обмен данными между различными узлами автономного кластера под управлением Windows, а также как аутентифицировать клиентов, подключающихся к этому кластеру, с помощью сертификатов X.509. Это гарантирует, что только авторизованные пользователи могут получить доступ к кластеру для развертывания приложений и выполнения задач управления.  Безопасность на основе сертификатов необходимо включить в кластере при его создании.  

Дополнительные сведения о безопасности кластера, в том числе безопасности обмена данными между узлами, между клиентом и узлом, и об управлении доступом на основе ролей см. в статье [Сценарии защиты кластера Service Fabric](service-fabric-cluster-security.md).

## <a name="which-certificates-will-you-need"></a>Необходимые сертификаты
Сначала нужно [скачать пакет автономного кластера](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) на один из узлов в кластере. В скачанном пакете вы найдете файл **ClusterConfig.X509.MultiMachine.json** . Откройте его и ознакомьтесь с разделом **security** в разделе **properties**.

```JSON
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
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

В этом разделе описываются сертификаты, необходимые для обеспечения безопасности автономного кластера Windows. При указании сертификата кластера задайте для параметра **ClusterCredentialType** значение _**X509**_. При указании сертификата сервера для внешних соединений задайте для параметра **ServerCredentialType** значение _**X509**_. Хотя это не обязательно, рекомендуется иметь оба этих сертификата, чтобы должным образом защитить кластер. Если для этих параметров задано значение *X509*, то вам также нужно указать соответствующие сертификаты, иначе Service Fabric породит исключение. В некоторых сценариях может потребоваться указать только один из параметров, _ClientCertificateThumbprints_ или _ReverseProxyCertificate_. В этих случаях не нужно задавать для _ClusterCredentialType_ или _ServerCredentialType_ значение _X509_.


> [!NOTE]
> [Отпечаток](https://en.wikipedia.org/wiki/Public_key_fingerprint) — это основной идентификатор сертификата. Узнайте о том, как [извлечь отпечатки создаваемых сертификатов](https://msdn.microsoft.com/library/ms734695.aspx) .
> 
> 

В следующей таблице перечислены сертификаты, которые понадобятся при настройке кластера.

| **Параметры CertificateInformation** | **Описание** |
| --- | --- |
| ClusterCertificate |Рекомендуется использовать в тестовой среде. Этот сертификат нужен, чтобы защитить связь между узлами кластера. Для обновления можно использовать два разных сертификата — основной и дополнительный. Укажите отпечаток основного сертификата в разделе **Thumbprint**, а отпечаток дополнительного сертификата — в переменных **ThumbprintSecondary**. |
| ClusterCertificateCommonNames |Рекомендуется использовать в рабочей среде. Этот сертификат нужен, чтобы защитить связь между узлами кластера. Можно использовать одно или два общих имени сертификата для кластера. **CertificateIssuerThumbprint** соответствует отпечатку издателя сертификата. Можно указать несколько отпечатков издателя, если используется более одного сертификата с одним и тем же общим именем.|
| ServerCertificate |Рекомендуется использовать в тестовой среде. Этот сертификат предоставляется клиенту при попытке подключиться к этому кластеру. Для удобства можно использовать один сертификат для параметров *ClusterCertificate* и *ServerCertificate*. Для обновления можно использовать два разных сертификата сервера — основной и дополнительный. Укажите отпечаток основного сертификата в разделе **Thumbprint**, а отпечаток дополнительного сертификата — в переменных **ThumbprintSecondary**. |
| ServerCertificateCommonNames |Рекомендуется использовать в рабочей среде. Этот сертификат предоставляется клиенту при попытке подключиться к этому кластеру. **CertificateIssuerThumbprint** соответствует отпечатку издателя сертификата. Можно указать несколько отпечатков издателя, если используется более одного сертификата с одним и тем же общим именем. Для удобства можно использовать один сертификат для параметров *ClusterCertificateCommonNames* и *ServerCertificateCommonNames*. Можно использовать одно или два общих имени сертификата для сервера. |
| ClientCertificateThumbprints |Это набор сертификатов, которые требуется установить на клиентских компьютерах, прошедших аутентификацию. На компьютерах, которым нужно предоставить доступ к кластеру, можно установить несколько различных клиентских сертификатов. Укажите отпечаток каждого сертификата в переменной **CertificateThumbprint**. Если для параметра **IsAdmin** задано значение *true*, то с клиентского компьютера с установленным сертификатом можно выполнять различные действия управления кластером от имени администратора. Если для параметра **IsAdmin** задано значение *false*, то с клиентского компьютера с установленным сертификатом можно выполнять действия, зависящие от прав доступа пользователей, обычно только операции чтения. Дополнительные сведения о ролях см. в статье [Контроль доступа на основе ролей](service-fabric-cluster-security.md#role-based-access-control-rbac) |
| ClientCertificateCommonNames |Укажите общее имя первого сертификата клиента для параметра **CertificateCommonName**. **CertificateIssuerThumbprint** — это отпечаток издателя сертификата. Дополнительные сведения об общих именах и издателе см. в статье [Работа с сертификатами](https://msdn.microsoft.com/library/ms731899.aspx). |
| ReverseProxyCertificate |Рекомендуется использовать в тестовой среде. Это необязательный сертификат, который можно указать, если вы хотите защитить [обратный прокси-сервер](service-fabric-reverseproxy.md). Если вы используете этот сертификат, обязательно укажите для параметра nodeTypes значение reverseProxyEndpointPort. |
| ReverseProxyCertificateCommonNames |Рекомендуется использовать в рабочей среде. Это необязательный сертификат, который можно указать, если вы хотите защитить [обратный прокси-сервер](service-fabric-reverseproxy.md). Если вы используете этот сертификат, обязательно укажите для параметра nodeTypes значение reverseProxyEndpointPort. |

Ниже приведен пример конфигурации кластера, в котором указаны сертификаты клиента, сервера и кластера. Примечание для сертификатов кластера, сервера и обратного прокси-сервера: нельзя одновременно настроить отпечаток и общее имя для одного типа сертификата.

 ```JSON
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
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 66 8d 9f a8 ee 71 2b a2 f4 37 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
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
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
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

## <a name="certificate-roll-over"></a>Смена сертификатов
Если используется общее имя сертификата вместо отпечатка, чтобы сменить сертификат, не нужно обновлять конфигурацию кластера.
Для обновления отпечатков издателя убедитесь, что новый список отпечатков пересекается со старым списком. Сначала нужно будет обновить конфигурацию, указав новые отпечатки издателя, а затем установить новые сертификаты (сертификат сервера или кластера и сертификаты издателя) в хранилище. Храните старый сертификат издателя в хранилище сертификатов как минимум 2 часа после установки нового сертификата издателя.

## <a name="acquire-the-x509-certificates"></a>Получение сертификатов X.509
Чтобы защитить связь в кластере, сначала необходимо получить сертификаты X.509 для узлов кластера. Кроме того, чтобы разрешить подключение к этому кластеру только для авторизованных компьютеров и пользователей, необходимо получить и установить сертификаты для клиентских компьютеров.

Для защиты кластеров, на которых выполняются рабочие нагрузки в рабочей среде, необходимо использовать сертификат X.509, подписанный [центром сертификации](https://en.wikipedia.org/wiki/Certificate_authority). Сведения о получении этих сертификатов приведены в статье [How to: Obtain a Certificate (WCF)](http://msdn.microsoft.com/library/aa702761.aspx)(Получение сертификата).

Для кластеров, которые применяются для тестирования, можно использовать самозаверяющие сертификаты.

## <a name="optional-create-a-self-signed-certificate"></a>Создание самозаверяющего сертификата (необязательно)
Защищенный самозаверяющий сертификат создается при помощи сценария *CertSetup.ps1*, который находится в папке пакета SDK для Service Fabric в каталоге *C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure*. Измените этот файл, чтобы изменить имя по умолчанию сертификата (найдите значение *CN = ServiceFabricDevClusterCert*). Выполните этот сценарий следующим образом: `.\CertSetup.ps1 -Install`.

Теперь экспортируйте сертификат в PFX-файл, защищенный паролем. Сначала получите отпечаток сертификата. В меню *Запуск* выберите *Управление сертификатами компьютеров*. Перейдите в папку **Local Computer\Personal** и найдите созданный сертификат. Дважды щелкните файл сертификата, чтобы открыть его, выберите вкладку *Сведения* и прокрутите вниз до поля *Отпечаток*. Вставьте значение отпечатка в команду PowerShell ниже, предварительно удалив в нем пробелы.  Укажите для параметра `String` безопасный пароль и выполните приведенную ниже команду PowerShell.

```powershell   
$pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
```

Чтобы просмотреть подробные сведения об установленном на компьютере сертификате, выполните следующую команду PowerShell:

```powershell
$cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
Write-Host $cert.ToString($true)
```

Либо, если у вас есть подписка Azure, перейдите к разделу [Добавление сертификатов в хранилище ключей](service-fabric-cluster-creation-via-arm.md#add-certificate-to-key-vault).

## <a name="install-the-certificates"></a>Установка сертификатов
После создания сертификаты можно установить на узлах кластера. На узлах должна быть установлена последняя версия Windows PowerShell 3.x. Эти шаги нужно повторить на каждом узле для сертификатов кластера и сервера, а также для дополнительных сертификатов.

1. Скопируйте PFX-файлы на узел.
2. Откройте окно PowerShell с правами администратора и выполните следующие команды: Замените значение *$password* паролем, который использовался при создании этого сертификата, и укажите в качестве значения *$PfxFilePath* полный путь к PFX-файлу, скопированному на этот узел.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Затем настройте контроль доступа для этого сертификата, чтобы служба Service Fabric, которая выполняется под учетной записью сетевой службы, могла использовать его, выполняя следующий сценарий. Укажите отпечаток сертификата и имя сетевой службы для учетной записи службы. Можно проверить правильность списков контроля доступа (ACL) для сертификата, открыв сертификат. Для этого щелкните *Запуск* > *Управление сертификатами компьютеров* и просмотрите *Все задачи* > *Управление закрытыми ключами*.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow"
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current acl of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ace to the acl of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new acl
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate.
    get-acl $keyFullPath| fl
    ```
4. Эти действия необходимо выполнить для каждого сертификата сервера. Воспользуйтесь этими шагами, чтобы установить сертификаты клиента на компьютерах, которым нужно предоставить доступ к кластеру.

## <a name="create-the-secure-cluster"></a>Создание защищенного кластера
После настройки в файле **ClusterConfig.X509.MultiMachine.json** раздела **security** можно приступать к [созданию кластера](service-fabric-cluster-creation-for-windows-server.md#createcluster), чтобы настроить узлы и создать автономный кластер. Используйте файл **ClusterConfig.X509.MultiMachine.json** при создании кластера. Например, команда может выглядеть следующим образом:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

После защиты автономного кластера под управлением Windows и настройки прошедших проверку подлинности клиентов, которые могут к нему подключаться, [подключитесь к защищенному кластеру с помощью PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster). Например:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Затем можно выполнить другие команды PowerShell для работы с этим кластером. Например, [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode.md?view=azureservicefabricps) для отображения списка узлов в этом защищенном кластере.


Чтобы удалить кластер, подключитесь к узлу в кластере, на который вы скачали пакет Service Fabric, откройте командную строку и перейдите в папку с пакетом. Теперь выполните следующую команду.

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Из-за неправильной конфигурации сертификата кластер может не восстановиться во время развертывания. Чтобы самостоятельно определить проблемы безопасности, откройте средство просмотра событий и выберите группу *Журналы приложений и служб* > *Microsoft-Service Fabric*.
> 
> 

