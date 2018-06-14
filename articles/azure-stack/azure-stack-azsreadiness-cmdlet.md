---
title: Справочник по командлету Start-AzsReadinessChecker | Документация Майкрософт
description: Справка по командлету PowerShell для модуля средства проверки готовности Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 8481fbd6c7cb82b34070f9bc8cc6d7f3f4b2518c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937580"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Справочник по командлету Start-AzsReadinessChecker

Модуль: Microsoft.AzureStack.ReadinessChecker

Этот модуль содержит только один командлет.  Этот командлет выполняет одну или несколько функций перед развертыванием или обслуживанием Azure Stack.

## <a name="syntax"></a>Синтаксис
```PowerShell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```


```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```





 ## <a name="description"></a>ОПИСАНИЕ
Командлет **Start-AzsReadinessChecker** проверяет сертификаты, учетные записи Azure, подписки Azure и Azure Active Directory. Выполните проверку перед развертыванием Azure Stack или перед выполнением таких действий по обслуживанию Azure Stack, как смена секрета. Командлет также может использоваться для создания запросов на подпись для сертификатов инфраструктуры и, при необходимости, сертификатов PaaS.  Наконец, командлет может распаковать сертификаты PFX для устранения общих проблем с упаковкой.

## <a name="examples"></a>Примеры
**Пример. Создание запроса на подпись сертификата**

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subjectName $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

В этом примере Start-AzsReadinessChecker создает несколько запросов на подпись сертификатов (CSR), которые подходят для развертывания Azure Stack ADFS с именем региона "east" и внешним полным доменным именем "azurestack.contoso.com".

**Пример. Проверка сертификатов**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

В этом примере пароль PFX запрашивается безопасно, а Start-AzsReadinessChecker проверяет относительную папку "Certificates" на наличие допустимых сертификатов для развертывания AAD с именем региона "east" и внешним полным доменным именем "azurestack.contoso.com". 

**Пример. Проверка сертификатов с помощью данных развертывания (развертывание и поддержка)**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```
В этом примере развертывания и поддержки пароль PFX запрашивается безопасно, а Start-AzsReadinessChecker проверяет относительную папку "Certificates" на наличие допустимых сертификатов для развертывания, при этом идентификационные данные, регион и внешнее полное доменное имя считываются из JSON-файла с данными развертывания, созданного для развертывания. 

**Пример. Проверка сертификатов PaaS**
```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

В этом примере хэш-таблица создается с путями и паролями для каждого сертификата PaaS. Сертификаты могут быть пропущены. Start-AzsReadinessChecker проверяет существование каждого PFX-пути для региона "east" и внешнего полного доменного имени "azurestack.contoso.com".

**Пример. Проверка сертификатов PaaS с помощью данных развертывания**
```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

В этом примере хэш-таблица создается с путями и паролями для каждого сертификата PaaS. Сертификаты могут быть пропущены. Start-AzsReadinessChecker проверяет существование каждого PFX-пути для региона и внешнего полного доменного имени, считываемых из JSON-файла с данными развертывания, созданного для развертывания. 

**Пример. Проверка удостоверения Azure**
```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AzureDirectoryTenantName azurestack.contoso.com
```

В этом примере учетные данные учетной записи администратора сервера безопасно запрашиваются, а Start-AzsReadinessChecker проверяет, допустимы ли учетная запись Azure и Azure Active Directory для развертывания AAD с именем каталога клиента "azurestack.contoso.com".


**Пример. Проверка идентификаторов Azure с помощью данных развертывания (развертывание и поддержка)**
```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

В этом примере учетные данные учетной записи администратора сервера безопасно запрашиваются, а Start-AzsReadinessChecker проверяет, допустимы ли учетная запись Azure и Azure Active Directory для развертывания AAD, при этом AzureCloud и TenantName считываются из JSON-файла с данными развертывания, созданного для развертывания.


