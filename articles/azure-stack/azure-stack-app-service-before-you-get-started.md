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
ms.date: 01/29/2018
ms.author: anwestg
ms.openlocfilehash: cdeec60b4935fda58e2657a215826d8355c81664
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2018
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Подготовка к работе со службой приложений в Azure Stack
*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Перед развертыванием службы приложений Azure в Azure Stack необходимо выполнить предварительные требования, указанные в этой статье.

## <a name="download-the-installer-and-helper-scripts"></a>Скачивание установочного и вспомогательного скриптов

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

## <a name="prepare-for-high-availability"></a>Подготовка к обеспечению высокой доступности

Сейчас служба приложений Azure в Azure Stack не может обеспечить высокий уровень доступности, так как Azure Stack развертывает рабочие нагрузки только в один домен сбоя.

Чтобы обеспечить для службы приложений Azure в Azure Stack высокий уровень доступности, разверните необходимый файловый сервер и экземпляр SQL Server в соответствующей конфигурации. Когда Azure Stack будет поддерживать несколько доменов сбоя, мы предоставим рекомендации по включению службы приложений Azure в Azure Stack в конфигурации, обеспечивающей высокий уровень доступности.


## <a name="get-certificates"></a>Получение сертификатов

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Сертификаты, необходимые для Пакета средств разработки Azure Stack

Первый скрипт работает с центром сертификации Azure Stack для создания четырех сертификатов, необходимых службе приложений:

| Имя файла | Использование |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | SSL-сертификат по умолчанию службы приложений |
| api.appservice.local.azurestack.external.pfx | SSL-сертификат API службы приложений |
| ftp.appservice.local.azurestack.external.pfx | SSL-сертификат издателя службы приложений |
| Sso.appservice.local.azurestack.external.pfx | Сертификат приложения для удостоверения службы приложений |

Запустите скрипт в расположении Пакета средств разработки Azure Stack и убедитесь, что работаете в PowerShell с правами azurestack\CloudAdmin:

1. В сеансе PowerShell с правами azurestack\AzureStackAdmin выполните скрипт Create-AppServiceCerts.ps1 из папки, в которую были извлечены вспомогательные скрипты. Скрипт создает четыре сертификата в той же папке, где расположен скрипт, который требуется службе приложений для создания сертификатов.
2. Введите пароль для защиты PFX-файлов и запишите его. Его необходимо ввести в установщике службы приложений для Azure Stack.

#### <a name="create-appservicecertsps1-parameters"></a>Параметры Create-AppServiceCerts.ps1

| Параметр | Обязательный или необязательный | Значение по умолчанию | ОПИСАНИЕ |
| --- | --- | --- | --- |
| pfxPassword | Обязательно | Null | Пароль, который помогает защитить закрытый ключ сертификата |
| DomainName | Обязательно | local.azurestack.external | Суффикс региона и домена для Azure Stack |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Сертификаты, необходимые для развертывания в рабочей среде службы приложений Azure в Azure Stack

Для работы поставщика ресурсов в рабочей среде необходимо предоставить четыре указанных ниже сертификата.

#### <a name="default-domain-certificate"></a>Сертификат домена по умолчанию

Сертификат домена по умолчанию размещается в роли внешнего интерфейса. Он используется пользовательскими приложениями для групповых запросов или запросов домена по умолчанию к службе приложений Azure. Сертификат также применяется для операций системы управления версиями (Kudu).

Сертификат должен быть в формате PFX и должен быть групповым сертификатом двух субъектов. Это позволяет использовать один сертификат для домена по умолчанию и конечной точки SCM для операций управления версиями.

| Формат | Пример |
| --- | --- |
| \*.appservice.\<регион\>.\<имя домена\>.\<расширение\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.appservice.scm.redmond.azurestack.external |

#### <a name="api-certificate"></a>Сертификат API

Сертификат API помещается в роль управления. Он используется поставщиком ресурсов для защиты вызовов API. Сертификат для публикации должен содержать субъект, соответствующий записи API DNS.

| Формат | Пример |
| --- | --- |
| api.appservice.\<регион\>.\<доменное_имя\>.\<расширение\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Сертификат для публикации

