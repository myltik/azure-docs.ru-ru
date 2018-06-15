---
title: Управление виртуальными машинами Windows Azure Pack из Azure Stack | Документация Майкрософт
description: Узнайте, как управлять виртуальными машинами Windows Azure Pack (WAP) с пользовательского портала Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 213c2792-d404-4b44-8340-235adf3f8f0b
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: a7e4896c84938b392a86f4d9609c4932324c785d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2018
ms.locfileid: "29735830"
---
# <a name="manage-windows-azure-pack-virtual-machines-from-azure-stack"></a>Управление виртуальными машинами Windows Azure Pack из Azure Stack

*Область применения: Пакет средств разработки Azure Stack*

В Пакете средств разработки Azure Stack можно активировать доступ из пользовательского портала Azure Stack к виртуальным машинам клиента, работающим в Windows Azure Pack. Пользователи могут использовать портал Azure Stack, чтобы управлять имеющимися виртуальными машинами IaaS и виртуальными сетями. Эти ресурсы доступны в Windows Azure Pack с помощью базовых компонентов Service Provider Foundation (SPF) и Virtual Machine Manager (VMM). В частности, пользователям доступно следующее:

* поиск ресурсов;
* анализ значений конфигурации;
* запуск виртуальной машины или остановка ее работы;
* подключение к виртуальной машине с помощью протокола удаленного рабочего стола (RDP);
* создание контрольных точек и управление ими;
* удаление виртуальных машин и сетей.

Эти функции обеспечиваются соединителем Windows Azure Pack для Azure Stack (предварительная версия). Из этой статьи вы узнаете, как настроить соединитель для среды Azure Stack с одним узлом.

Для этого соединителя (предварительный выпуск) учтите такие рекомендации:
* используйте соединитель Windows Azure Pack только в средах тестирования (для Azure Stack и Windows Azure Pack), а не рабочих развертываниях;
* необходимо использовать накопительный пакет обновления (UR) Windows Azure Pack версии 9.1 или более поздней, а также накопительный пакет обновления VMM и System Center SPF версии 9 или более поздней;
* компоненты VMM и SPF, которые можно использовать, включают System Center 2012 R2 или System Center 2016;
* обязательно выполните этапы настройки Azure Stack и Windows Azure Pack;
* эти инструкции применимы к средам необлачной системы платформы (CPS);
* чтобы узнать об известных проблемах, ознакомьтесь со статьей [Microsoft Azure Stack troubleshooting](azure-stack-troubleshooting.md) (Устранение неполадок, связанных с Microsoft Azure Stack).


## <a name="architecture"></a>Архитектура
На схеме ниже показаны основные компоненты соединителя Windows Azure Pack.

![Компоненты соединителя Windows Azure Pack](media/azure-stack-manage-wap/image1.png)

Обратите внимание на следующее:
* пользовательский портал Azure Stack получает сведения о ресурсах из двух облаков (Azure Stack и Windows Azure Pack);
* у пользователя должна быть учетная запись, которую можно использовать в обеих средах;
* у пользовательского портала Azure Stack должен быть сетевой доступ к компонентам, работающим в Windows Azure Pack;
* в разделе **WAP** схемы находятся новые модули соединителя Windows Azure Pack (расширение WAP и соединитель) и имеющийся API клиента Windows Azure Pack с компонентами SPF и VMM.


## <a name="identity-management"></a>Управление удостоверениями
API клиента Windows Azure Pack должен доверять службе маркеров безопасности (STS) Azure Stack.

Когда пользователь выполняет действие на портале Azure Stack, который предоставляет доступ к ресурсам Windows Azure Pack, портал использует API клиента Windows Azure Pack. Поэтому соответствующий маркер проверки подлинности пользователя должен предоставляться доверенной службой маркеров безопасности (STS). Рассмотрим схему ниже.

![Схема проверки подлинности соединителя Windows Azure Pack](media/azure-stack-manage-wap/image2.png)

В среде комплекта разработки Windows Azure Pack и Azure Stack используют независимые поставщики удостоверений. Пользователи, которые получают доступ к двум средам с портала Azure Stack, должны использовать одно имя участника-пользователя (UPN) для двух поставщиков удостоверений. Например, учетная запись *azurestackadmin@azurestack.local* также должна присутствовать в службе STS для Windows Azure Pack. Если AD FS не настроены на поддержку исходящих отношений доверия, необходимо установить доверие из компонентов Windows Azure Pack (API клиента) для экземпляра Azure Stack AD FS.