**Пример. Проверка регистрации в Azure**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment AzureCloud
```

В этом примере учетные данные владельца подписки безопасно запрашиваются, а затем Start-AzsReadinessChecker выполняет проверку данной учетной записи и подписки, чтобы убедиться, что их можно использовать для регистрации в Azure Stack. 


**Пример. Проверка регистрации Azure с помощью данных развертывания (команда развертывания)**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

В этом примере учетные данные владельца подписки безопасно запрашиваются, а затем Start-AzsReadinessChecker выполняет проверку данной учетной записи и подписки, чтобы убедиться, что ее можно использовать для регистрации в Azure Stack, при этом дополнительные сведения считываются из JSON-файла с данными развертывания, созданного для развертывания.

**Пример. Импорт и экспорт пакета PFX**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

В этом примере PFX-пароль запрашивается безопасно. Файл ssl.pfx импортируется в хранилище сертификатов на локальном компьютере, а затем повторно экспортируется с тем же паролем и сохраняется как ssl_new.pfx.  Эта процедура используется, если в результате проверки сертификата обнаруживается, что у закрытого ключа нет установленного атрибута Local Machine, цепочка сертификата повреждена, в PFX присутствуют несоответствующие сертификаты или цепочка сертификатов находится в неправильном порядке.


**Пример. Просмотр отчета о проверке (поддержка развертывания)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

В этом примере команда разработки или поддержки получает отчет о готовности от клиента (Contoso) и использует Start-AzsReadinessChecker для просмотра состояния выполнения проверки для Contoso.

**Пример. Просмотр сводки отчета о проверке только для подтверждения сертификата (развертывание и поддержка)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

В этом примере команда разработки или поддержки получает отчет о готовности от клиента (Contoso) и использует Start-AzsReadinessChecker для просмотра сводки по состоянию проверки сертификата для Contoso.



## <a name="required-parameters"></a>Необходимые параметры
> -RegionName

Указывает имя региона развертывания Azure Stack.
|  |  |
|----------------------------|--------------|
|Тип:                       |Строка        |
|Позиция:                   |именованная         |
|Значение по умолчанию:              |None          |
|Принимает входные данные конвейера:      |Ложь         |
|Принимает подстановочные знаки: |Ложь         |

> -FQDN    

Указывает внешнее полное доменное имя развертывания Azure Stack (FQDN), которое также имеет псевдоним ExternalFQDN и ExternalDomainName.
|  |  |
|----------------------------|--------------|
|Тип:                       |Строка        |
|Позиция:                   |именованная         |
|Значение по умолчанию:              |ExternalFQDN, ExternalDomainName |
|Принимает входные данные конвейера:      |Ложь         |
|Принимает подстановочные знаки: |Ложь         |

 

> -IdentitySystem    

Указывает допустимые значения идентификационной системы развертывания Azure Stack, AAD или ADFS, для Azure Active Directory и федеративных служб Active Directory соответственно.
|  |  |
|----------------------------|--------------|
|Тип:                       |Строка        |
|Позиция:                   |именованная         |
|Значение по умолчанию:              |None          |
|Допустимые значения:                |'AAD','ADFS'  |
|Принимает входные данные конвейера:      |Ложь         |
|Принимает подстановочные знаки: |Ложь         |

> -PfxPassword    

Указывает пароль, связанный с PFX-файлами сертификатов.
|  |  |
|----------------------------|---------|
|Тип:                       |SecureString |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |

> -PaaSCertificates

Указывает хэш-таблицу, содержащую пути и пароли для сертификатов PaaS.
|  |  |
|----------------------------|---------|
|Тип:                       |Хэш-таблицы |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |

> -DeploymentDataJSONPath

Указывает JSON-файл конфигурации с данными развертывания Azure Stack. Этот файл создается для развертывания.
|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |

> -PfxPath

Указывает путь к проблемному сертификату, которому для исправления требуется процедура импорта и экспорта, как указано в ходе проверки сертификатов в этом инструменте.
|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |

> -ExportPFXPath  

Указывает конечный путь для PFX-файла, полученного в результате процедуры импорта и экспорта.  
|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |

> -Subject

Указывает упорядоченный словарь субъекта для создания запроса на сертификат.
|  |  |
|----------------------------|---------|
|Тип:                       |OrderedDictionary   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |

> -RequestType

Указывает тип сети хранения данных для запроса на сертификат. Допустимые значения: MultipleCSR, SingleCSR.
- *MultipleCSR*. Создает несколько запросов на сертификаты, по одному для каждой службы.
- *SingleCSR*. Создает один запрос на сертификат для всех служб.   

|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Допустимые значения:                |'MultipleCSR','SingleCSR' |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |

> -OutputRequestPath

Указывает путь назначения для файлов запроса на сертификат. Каталог уже должен существовать.
|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |

> -AADServiceAdministrator

Указывает администратора службы Azure Active Directory, используемого в развертывании Azure Stack.
|  |  |
|----------------------------|---------|
|Тип:                       |PSCredential   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |

> -AADDirectoryTenantName

Указывает имя Azure Active Directory, используемое в развертывании Azure Stack.
|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |

> -AzureEnvironment

Указывает экземпляр служб Azure с учетными записями, каталогами и подписками для развертывания и регистрации Azure Stack.
|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Допустимые значения:                |'AzureCloud','AzureChinaCloud','AzureGermanCloud' |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |

> -RegistrationAccount

Указывает учетную запись для регистрации в Azure Stack.
|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |

> -RegistrationSubscriptionID

Указывает идентификатор подписки для регистрации в Azure Stack.
|  |  |
|----------------------------|---------|
|Тип:                       |Guid     |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |

> -ReportPath

Указывает путь для отчета о готовности, по умолчанию используется текущий каталог и стандартное имя отчета.
|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |Все      |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |



## <a name="optional-parameters"></a>Необязательные параметры
> -CertificatePath     

Указывает путь, по которому присутствуют только необходимые папки с сертификатами.

Необходимые папки для развертывания Azure Stack с системой идентификации "Azure Active Directory":

ACSBlob, ACSQueue, ACSTable, Admin Portal, ARM Admin, ARM Public, KeyVault, KeyVaultInternal, Public Portal

Необходимые папки для развертывания Azure Stack с системой идентификации "Службы федерации Active Directory (AD FS)":

ACSBlob, ACSQueue, ACSTable, ADFS, Admin Portal, ARM Admin, ARM Public, Graph, KeyVault, KeyVaultInternal, Public Portal


|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |.\Certificates |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |


> -IncludePaaS  

Указывает, нужно ли добавлять к запросам на сертификат службы PaaS или имена узлов.


|  |  |
|----------------------------|------------------|
|Тип:                       |SwitchParameter   |
|Позиция:                   |именованная             |
|Значение по умолчанию:              |Ложь             |
|Принимает входные данные конвейера:      |Ложь             |
|Принимает подстановочные знаки: |Ложь             |


> -ReportSections        

Показывает только сводку отчета, опуская подробности.
|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |Все      |
|Допустимые значения:                |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|Принимает входные данные конвейера:      |Ложь    |
|Принимает подстановочные знаки: |Ложь    |


> -Summary 

Показывает только сводку отчета, опуская подробности.
|  |  |
|----------------------------|------------------|
|Тип:                       |SwitchParameter   |
|Позиция:                   |именованная             |
|Значение по умолчанию:              |Ложь             |
|Принимает входные данные конвейера:      |Ложь             |
|Принимает подстановочные знаки: |Ложь             |


> -CleanReport  

Удаляет журналы предыдущего выполнения и проверки и записывает проверки в новый отчет.
|  |  |
|----------------------------|------------------|
|Тип:                       |SwitchParameter   |
|Псевдонимы:                    |CF                |
|Позиция:                   |именованная             |
|Значение по умолчанию:              |Ложь             |
|Принимает входные данные конвейера:      |Ложь             |
|Принимает подстановочные знаки: |Ложь             |


> -OutputPath    

Задает пользовательский путь для сохранения отчета о готовности в формате JSON и подробного файла журнала.  Если путь еще не существует, инструмент попытается создать каталог.
|  |  |
|----------------------------|------------------|
|Тип:                       |Строка            |
|Позиция:                   |именованная             |
|Значение по умолчанию:              |$ENV:TEMP\AzsReadinessChecker  |
|Принимает входные данные конвейера:      |Ложь             |
|Принимает подстановочные знаки: |Ложь             |


> -Confirm  

Запрашивает подтверждение перед выполнением командлета.
|  |  |
|----------------------------|------------------|
|Тип:                       |SwitchParameter   |
|Псевдонимы:                    |CF                |
|Позиция:                   |именованная             |
|Значение по умолчанию:              |Ложь             |
|Принимает входные данные конвейера:      |Ложь             |
|Принимает подстановочные знаки: |Ложь             |


> -WhatIf  

Показывает, что произойдет при запуске командлета. Командлет не выполняется.
|  |  |
|----------------------------|------------------|
|Тип:                       |SwitchParameter   |
|Псевдонимы:                    |wi                |
|Позиция:                   |именованная             |
|Значение по умолчанию:              |Ложь             |
|Принимает входные данные конвейера:      |Ложь             |
|Принимает подстановочные знаки: |Ложь             |

 
