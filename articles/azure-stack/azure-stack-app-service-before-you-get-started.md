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
ms.date: 10/10/2017
ms.author: anwestg
ms.openlocfilehash: 430101c398eff85b330d15242ed1e396a277a93a
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Подготовка к работе со службой приложений в Azure Stack

Служба приложений Azure в Azure Stack имеет ряд предварительных шагов, которые необходимо выполнить перед развертыванием:

- скачать службу приложений Azure во вспомогательных скриптах Azure Stack;
- получить сертификаты, необходимые для службы приложений Azure в Azure Stack;
- подготовить файловый сервер;
- подготовить SQL Server;
- Создание приложения Azure Active Directory
- создать приложение служб федерации Active Directory (AD FS).

## <a name="download-the-azure-app-service-on-azure-stack-helper-scripts"></a>Скачивание службы приложений Azure во вспомогательных скриптах Azure Stack

1. Скачайте [службу приложений Azure для вспомогательных скриптов Azure Stack](http://aka.ms/appsvconmasrc1helper).
2. Извлеките файлы из ZIP-файла вспомогательных скриптов. Вы увидите следующие файлы и структуру папок:
  - Create-AppServiceCerts.ps1
  - Create-AADIdentityApp.ps1
  - Create-ADFSIdentityApp.ps1
  - модули
    - AzureStack.Identity.psm1
    - GraphAPI.psm1
    
## <a name="certificates-required-for-azure-app-service-on-azure-stack"></a>Получение сертификатов, необходимых для службы приложений Azure в Azure Stack

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Сертификаты, необходимые для комплекта разработки Azure Stack

Первый скрипт работает с центром сертификации Azure Stack для создания четырех сертификатов, необходимых службе приложений.

| Имя файла | Использование |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | SSL-сертификат по умолчанию службы приложений |
| api.appservice.local.azurestack.external.pfx | SSL-сертификат API службы приложений |
| ftp.appservice.local.azurestack.external.pfx | SSL-сертификат издателя службы приложений |
| Sso.appservice.local.azurestack.external.pfx | Сертификат приложения идентификации службы приложений |

Запустите скрипт в расположении комплекта разработки Azure Stack и убедитесь, что PowerShell выполняется с правами azurestack\AzureStackAdmin.

1. В сеансе PowerShell, который выполняется с правами azurestack\AzureStackAdmin, запустите скрипт Create-AppServiceCerts.ps1 из папки, в которую были извлечены вспомогательные скрипты. Скрипт создает четыре сертификата в той же папке, что и скрипт создания сертификатов, который требуется службе приложений.
2. Введите пароль для защиты PFX-файлов и запишите его. Его будет необходимо ввести в службе приложений в программе установки Azure Stack.

#### <a name="create-appservicecertsps1-parameters"></a>Параметры Create-AppServiceCerts.ps1

| Параметр | Обязательный/необязательный | Значение по умолчанию | Описание |
| --- | --- | --- | --- |
| pfxPassword | Обязательно | Null | Пароль, используемый для защиты закрытого ключа сертификата |
| DomainName | Обязательно | local.azurestack.external | Суффикс региона и домена для Azure Stack |
| CertificateAuthority | Обязательно | AzS-CA01.azurestack.local | Конечная точка центра сертификации |

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
| api.appservice.\<регион\>.\<имя домена\>.\<расширение\> | api.appservice.redmond.azurestack.external |

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

#### <a name="extract-the-azure-stack-azure-resource-manager-root-certificate"></a>Извлечение корневого сертификата Azure Resource Manager Azure Stack

В сеансе PowerShell, который выполняется с правами azurestack\AzureStackAdmin, запустите скрипт Get-AzureStackRootCert.ps1 из папки, в которую были извлечены вспомогательные скрипты. Скрипт создает четыре сертификата в той же папке, что и скрипт создания сертификатов, который требуется службе приложений.

| Параметр Get-AzureStackRootCert.ps1 | Обязательный/необязательный | Значение по умолчанию | Описание |
| --- | --- | --- | --- |
| EmergencyConsole | Обязательно | AzS-ERCS01 | Привилегированная конечная точка аварийной консоли |
| CloudAdminCredential | Обязательно | AzureStack\AzureStackAdmin | Учетные данные домена администратора облака Azure Stack |


## <a name="prepare-the-file-server"></a>Подготовка файлового сервера

Служба приложений Azure требует использования файлового сервера. Для развертываний в рабочей среде он должен быть настроен так, чтобы обеспечивать высокую доступность и обрабатывать сбои.

Этот пример шаблона развертывания ARM для развертывания файлового сервера на одном узле можно использовать только при развертываниях комплекта разработки Azure Stack: https://aka.ms/appsvconmasdkfstemplate.

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

В рабочей группе выполните команды net и WMIC, чтобы подготовить группы и учетные записи.

1. Выполните следующие команды, чтобы создать учетные записи FileShareOwner и FileShareUser. Замените <password> собственными значениями.
    - net user FileShareOwner <password> /add /expires:never /passwordchg:no
    - net user FileShareUser <password> /add /expires:never /passwordchg:no
2. Задайте неограниченный срок действия паролей для учетных записей, выполнив следующие команды WMIC:
    - WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
    - WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
3. Создайте локальные группы FileShareUsers и FileShareOwners и добавьте в них учетные записи с первого шага.
    - net localgroup FileShareUsers /add
    - net localgroup FileShareUsers FileShareUser /add
    - net localgroup FileShareOwners /add
    - net localgroup FileShareOwners FileShareOwner /add

### <a name="provision-the-content-share"></a>Подготовка общей папки содержимого

В общей папке содержимого размещается содержимое веб-сайта клиента. Процедура подготовки общей папки содержимого на одном файловом сервере одинакова для сред Active Directory и рабочей группы, но отличается для отказоустойчивого кластера в Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-ad-or-workgroup"></a>Подготовка общей папки содержимого на одном файловом сервере (AD или рабочей группы)

На одном файловом сервере выполните следующие команды в командной строке с повышенными привилегиями. Замените значение < C:\WebSites > соответствующими путями в своей среде.

```powershell
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=<C:\WebSites>
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="to-enable-winrm-add-the-fileshareowners-group-to-the-local-administrators-group"></a>Добавление группы FileShareOwners в группу локальных администраторов, чтобы включить WinRM

Для правильной работы службы удаленного управления Windows необходимо добавить группу FileShareOwners в группу локальных администраторов.

#### <a name="active-directory"></a>Active Directory

Выполните следующие команды в командной строке с повышенными привилегиями на файловом сервере или на каждом узле отказоустойчивого кластера файлового сервера. Замените значение <DOMAIN> доменным именем, которое необходимо использовать.

```powershell
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Рабочая группа

Выполните следующую команду в командной строке с повышенными привилегиями на файловом сервере.

net localgroup Administrators FileShareOwners /add

### <a name="configure-access-control-to-the-shares"></a>Настройка управления доступом к общим папкам

Выполните следующие команды в командной строке с повышенными привилегиями на файловом сервере или на узле отказоустойчивого кластера файлового сервера, который является текущим владельцем кластерного ресурса. Замените значения, выделенные курсивом, значениями для конкретной среды.

#### <a name="active-directory"></a>Active Directory
```powershell
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=<C:\WebSites>
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Рабочая группа
```powershell
set WEBSITES_FOLDER=<C:\WebSites>
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server"></a>Подготовка SQL Server

Для службы приложений Azure в базах данных размещения и экспозамеров Azure Stack необходимо подготовить SQL Server, чтобы разместить базу данных среды выполнения веб-сайтов Windows Azure Pack.

Для комплекта разработки Azure Stack можно использовать SQL Express 2012 с пакетом обновления 1 или более позднюю версию. Сведения о скачивании см. в статье [Download SQL Server 2012 Express with SP1](https://msdn.microsoft.com/evalcenter/hh230763.aspx) (Скачивание SQL Server 2012 Express с пакетом обновления 1).
Для рабочих сред и обеспечения высокого уровня доступности следует использовать полную версию SQL 2012 с пакетом обновления 1 или более позднюю версию. Сведения об установке SQL Server см. в статье [Установка SQL Server](http://go.microsoft.com/fwlink/?LinkId=322141).
Включите проверку подлинности в смешанном режиме.
Служба приложений Azure на SQL Server Azure Stack должна быть доступна из всех ролей службы приложений.
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

1. Откройте экземпляр PowerShell с правами azurestack\azurestackadmin.
2. Перейдите к расположению скриптов, скачанных и извлеченных на [этапе подготовки](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-app-service-deploy#download-required-components).
3. [Установите](azure-stack-powershell-install.md) и [настройте среду Azure Stack PowerShell](azure-stack-powershell-configure-admin.md).
4. В том же сеансе PowerShell запустите скрипт **CreateIdentityApp.ps1**. Когда появится запрос на ввод идентификатора клиента Azure AD, введите идентификатор клиента Azure AD, используемый для развертывания Azure Stack, например myazurestack.onmicrosoft.com.
5. В окне **Учетные данные** введите учетную запись администратора службы Azure AD и пароль. Нажмите кнопку **ОК**.
6. Введите путь к файлу сертификата и пароль для [сертификата, созданного ранее](azure-stack-app-service-deploy.md). Для этого шага по умолчанию создается сертификат sso.appservice.local.azurestack.external.pfx.
7. Этот скрипт создает новое приложение в Azure AD клиента и создает скрипт PowerShell с именем **UpdateConfigOnController.ps1**. Запишите идентификатор приложения, который возвращается в выходных данных PowerShell. Эта информация нужна для выполнения поиска на шаге 11.
8. Откройте новое окно в браузере и войдите на портал Azure (portal.azure.com) в качестве **администратора службы Azure Active Directory**.
9. Откройте поставщик ресурсов Azure AD.
10. Щелкните **Регистрация приложений**.
11. Выполните поиск **идентификатора приложения**, возвращенного на шаге 7. Вы увидите приложение службы приложений.
12. Щелкните **Приложение** в списке.
13. Щелкните **Необходимые разрешения** > **Предоставление разрешений** > **Да**.

| Параметр CreateIdentityApp.ps1 | Обязательный/необязательный | Значение по умолчанию | Описание |
| --- | --- | --- | --- |
| DirectoryTenantName | Обязательно | Null | Идентификатор клиента Azure AD. Укажите идентификатор GUID или строку, например myazureaaddirectory.onmicrosoft.com. |
| TenantAzure Resource ManagerEndpoint | Обязательно | management.local.azurestack.external | Конечная точка Azure Resource Manager клиента. |
| AzureStackCredential | Обязательно | Null | Администратор Azure AD |
| CertificateFilePath | Обязательно | Null | Путь к файлу сертификата приложения идентификации, созданному ранее. |
| CertificatePassword | Обязательно | Null | Пароль, используемый для защиты закрытого ключа сертификата. |
| DomainName | Обязательно | local.azurestack.external | Суффикс региона и домена для Azure Stack. |
| AdfsMachineName | Необязательно | Имя компьютера AD FS, например AzS ADFS01.azurestack.local. | Требуется при развертывании AD FS. Игнорируйте при развертывании Azure AD. |

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
2. Перейдите к расположению скриптов, скачанных и извлеченных на [этапе подготовки](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-app-service-deploy#download-required-components).
3. [Установите](azure-stack-powershell-install.md) и [настройте среду Azure Stack PowerShell](azure-stack-powershell-configure-admin.md).
4.  В том же сеансе PowerShell запустите скрипт **CreateIdentityApp.ps1**. Когда появится запрос на ввод идентификатора клиента Azure AD, введите AD FS.
5.  В окне **Учетные данные** введите учетную запись администратора службы AD FS и пароль. Нажмите кнопку **ОК**.
6.  Предоставьте путь к файлу сертификата и пароль для [сертификата, созданного ранее](azure-stack-app-service-deploy.md). Для этого шага по умолчанию создается сертификат sso.appservice.local.azurestack.external.pfx.

| Параметр CreateIdentityApp.ps1 | Обязательный/необязательный | Значение по умолчанию | Описание |
| --- | --- | --- | --- |
| DirectoryTenantName | Обязательно | Null | Используйте AD FS в среде AD FS. |
| TenantAzure Resource ManagerEndpoint | Обязательно | management.local.azurestack.external | Конечная точка Azure Resource Manager клиента. |
| AzureStackCredential | Обязательно | Null | Администратор Azure AD |
| CertificateFilePath | Обязательно | Null | Путь к файлу сертификата приложения идентификации, созданному ранее. |
| CertificatePassword | Обязательно | Null | Пароль, используемый для защиты закрытого ключа сертификата. |
| DomainName | Обязательно | local.azurestack.external | Суффикс региона и домена для Azure Stack. |
| AdfsMachineName | Необязательно | Имя компьютера AD FS, например AzS ADFS01.azurestack.local. | Требуется при развертывании AD FS. Игнорируйте при развертывании Azure AD. |


## <a name="next-steps"></a>Дальнейшие действия

[Добавление поставщика ресурсов службы приложений в Azure Stack](azure-stack-app-service-deploy.md)