Сертификат для роли издателя защищает трафик FTPS для владельцев приложений при отправке содержимого. Сертификат для публикации должен содержать субъект, соответствующий записи FTPS DNS.

| Формат | Пример |
| --- | --- |
| ftp.appservice.\<регион\>.\<имя домена\>.\<расширение\> | api.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Сертификат удостоверения

Сертификат для приложения удостоверения активирует:
- интеграцию между каталогами Azure Active Directory (Azure AD) или службы федерации Active Directory (AD FS), Azure Stack и службой приложений для интеграции с поставщиком вычислительных ресурсов;
- сценарии единого входа для расширенных средств разработки в службе приложений Azure в Azure Stack.

Сертификат для идентификации должен содержать субъект, соответствующий следующему формату:

| Формат | Пример |
| --- | --- |
| sso.appservice.\<регион\>.\<имя домена\>.\<расширение\> | sso.appservice.redmond.azurestack.external |

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Корневой сертификат Azure Resource Manager для Azure Stack

В сеансе PowerShel с правами azurestack\CloudAdmin выполните скрипт Get-AzureStackRootCert.ps1 из папки, в которую были извлечены вспомогательные скрипты. Скрипт создает четыре сертификата в той же папке, где расположен скрипт, который требуется службе приложений для создания сертификатов.

| Параметр Get-AzureStackRootCert.ps1 | Обязательный или необязательный | Значение по умолчанию | ОПИСАНИЕ |
| --- | --- | --- | --- |
| PrivelegedEndpoint | Обязательно | AzS-ERCS01 | Привилегированная конечная точка |
| CloudAdminCredential | Обязательно | AzureStack\CloudAdmin | Учетные данные домена администратора облака Azure Stack |


## <a name="prepare-the-file-server"></a>Подготовка файлового сервера

Служба приложений Azure требует использования файлового сервера. Для развертываний в рабочей среде файловый сервер должен быть настроен так, чтобы обеспечивать высокую доступность и обрабатывать сбои.

