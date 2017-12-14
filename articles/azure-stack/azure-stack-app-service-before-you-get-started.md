---
title: "Подготовка к развертыванию службы приложений в Azure Stack | Документация Майкрософт"
description: "Действия, которые необходимо выполнить перед развертыванием службы приложений в Azure Stack"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: d6962bf2ffbf731a4aa301e663c7c7d3428080d4
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Подготовка к работе со службой приложений в Azure Stack

Служба приложений Azure в Azure Stack имеет ряд предварительных шагов, которые необходимо выполнить перед развертыванием:

- скачать службу приложений Azure во вспомогательных скриптах Azure Stack;
- высокую доступность;
- Получение сертификатов, необходимых для службы приложений Azure в Azure Stack
- подготовить файловый сервер;
- подготовить SQL Server;
- Создание приложения Azure Active Directory
- Создание приложения служб федерации Active Directory (AD FS)

## <a name="download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts"></a>Скачивание установщика и вспомогательных скриптов для развертывания службы приложений Azure в Azure Stack

1. Скачайте [службу приложений Azure для вспомогательных скриптов Azure Stack](https://aka.ms/appsvconmashelpers).
2. Скачайте [установщик для развертывания службы приложений Azure в Azure Stack](https://aka.ms/appsvconmasinstaller).
3. Извлеките файлы из ZIP-файла вспомогательных скриптов. Вы увидите следующие файлы и структуру папок:
  - Common.ps1;
  - Create-AADIdentityApp.ps1
  - Create-ADFSIdentityApp.ps1
  - Create-AppServiceCerts.ps1
  - Get-AzureStackRootCert.ps1;
  - Remove-AppService.ps1;
  - модули
    - GraphAPI.psm1
    
## <a name="high-availability"></a>высокую доступность;

Сейчас служба приложений Azure в Azure Stack не может обеспечить высокий уровень доступности, так как Azure Stack развертывает рабочие нагрузки в один домен сбоя.

Чтобы обеспечить для службы приложений Azure в Azure Stack высокий уровень доступности, разверните необходимый файловый сервер и SQL Server в соответствующей конфигурации. Когда Azure Stack будет поддерживать несколько доменов сбоя, мы предоставим рекомендации по включению службы приложений Azure в Azure Stack в конфигурации, обеспечивающей высокий уровень доступности.


## <a name="certificates-required-for-azure-app-service-on-azure-stack"></a>Получение сертификатов, необходимых для службы приложений Azure в Azure Stack

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Сертификаты, необходимые для комплекта разработки Azure Stack

Первый скрипт работает с центром сертификации Azure Stack для создания четырех сертификатов, необходимых службе приложений.

| Имя файла | Использование |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | SSL-сертификат по умолчанию службы приложений |
| api.appservice.local.azurestack.external.pfx | SSL-сертификат API службы приложений |
| ftp.appservice.local.azurestack.external.pfx | SSL-сертификат издателя службы приложений |
| Sso.appservice.local.azurestack.external.pfx | Сертификат приложения идентификации службы приложений |

Запустите скрипт в расположении пакета SDK Azure Stack и убедитесь, что вы работаете в PowerShell с правами azurestack\CloudAdmin.

1. В сеансе PowerShell, который выполняется с правами azurestack\AzureStackAdmin, запустите скрипт Create-AppServiceCerts.ps1 из папки, в которую были извлечены вспомогательные скрипты. Скрипт создает четыре сертификата в той же папке, что и скрипт создания сертификатов, который требуется службе приложений.
2. Введите пароль для защиты PFX-файлов и запишите его. Его необходимо ввести в установщике службы приложений для Azure Stack.

#### <a name="create-appservicecertsps1-parameters"></a>Параметры Create-AppServiceCerts.ps1

| Параметр | Обязательный/необязательный | Значение по умолчанию | Описание |
| --- | --- | --- | --- |
| pfxPassword | Обязательно | Null | Пароль, используемый для защиты закрытого ключа сертификата |
| DomainName | Обязательно | local.azurestack.external | Суффикс региона и домена для Azure Stack |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Сертификаты, необходимые для развертывания в рабочей среде службы приложений Azure в Azure Stack

Для работы поставщика ресурсов в рабочей среде необходимо предоставить четыре указанных ниже сертификата.

#### <a name="default-domain-certificate"></a>Сертификат домена по умолчанию

Сертификат домена по умолчанию размещается в роли внешнего интерфейса. Он используется пользовательскими приложениями для групповых запросов или запросов домена по умолчанию к службе приложений Azure. Сертификат также применяется для операций системы управления версиями (KUDU).

Сертификат должен быть в формате PFX и должен быть групповым сертификатом двух субъектов. Это позволяет использовать один сертификат для домена по умолчанию и конечной точки SCM для выполнения операций управления версиями.

| Формат | Пример |
| --- | --- |
| \*.appservice.\<регион\>.\<имя домена\>.\<расширение\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.appservice.scm.redmond.azurestack.external |

#### <a name="api-certificate"></a>Сертификат API

Сертификат API размещается в роли управления и используется поставщиком ресурсов для защиты вызовов API. Сертификат для публикации должен содержать субъект, соответствующей записи API DNS:

| Формат | Пример |
| --- | --- |
| api.appservice.\<регион\>.\<доменное_имя\>.\<расширение\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Сертификат для публикации

Сертификат для роли издателя защищает трафик FTPS для владельцев приложений при отправке содержимого.  Сертификат для публикации должен содержать субъект, соответствующий записи FTPS DNS.

| Формат | Пример |
| --- | --- |
| ftp.appservice.\<регион\>.\<имя домена\>.\<расширение\> | api.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Сертификат удостоверения

Сертификат для приложения идентификации активирует:
- интеграцию между каталогами AAD и ADFS, Azure Stack и службой приложений для интеграции с поставщиком вычислительных ресурсов;
- сценарии единого входа для расширенных средств разработки в службе приложений Azure в Azure Stack.
Сертификат для идентификации должен содержать субъект, соответствующий следующему формату:

| Формат | Пример |
| --- | --- |
| sso.appservice.\<регион\>.\<имя домена\>.\<расширение\> | sso.appservice.redmond.azurestack.external |

### <a name="extract-the-azure-stack-azure-resource-manager-root-certificate"></a>Извлечение корневого сертификата Azure Resource Manager Azure Stack

В сеансе PowerShell, который выполняется с правами azurestack\CloudAdmin, запустите скрипт Get-AzureStackRootCert.ps1 из папки, в которую были извлечены вспомогательные скрипты. Скрипт создает четыре сертификата в той же папке, что и скрипт создания сертификатов, который требуется службе приложений.

| Параметр Get-AzureStackRootCert.ps1 | Обязательный/необязательный | Значение по умолчанию | Описание |
| --- | --- | --- | --- |
| PrivelegedEndpoint | Обязательно | AzS-ERCS01 | Привилегированная конечная точка. |
| CloudAdminCredential | Обязательно | AzureStack\CloudAdmin | Учетные данные домена администратора облака Azure Stack. |


## <a name="prepare-the-file-server"></a>Подготовка файлового сервера

Служба приложений Azure требует использования файлового сервера. Для развертываний в рабочей среде файловый сервер должен быть настроен так, чтобы обеспечивать высокую доступность и обрабатывать сбои.

Этот пример шаблона развертывания Azure Resource Manager для развертывания настроенного файлового сервера на одном узле можно использовать только при развертываниях пакета SDK Azure Stack: https://aka.ms/appsvconmasdkfstemplate. В рабочей группе будет файловый сервер с одним узлом.

### <a name="provision-groups-and-accounts-in-active-directory"></a>Подготовка групп и учетных записей в Active Directory


1. Создайте следующие группы глобальной безопасности Active Directory:
    - FileShareOwners
    - FileShareUsers
2. Создайте следующие учетные записи Active Directory в качестве учетных записей службы:
    - FileShareOwner
    - FileShareUser

    С точки зрения безопасности пользователи этих учетных записей (и всех веб-ролей) должны отличаться друг от друга и иметь надежные имена пользователей и пароли.
    Задайте пароли со следующими условиями:
     - Включите параметр **Срок действия пароля не ограничен**.
     - Включите параметр **Запретить смену пароля пользователем**.
     - Отключите параметр **Требовать смены пароля при следующем входе в систему**.
3. Добавьте учетные записи в группы следующим образом:
    - Добавьте **FileShareOwner** в группу **FileShareOwners**.
    - Добавьте **FileShareUser** в группу **FileShareUsers**.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Подготовка групп и учетных записей в рабочей группе

>[!NOTE]
> При настройке файлового сервера выполняйте все приведенные ниже команды в сеансе командной строки администратора.  **НЕ используйте PowerShell.**

Если используется указанный выше шаблон Azure Resource Manager, пользователи уже созданы.

1. Выполните следующие команды, чтобы создать учетные записи FileShareOwner и FileShareUser. Замените <password> собственными значениями.
``` DOS
net user FileShareOwner <password> /add /expires:never /passwordchg:no
net user FileShareUser <password> /add /expires:never /passwordchg:no
```
2. Задайте неограниченный срок действия паролей для учетных записей, выполнив следующие команды WMIC:
``` DOS
WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
```
3. Создайте локальные группы FileShareUsers и FileShareOwners и добавьте в них учетные записи с первого шага.
``` DOS
net localgroup FileShareUsers /add
net localgroup FileShareUsers FileShareUser /add
net localgroup FileShareOwners /add
net localgroup FileShareOwners FileShareOwner /add
```

### <a name="provision-the-content-share"></a>Подготовка общей папки содержимого

В общей папке содержимого размещается содержимое веб-сайта клиента. Процедура подготовки общей папки содержимого на одном файловом сервере одинакова для сред Active Directory и рабочей группы, но отличается для отказоустойчивого кластера в Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-ad-or-workgroup"></a>Подготовка общей папки содержимого на одном файловом сервере (AD или рабочей группы)

На одном файловом сервере выполните следующие команды в командной строке с повышенными привилегиями. Замените значение C:\WebSites соответствующими путями в своей среде.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="to-enable-winrm-add-the-fileshareowners-group-to-the-local-administrators-group"></a>Добавление группы FileShareOwners в группу локальных администраторов, чтобы включить WinRM

Для правильной работы службы удаленного управления Windows необходимо добавить группу FileShareOwners в группу локальных администраторов.

#### <a name="active-directory"></a>Active Directory

Выполните следующие команды в командной строке с повышенными привилегиями на файловом сервере или на каждом узле отказоустойчивого кластера файлового сервера. Замените значение <DOMAIN> доменным именем, которое необходимо использовать.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Рабочая группа

Выполните следующую команду в командной строке с повышенными привилегиями на файловом сервере.

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>Настройка управления доступом к общим папкам

Выполните следующие команды в командной строке с повышенными привилегиями на файловом сервере или на узле отказоустойчивого кластера файлового сервера, который является текущим владельцем кластерного ресурса. Замените значения, выделенные курсивом, значениями для конкретной среды.

#### <a name="active-directory"></a>Active Directory
```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Рабочая группа
```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server"></a>Подготовка SQL Server

Для службы приложений Azure в базах данных размещения и экспозамеров Azure Stack необходимо подготовить SQL Server, чтобы разместить базы данных службы приложений Azure.

Для комплекта разработки Azure Stack можно использовать SQL Express 2014 с пакетом обновления 2 (SP2) или более поздней версии.

Для рабочих сред и с целью обеспечения высокого уровня доступности следует использовать полную версию SQL 2014 с пакетом обновления 2 (SP2) или более поздней версии, включить аутентификацию в смешанном режиме и выполнить развертывание в [конфигурации, обеспечивающей высокий уровень доступности](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Служба приложений Azure на SQL Server Azure Stack должна быть доступна из всех ролей службы приложений. SQL Server можно развернуть в стандартной подписке поставщика в Azure Stack. Либо можно использовать существующую инфраструктуру в организации (при наличии подключения к Azure Stack). Если вы используете образ Azure Marketplace, не забудьте соответствующим образом настроить брандмауэр. 

Для любой роли SQL Server можно использовать экземпляр по умолчанию или именованный экземпляр. Однако, если используется именованный экземпляр, вручную запустите службу обозревателя SQL и откройте порт 1434.

## <a name="create-an-azure-active-directory-application"></a>Создание приложения Azure Active Directory

Настройте субъект-службу Azure AD для поддержки следующего:
- интеграция масштабируемых наборов виртуальных машин на уровнях рабочих ролей;
- единый вход для портала Функций Azure и расширенные средства разработчика.

Эти шаги применимы только к средам Azure Stack, защищенным с помощью Azure AD.

Администраторы должны выполнить настройку единого входа, чтобы:
- включить расширенные средства разработчиков в рамках службы приложений (Kudu);
- включить использование портала Функций Azure.

Выполните следующие действия.

1. Откройте экземпляр PowerShell с правами azurestack\AzureStackAdmin.
2. Перейдите к расположению скриптов, скачанных и извлеченных на [этапе подготовки](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Установите PowerShell для Azure Stack](azure-stack-powershell-install.md).
4. Запустите скрипт **Create-AADIdentityApp.ps1**. Когда появится запрос на ввод идентификатора клиента Azure AD, введите идентификатор клиента Azure AD, используемый для развертывания Azure Stack, например myazurestack.onmicrosoft.com.
5. В окне **Учетные данные** введите учетную запись администратора службы Azure AD и пароль. Нажмите кнопку **ОК**.
6. Введите путь к файлу сертификата и пароль для [сертификата, созданного ранее](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Для этого шага по умолчанию создается сертификат **sso.appservice.local.azurestack.external.pfx**.
7. Скрипт создаст приложение в клиенте Azure AD. Запишите идентификатор приложения, который возвращается в выходных данных PowerShell. Он вам понадобится при установке.
8. Откройте новое окно в браузере и войдите на портал Azure (portal.azure.com) в качестве **администратора службы Azure Active Directory**.
9. Откройте поставщик ресурсов Azure AD.
10. Щелкните **Регистрация приложений**.
11. Выполните поиск **идентификатора приложения**, возвращенного на шаге 7. Вы увидите приложение службы приложений.
12. Щелкните **Приложение** в списке.
13. Щелкните **Необходимые разрешения** > **Предоставление разрешений** > **Да**.

| Параметр Create-AADIdentityApp.ps1 | Обязательный/необязательный | Значение по умолчанию | Описание |
| --- | --- | --- | --- |
| DirectoryTenantName | Обязательно | Null | Идентификатор клиента Azure AD. Укажите идентификатор GUID или строку, например myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Обязательно | Null | Конечная точка Azure Resource Manager для администратора, например adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Обязательно | Null | Конечная точка Azure Resource Manager для клиента, например management.local.azurestack.external. |
| AzureStackAdminCredential | Обязательно | Null | Учетные данные администратора службы Azure AD |
| CertificateFilePath | Обязательно | Null | Путь к файлу сертификата приложения идентификации, созданному ранее. |
| CertificatePassword | Обязательно | Null | Пароль, используемый для защиты закрытого ключа сертификата. |

## <a name="create-an-active-directory-federation-services-application"></a>Создание приложения служб федерации Active Directory (AD FS)

В средах Azure Stack, защищенных с помощью AD FS, необходимо настроить субъект-службу AD FS для поддержки следующего:
- интеграция масштабируемых наборов виртуальных машин на уровнях рабочих ролей;
- единый вход для портала Функций Azure и расширенные средства разработчика.

Администраторам нужно выполнить настройку единого входа, чтобы:
- настроить субъект-службу для интеграции масштабируемого набора виртуальных машин на уровнях рабочих ролей;
- включить расширенные средства разработчиков в рамках службы приложений (Kudu);
- включить использование портала Функций Azure.

Выполните следующие действия.

1. Откройте экземпляр PowerShell с правами azurestack\azurestackadmin.
2. Перейдите к расположению скриптов, скачанных и извлеченных на [этапе подготовки](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Установите PowerShell для Azure Stack](azure-stack-powershell-install.md).
4.  Запустите скрипт **Create-ADFSIdentityApp.ps1**.
5.  В окне **Учетные данные** укажите учетную запись администратора облака AD FS и пароль. Нажмите кнопку **ОК**.
6.  Предоставьте путь к файлу сертификата и пароль для [сертификата, созданного ранее](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Для этого шага по умолчанию создается сертификат sso.appservice.local.azurestack.external.pfx.

| Параметр Create-ADFSIdentityApp.ps1 | Обязательный/необязательный | Значение по умолчанию | Описание |
| --- | --- | --- | --- |
| AdminArmEndpoint | Обязательно | Null | Конечная точка Azure Resource Manager администратора. Например, adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Обязательно | Null | Привилегированная конечная точка. Например, AzS-ERCS01. |
| CloudAdminCredential | Обязательно | Null | Учетные данные домена администратора облака Azure Stack. Например, Azurestack\CloudAdmin. |
| CertificateFilePath | Обязательно | Null | Путь к PFX-файлу сертификата приложения идентификации. |
| CertificatePassword | Обязательно | Null | Пароль, используемый для защиты закрытого ключа сертификата. |


## <a name="next-steps"></a>Дальнейшие действия

[Добавление поставщика ресурсов службы приложений в Azure Stack](azure-stack-app-service-deploy.md)