## <a name="prerequisites"></a>предварительным требованиям

### <a name="download-the-windows-azure-pack-connector"></a>Загрузка соединителя Windows Azure Pack
В [Центре загрузки Майкрософт](https://aka.ms/wapconnectorazurestackdlc) загрузите EXE-файл и извлеките его на локальный компьютер. Позже вы скопируете его содержимое на компьютер, у которого есть доступ к среде Windows Azure Pack.

### <a name="deployment-option-requirement"></a>Требования для развертывания
Для интеграции с Windows Azure Pack вы можете развернуть Azure Stack с помощью AD FS или Azure Active Directory.

### <a name="connectivity-requirements"></a>Требования к подключению
1. У компьютера, который вы используете для доступа к порталу Azure Stack, должен также быть доступ к порталу клиента Windows Azure Pack через веб-браузер.
2. Виртуальная машина AzS-WASP01 в Azure Stack должна подключаться к компьютеру, у которого есть доступ к порталу клиента Windows Azure Pack. Используйте Ping.exe, чтобы проверить подключение к сети. 
3.  У вас должны быть действительные сертификаты для новых служб соединителя. Эти SSL-сертификаты должны выдаваться доверенным центром сертификации (ЦС). Вы не можете использовать самозаверяющие сертификаты. Среда Azure Stack должна доверять SSL-сертификатам (особенно для виртуальной машины AzS-WASP01). Этим сертификатам также должен доверять любой компьютер, который клиент может использовать для доступа к пользовательскому порталу Azure Stack.
 
    >[!NOTE]
    Так как AzS-WASP01 запускает Windows Server с параметром установки основных серверных компонентов, для импорта сертификата можно использовать программу командной строки, например Certutil.ext. Например, вы можете скопировать CER-файл в каталог c:\temp на виртуальной машине AzS-WASP01, а затем запустить команду **certutil -addstore "CA" "c:\temp\certname.cer"**.

4.  Чтобы установить RDP-подключение к виртуальным машинам клиента Windows Azure Pack через портал Azure Stack, в среде Windows Azure Pack должна разрешаться передача трафика с удаленного рабочего стола на виртуальные машины клиента.
5.  Чтобы установить подключение между виртуальными машинами клиента Azure Stack и Windows Azure Pack, их внешние IP-адреса должны маршрутизироваться в двух средах. Это подключение может также включать связывание DNS-сервера для разрешения имен виртуальных машин между разными средами.

### <a name="iis-requirements"></a>Установка расширения IIS
На компьютере, на котором размещается портал клиента Windows Azure Pack (может представлять собой физический узел, виртуальную машину или несколько виртуальных машин), должно быть установлено расширение IIS переопределения URL-адресов. Если оно еще не установлено, вы можете скачать его [здесь](https://www.iis.net/downloads/microsoft/url-rewrite). Если портал клиента размещается на нескольких виртуальных машинах, установите расширение на каждой из них.

## <a name="configure-azure-stack"></a>Настройка Azure Stack
Активируйте на портале Azure Stack многооблачный режим, а затем настройте соединитель Windows Azure Pack. Этот режим позволит пользователям выбрать облако для доступа к ресурсам.

Чтобы активировать многооблачный режим, запустите командлет Add-AzurePackConnector.ps1 после развертывания Azure Stack. В таблице ниже приведены параметры сценария.


|  Параметр | ОПИСАНИЕ | Пример |   
| -------- | ------------- | ------- |  
| AzurePackClouds | URI-адреса соединителей Windows Azure Pack. Эти адреса должны соответствовать порталам клиента Windows Azure Pack. | @{CloudName = "AzurePack1"; CloudEndpoint = "https://waptenantportal1:40005"},@{CloudName = "AzurePack2"; CloudEndpoint = "https://waptenantportal2:40005"}<br><br>  (По умолчанию используется значение порта 40005.) |  
| AzureStackCloudName | Метка для представления локального облака Azure Stack.| "AzureStack" |
| DisableMultiCloud | Переключатель, чтобы отключить многооблачный режим.| Недоступно |
| | |

Вы можете запустить сценарий Add-AzurePackConnector.ps1 сразу же после развертывания или позже. Чтобы запустить его сразу после развертывания, используйте тот же сеанс Windows PowerShell, в котором было выполнено развертывание Azure Stack. В противном случае вы можете открыть новый сеанс Windows PowerShell от имени администратора (используя для входа учетную запись azurestackadmin).

1. Запустите сценарий Add-AzurePackConnector.ps1 с помощью команд ниже (заменив приведенные значения значениями для своей среды). Обратите внимание, что сценарий Add-AzurePackConnector позволяет добавить несколько конечных точек соединителя Windows Azure Pack.
 
 ```powershell
    $cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local", `
    (ConvertTo-SecureString -String "<password>" -AsPlainText -Force))
    $session = New-PSSession -ComputerName 'azs-ercs01' `
     -Credential $cred `
     -ConfigurationName PrivilegedEndpoint `
     -Authentication Credssp

    # Enable Multicloud
    Invoke-Command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
    @{CloudName = "AzurePack_1"; CloudEndpoint = "https://waptenantportal1:40005"},`
    @{CloudName = "AzurePack_2"; CloudEndpoint = "https://waptenantportal2:40005" } `
    -AzureStackCloudName "AzureStack" }

 ```
> [!NOTE]
> В текущей сборке имеется проблема, когда после завершения сценария Add-AzurePackConnector он продолжает выполняться в опрашивающем цикле в течение длительного времени (несколько минут). Когда вы увидите сообщение **VERBOSE: Step 'Configure Azure Pack Connector' status: 'Success'** (ПОДРОБНО: состояние "Успешно" для действия "Настройка соединителя Azure Pack"), завершите выполнение сценария или подождите, когда он завершится сам. Принципиальной разницы нет, так как в любом случае настройка завершилась успешно.

2. Запишите значения созданных выходных файлов для каждой указанной среды Windows Azure Pack. Они находятся в папке: \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput. Эти файлы содержат сведения, необходимые для настройки целевых сред Windows Azure Pack. Вы передадите этот файл в сценарий в качестве параметра далее в инструкциях. В нем содержится следующее:

    * **AzurePackConnectorEndpoint**. Содержит конечную точку к службе соединителя Windows Azure Pack.
    * **AuthenticationIdentityProviderPartner**. Содержит следующие значения:
        * сертификат для подписи маркера проверки подлинности, которому должен доверять API клиента Windows Azure Pack, чтобы принимать вызовы из расширения портала Azure Stack;

        * область, связанная с сертификатом для подписи. Например, https://adfs.local.azurestack.global.external/adfs/c1d72562-534e-4aa5-92aa-d65df289a107/.

3.  Перейдите в папку, в которой содержатся выходные файлы (\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput), а затем скопируйте их на локальный компьютер. Файлы будут выглядеть примерно так: AzurePack-06-27-15-50.txt.

4.  Выполните проверку настройки.

    a. Откройте браузер и войдите на пользовательский портал Azure Stack (https://portal.local.azurestack.external/).
    
    Б. После входа от имени клиента и загрузки портала вы увидите ошибки получения подписок или расширений из облака Azure Pack. Нажмите кнопку **ОК**, чтобы закрыть эти сообщения. (Сообщения об ошибках исчезнут после настройки Windows Azure Pack.)

    c. Обратите внимание на раскрывающийся список **облаков** в верхнем левом углу портала.

    ![Выбор облака в пользовательском интерфейсе Azure Stack](media/azure-stack-manage-wap/image3.png)

## <a name="configure-windows-azure-pack"></a>Настройка Windows Azure Pack
Только в предварительной версии этого соединителя необходимо настроить Windows Azure Pack вручную.

>[!IMPORTANT]
В рамках этой предварительной версии используйте соединитель Windows Azure Pack только в средах тестирования, а не рабочих развертываниях.

1.  Установите MSI-файлы соединителя на виртуальной машине для портала клиента Windows Azure Pack, а затем установите сертификаты. (Если у вас несколько виртуальных машин для портала клиента, выполните это действие на каждой из них.)

    a. В проводнике скопируйте папку **WAPConnector** (которую вы загрузили ранее) в папку с именем **c:\temp** на виртуальной машине для портала клиента.

    Б. Откройте консоль или RDP-подключение к виртуальной машине портала клиента.

    c. Перейдите в каталог **c:\temp\wapconnector\setup\scripts**, а затем запустите сценарий **Install-Connector.ps1**, чтобы установить три MSI-файла. Никакие параметры больше не требуются.

     ```powershell
    cd C:\temp\wapconnector\setup\scripts\

    .\Install-Connector.ps1
    ```
     d. Перейдите в каталог **c:\inetpub**. Должны быть установлены три новых файла:

       * MgmtSvc-Connector

       * MgmtSvc-ConnectorExtension

       * MgmtSvc-ConnectorController

    д. В той же папке **c:\temp\wapconnector\setup\scripts** запустите сценарий **Configure-Certificates.ps1**, чтобы установить сертификаты. По умолчанию будет использоваться тот же сертификат, доступный для сайта портала клиента на портале Windows Azure Pack. Сертификат должен быть действительным (доверенным для виртуальной машины AzS-WASP01 Azure Stack и любого клиентского компьютера, у которого есть доступ к порталу Azure Stack). В противном случае подключения не произойдет. (Кроме того, можно явно передать отпечаток сертификата как параметр с помощью параметра -Thumbprint.)

     ```powershell
        cd C:\temp\wapconnector\setup\scripts\

        .\Configure-Certificates.ps1
    ```

    f. Чтобы завершить настройку этих трех служб, запустите сценарий **Configure-WapConnector.ps1**, чтобы обновить параметры файла Web.config.

    |  Параметр | ОПИСАНИЕ | Пример |   
    | -------- | ------------- | ------- |  
    | TenantPortalFQDN | Полное доменное имя портала клиента Windows Azure Pack. | tenant.contoso.com | 
    | TenantAPIFQDN | Полное доменное имя API клиента Windows Azure Pack. | tenantapi.contoso.com  |
    | AzureStackPortalFQDN | Полное доменное имя портала пользователя Azure Stack. | portal.local.azurestack.external |
    | | |
    
     ```powershell
    .\Configure-WapConnector.ps1 -TenantPortalFQDN "tenant.contoso.com" `
         -TenantAPIFQDN "tenantapi.contoso.com" `
         -AzureStackPortalFQDN "portal.local.azurestack.external"
    ```
    ж. Если у вас несколько виртуальных машин для портала клиента, выполните шаг 1 для каждой из них.

2. Установите новый MSI-файл API клиента на каждой виртуальной машине API клиента Windows Azure Pack.

    a. Если используется подсистема балансировки нагрузки, виртуальную машину можно пометить как автономную.

    Б. В проводнике скопируйте папку **WAPConnector** в папку с именем **c:\temp** на каждой виртуальной машине API клиента.

    c. Скопируйте файл AzurePackConnectorOutput.txt, сохраненный ранее, в папку **c:\temp\WAPConnector**.

    d. Откройте консоль или RDP-подключение к виртуальной машине API клиента, на которую вы скопировали файлы.
    
    д. Перейдите в каталог **c:\temp\wapconnector\setup\scripts** и запустите сценарий **Update-TenantAPI.ps1**. Эта новая версия API клиента WAP содержит изменение, чтобы активировать отношение доверия не только с текущей службой маркеров безопасности, но и с экземпляром AD FS на портале Azure Stack.

     ```powershell
    cd C:\temp\wapconnector\setup\packages\

    .\Update-TenantAPI.ps1
    ```

    f.  Повторите шаг 2 на любой виртуальной машине, на которой запущен API клиента.
3. Запустите сценарий Configure-TrustAzureStack.ps1 только на **одной** из виртуальных машин API клиента, чтобы добавить отношение доверия между API клиента и экземпляром AD FS на портале Azure Stack. Используйте учетную запись с правами системного администратора, чтобы получить доступ к базе данных Microsoft.MgmtSvc.Store. В этом сценарии используются следующие параметры.

    | Параметр | ОПИСАНИЕ | Пример |
    | --------- | ------------| ------- |
   | SqlServer | Имя SQL Server, который содержит базу данных Microsoft.MgmtSvc.Store. Этот параметр является обязательным. | SQLServer | 
   | DataFile | Выходной файл, созданный во время настройки многооблачного режима Azure Stack ранее. Этот параметр является обязательным. | AzurePack-06-27-15-50.txt | 
   | PromptForSqlCredential | Указывает, что сценарий должен запросить учетные данные для проверки подлинности SQL, которые будут использоваться при подключении к экземпляру SQL Server. Указанным учетным данным должны быть назначены необходимые разрешения для удаления баз данных, схем и имен для входа пользователей. Если не указать учетные данные, сценарий предположит, что у текущего контекста пользователя есть доступ. | Значение не требуется. |
   |  |  |

    Если вы не знаете, какой SQL Server используется, это можно узнать. Подключитесь к компьютеру, на котором расположен API клиента, используйте команду Unprotect-MgmtSvc, чтобы снять защиту с файла Web.config API клиента, и выполните поиск имени сервера в строке подключения. После получения имени выполните команду Protect-MgmtSvc, чтобы защитить файл Web.config API клиента.

  ```powershell
  cd C:\temp\wapconnector\setup\scripts\

 .\Configure-TrustAzureStack.ps1 -SqlServer "SQLServer" `
       -DataFile "C:\temp\wapconnector\AzurePackConnectorOutput.txt"
  ```

## <a name="example"></a>Пример
В примере ниже показано полное развертывание соединителя Windows Azure Pack в конфигурации Azure Stack с одним узлом и две быстрые установки Windows Azure Pack. (Для каждого комплекта Windows Azure Pack быстрая установка выполняется на отдельном компьютере. В примере ниже используются имена *wapcomputer1* и *wapcomputer2*.)

```powershell
# Run the following script on the Azure Stack host
$cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local",`
     (ConvertTo-SecureString -String "p@ssw0rd" -AsPlainText -Force))
$session = New-PSSession -ComputerName 'azs-ercs01' -Credential $cred `
     -ConfigurationName PrivilegedEndpoint -Authentication Credssp
 
# Enable Multicloud
invoke-command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
     @{CloudName = "AzurePack_1"; CloudEndpoint = "https://wapcomputer1.contoso.com:40005"},`
     @{CloudName = "AzurePack_2"; CloudEndpoint = "https://wapcomputer2.contoso.com:40005"}`
     -AzureStackCloudName "AzureStack" }  

```
Загрузите EXE-файл из [Центра загрузки Майкрософт](https://aka.ms/wapconnectorazurestackdlc), извлеките его и скопируйте папку WAPConnector в папку **c:\temp** на компьютере с Windows Azure Pack. Скопируйте файлы, созданные как выходные данные в сценарии выше (расположенные в папке \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput), в папку **c:\temp\WAPConnector**. (Файлы будут выглядеть примерно так: AzurePack-06-27-15-50.txt.) Затем запустите сценарий ниже — по одному разу для каждого экземпляра Windows Azure Pack:

 ```powershell
# Install Connector components
cd C:\temp\WAPConnector\Setup\Scripts
.\Install-Connector.ps1

# Configure Certificates for the new Connector services
.\Configure-Certificates.ps1

# Configure the Connector services
.\Configure-WapConnector.ps1 -TenantPortalFQDN "wapcomputer1.contoso.com" `
     -TenantAPIFQDN "wapcomputer1.contoso.com" `
     -AzureStackPortalFQDN "portal.local.azurestack.external"

# Install the updated TenantAPI
.\Update-TenantAPI.ps1

# Establish trust with the Azure Stack AD FS
.\Configure-TrustAzureStack.ps1 -SqlServer "wapcomputer1" `
     -DataFile "C:\temp\wapconnector\AzurePack-06-27-15-50.txt" 

```
## <a name="troubleshooting-tips"></a>Советы по устранению неполадок
1.  Проверьте наличие подключения между Azure Stack и Windows Azure Pack. Это подключение должно быть установлено между любым компьютером клиента, у которого есть доступ к порталу Azure Stack, и виртуальной машиной портала клиента Windows Azure Pack, на которой запущены новые службы соединителя.
2.  Все указанные полные доменные имена не должны содержать ошибок.
3.  Среда Azure Stack должна доверять SSL-сертификатам (особенно для виртуальной машины AzS-WASP01), используемым в новых службах соединителя. Этим сертификатам также должен доверять любой компьютер, который клиент может использовать для доступа к пользовательскому порталу Azure Stack.
4. Чтобы узнать об известных проблемах, ознакомьтесь со статьей [Microsoft Azure Stack troubleshooting](azure-stack-troubleshooting.md) (Устранение неполадок, связанных с Microsoft Azure Stack).


## <a name="next-steps"></a>Дополнительная информация
[Using the administrator portal in Azure Stack](azure-stack-manage-portals.md) (Использование портала администрирования в Azure Stack)