Этот [пример шаблона развертывания Azure Resource Manager](https://aka.ms/appsvconmasdkfstemplate) для развертывания настроенного файлового сервера на одном узле можно использовать только при развертываниях Пакета средств разработки Azure Stack. В рабочей группе будет файловый сервер с одним узлом.

### <a name="provision-groups-and-accounts-in-active-directory"></a>Подготовка групп и учетных записей в Active Directory


1. Создайте следующие группы глобальной безопасности Active Directory:
   - FileShareOwners
   - FileShareUsers
2. Создайте следующие учетные записи Active Directory в качестве учетных записей службы:
   - FileShareOwner
   - FileShareUser

   Из соображений безопасности пользователи этих учетных записей (и всех веб-ролей) должны отличаться друг от друга и иметь надежные имена пользователей и пароли. Задайте пароли со следующими условиями:
   - Включите параметр **Срок действия пароля не ограничен**.
   - Включите параметр **Запретить смену пароля пользователем**.
   - Отключите параметр **Требовать смены пароля при следующем входе в систему**.
3. Добавьте учетные записи в группы следующим образом:
   - Добавьте **FileShareOwner** в группу **FileShareOwners**.
   - Добавьте **FileShareUser** в группу **FileShareUsers**.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Подготовка групп и учетных записей в рабочей группе

>[!NOTE]
> При настройке файлового сервера выполните указанные ниже команды в окне командной строки с правами администратора. *Не используйте PowerShell.*

Если вы используете указанный выше шаблон Azure Resource Manager, пользователи уже созданы.

1. Выполните следующие команды, чтобы создать учетные записи FileShareOwner и FileShareUser. Замените `<password>` собственными значениями.
    ``` DOS
    net user FileShareOwner <password> /add /expires:never /passwordchg:no
    net user FileShareUser <password> /add /expires:never /passwordchg:no
    ```
2. Задайте неограниченный срок действия паролей для учетных записей, выполнив следующие команды WMIC:
    ``` DOS
    WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
    WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
    ```
3. Создайте локальные группы FileShareUsers и FileShareOwners и добавьте в них учетные записи из первого шага:
    ``` DOS
    net localgroup FileShareUsers /add
    net localgroup FileShareUsers FileShareUser /add
    net localgroup FileShareOwners /add
    net localgroup FileShareOwners FileShareOwner /add
    ```

### <a name="provision-the-content-share"></a>Подготовка общей папки содержимого

В общей папке содержимого размещается содержимое веб-сайта клиента. Процедура подготовки общей папки содержимого на одном файловом сервере одинакова для сред Active Directory и рабочей группы. Однако для отказоустойчивого кластера в Active Directory эта процедура отличается.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Подготовка общей папки содержимого на одном файловом сервере (Active Directory или рабочей группы)

На одном файловом сервере выполните следующие команды в командной строке с повышенными привилегиями. Замените значение `C:\WebSites` соответствующими путями в своей среде.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="add-the-fileshareowners-group-to-the-local-administrators-group"></a>Добавление группы FileShareOwners в группу локальных администраторов

Для правильной работы службы удаленного управления Windows необходимо добавить группу FileShareOwners в группу локальных администраторов.

#### <a name="active-directory"></a>Active Directory

Выполните следующие команды в командной строке с повышенными привилегиями на файловом сервере или на каждом файловом сервере, который используется как узел отказоустойчивого кластера. Замените значение `<DOMAIN>` доменным именем, которое необходимо использовать.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Рабочая группа

Выполните следующую команду в командной строке с повышенными привилегиями на файловом сервере:

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>Настройка управления доступом к общим папкам

Выполните указанные ниже команды в командной строке с повышенными привилегиями на файловом сервере или на узле отказоустойчивого кластера, который является текущим владельцем кластерного ресурса. Замените значения, выделенные курсивом, значениями для конкретной среды.

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

## <a name="prepare-the-sql-server-instance"></a>Подготовка экземпляра SQL Server

Для службы приложений Azure в базах данных размещения и измерений Azure Stack необходимо подготовить экземпляр SQL Server, чтобы разместить базы данных службы приложений.

Чтобы развернуть Пакет средств разработки Azure Stack, можно использовать SQL Server Express 2014 с пакетом обновления 2 (SP2) или более поздней версии.

Для рабочих сред и с целью обеспечения высокого уровня доступности следует использовать полную версию SQL Server 2014 с пакетом обновления 2 (SP2) или более поздней версии, включить аутентификацию в смешанном режиме и выполнить развертывание в [конфигурации, обеспечивающей высокий уровень доступности](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Экземпляр SQL Server для службы приложений Azure в Azure Stack должен быть доступен из всех ролей службы приложений. Вы можете развернуть SQL Server в стандартной подписке поставщика в Azure Stack. Либо можно использовать существующую инфраструктуру в организации (при наличии подключения к Azure Stack). Если вы используете образ Azure Marketplace, не забудьте соответствующим образом настроить брандмауэр.

Для любой роли SQL Server можно использовать экземпляр по умолчанию или именованный экземпляр. Если используется именованный экземпляр, вручную запустите службу обозревателя SQL Server и откройте порт 1434.

## <a name="create-an-azure-active-directory-application"></a>Создание приложения Azure Active Directory

Настройте субъект-службу Azure AD для поддержки следующего:
- интеграция масштабируемых наборов виртуальных машин на уровнях рабочих ролей;
- единый вход для портала решения "Функции Azure" и расширенные средства разработчика.

Эти шаги применимы только к средам Azure Stack, защищенным с помощью Azure AD.

Администраторы должны выполнить настройку единого входа, чтобы:
- включить расширенные средства разработчиков в рамках службы приложений (Kudu);
- включить использование портала Функций Azure.

Выполните следующие действия.

1. Откройте экземпляр PowerShell с правами azurestack\AzureStackAdmin.
2. Перейдите к расположению скриптов, скачанных и извлеченных на [этапе подготовки](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Установите PowerShell для Azure Stack](azure-stack-powershell-install.md).
4. Запустите скрипт **Create-AADIdentityApp.ps1**. Когда появится запрос, введите идентификатор клиента Azure AD, используемый для развертывания Azure Stack. Например, введите **myazurestack.onmicrosoft.com**.
5. В окне **Учетные данные** введите учетную запись администратора службы Azure AD и пароль. Нажмите кнопку **ОК**.
6. Введите путь к файлу сертификата и пароль для [сертификата, созданного ранее](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Для этого шага по умолчанию создается сертификат **sso.appservice.local.azurestack.external.pfx**.
7. Скрипт создаст приложение в экземпляре клиента Azure AD. Запишите идентификатор приложения, который возвращается в выходных данных PowerShell. Он вам понадобится при установке.
8. Откройте новое окно в браузере и войдите на портал Azure в качестве [администратора службы Azure Active Directory](https://portal.azure.com).
9. Откройте поставщик ресурсов Azure AD.
10. Выберите **Регистрация приложений**.
11. Выполните поиск идентификатора приложения, возвращенного на шаге 7. Вы увидите приложение службы приложений.
12. Выберите **Приложение** в списке.
13. Последовательно выберите **Необходимые разрешения** > **Предоставление разрешений** > **Да**.

| Параметр Create-AADIdentityApp.ps1 | Обязательный или необязательный | Значение по умолчанию | ОПИСАНИЕ |
| --- | --- | --- | --- |
| DirectoryTenantName | Обязательно | Null | Идентификатор клиента Azure AD. Введите идентификатор GUID или строку. Пример: myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Обязательно | Null | Конечная точка Azure Resource Manager администратора. Пример: adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Обязательно | Null | Конечная точка Azure Resource Manager клиента. Пример: management.local.azurestack.external. |
| AzureStackAdminCredential | Обязательно | Null | Учетные данные администратора службы Azure AD. |
| CertificateFilePath | Обязательно | Null | Путь к файлу сертификата приложения идентификации, созданному ранее. |
| CertificatePassword | Обязательно | Null | Пароль, который помогает защитить закрытый ключ сертификата. |

## <a name="create-an-active-directory-federation-services-application"></a>Создание приложения служб федерации Active Directory (AD FS)

В средах Azure Stack, защищенных с помощью AD FS, необходимо настроить субъект-службу AD FS для поддержки следующего:
- интеграция масштабируемых наборов виртуальных машин на уровнях рабочих ролей;
- единый вход для портала решения "Функции Azure" и расширенные средства разработчика.

Администраторы должны выполнить настройку единого входа, чтобы:
- настроить субъект-службу для интеграции масштабируемого набора виртуальных машин на уровнях рабочих ролей;
- включить расширенные средства разработчиков в рамках службы приложений (Kudu);
- включить использование портала Функций Azure.

Выполните следующие действия.

1. Откройте экземпляр PowerShell с правами azurestack\AzureStackAdmin.
2. Перейдите к расположению скриптов, скачанных и извлеченных на [этапе подготовки](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Установите PowerShell для Azure Stack](azure-stack-powershell-install.md).
4.  Запустите скрипт **Create-ADFSIdentityApp.ps1**.
5.  В окне **Учетные данные** укажите учетную запись администратора облака AD FS и пароль. Нажмите кнопку **ОК**.
6.  Предоставьте путь к файлу сертификата и пароль для [сертификата, созданного ранее](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Для этого шага по умолчанию создается сертификат **sso.appservice.local.azurestack.external.pfx**.

| Параметр Create-ADFSIdentityApp.ps1 | Обязательный или необязательный | Значение по умолчанию | ОПИСАНИЕ |
| --- | --- | --- | --- |
| AdminArmEndpoint | Обязательно | Null | Конечная точка Azure Resource Manager администратора. Пример: adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Обязательно | Null | Привилегированная конечная точка. Пример: AzS-ERCS01. |
| CloudAdminCredential | Обязательно | Null | Учетные данные домена администратора облака Azure Stack. Пример: Azurestack\CloudAdmin. |
| CertificateFilePath | Обязательно | Null | Путь к PFX-файлу сертификата приложения идентификации. |
| CertificatePassword | Обязательно | Null | Пароль, который помогает защитить закрытый ключ сертификата. |


## <a name="next-steps"></a>Дополнительная информация

[Установка поставщика ресурсов службы приложений](azure-stack-app-service-deploy.md).
