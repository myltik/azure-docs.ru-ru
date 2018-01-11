---
title: "Вопросы и ответы по проблемам конфигурации и управления для облачных служб Microsoft Azure | Документы Майкрософт"
description: "В этой статье приведены часто задаваемые вопросы по конфигурации и управлению для облачных служб Microsoft Azure."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: genli
ms.openlocfilehash: 7a1dc5d755303d12aa070308a19ab81ef2b911fb
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Проблемы конфигурации и управления для облачных служб Azure. Вопросы и ответы (FAQ)

В этой статье приведены часто задаваемые вопросы по конфигурации и управлению для [облачных служб Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Сведения о размерах приводятся в статье [Размеры для облачных служб](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certificates**

- [Почему неполон цепочки сертификатов my сертификат SSL облачные службы](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- [Что такое назначение «Windows Azure Tools шифрования для расширения сертификатов»](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Как создать подписи сертификата запроса (CSR) без «RDP-ing» в экземпляре?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Истекает срок действия сертификата управления My облачной службы. Как обновлять его?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Как автоматизировать установку основной сертификат SSL (PFX) и промежуточный certificate(.p7b)?](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)

**Мониторинг и запись**

- [Каковы возможности предстоящих облачной службы на портале Azure, что может помочь управление и наблюдение за приложениями?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Почему IIS остановить запись в каталог журнала?](#why-does-iis-stop-writing-to-the-log-directory)

**Конфигурация сети**

- [Как задать время ожидания простоя для подсистемы балансировки нагрузки Azure?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Как связать статический IP-адрес моей облачную службу?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Каковы функции и возможности, предоставляемые Azure basic IP-адресов и Идентификаторы и DDOS?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Как включить HTTP/2 на виртуальной Машине облачных служб?](#how-to-enable-http2-on-cloud-services-vm)

**Разрешения**

- [Можно внутренней инженеров удаленного рабочего стола Майкрософт для облачной службы экземпляры без разрешения?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Не удается выполнить удаленный рабочий стол для облачной службы ВМ с помощью RDP-файл. Я получить следующая ошибка: ошибка проверки подлинности (код: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Масштабирование**

- [Я не может использовать более чем X экземпляров](#i-cannot-scale-beyond-x-instances)
- [Как настроить Автомасштабирование на основе памяти](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Универсальный**

- [Как добавить «nosniff» на веб-сайте?](#how-do-i-add-nosniff-to-my-website)
- [Настройка IIS для веб-роли](#how-do-i-customize-iis-for-a-web-role)
- [Что такое предельную квоту для облака в службе](#what-is-the-quota-limit-for-my-cloud-service)
- [Почему диск на моей Виртуальной облачной службы определяет очень мало свободного места на диске](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Как добавить расширение защиты от вредоносных программ для моей облачных служб автоматической?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Как включить указание имени сервера (SNI) для облачных служб?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Как добавить теги в облачной службе Azure?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Портал Azure не отображает версию пакета SDK для моей облачной службы. Каким образом, можно получить?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Требуется завершение работы облачной службы для нескольких месяцев. Как сократить стоимость выставления счетов для облачной службы без потери IP-адрес?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Сертификаты

### <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Почему неполон цепочки сертификатов my сертификат SSL облачные службы
    
Корпорация Майкрософт рекомендует пользователям установить полную цепочку сертификатов (конечный сертификат, промежуточные сертификаты и корневой сертификат) вместо только конечного сертификата. При установке только конечного сертификата вы зависите от того, как Windows построит цепочку сертификатов, проходя список доверия сертификатов. Если при попытке Windows проверить сертификат происходит перебой в сети или проблема DNS, сертификат может посчитаться недействительным. Установив полную цепочку сертификатов, можно избежать этой проблемы. В публикации в блоге [Как установить связанный в цепочку SSL-сертификат](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) показано, как это сделать.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>В чем заключается цель сертификата шифрования Microsoft Azure Tools для расширений?

Эти сертификаты создаются автоматически при добавлении расширения к облачной службе. Чаще всего это расширение WAD или RDP, но может быть и другое расширение, например антивредоносное ПО или сборщик журналируемых данных. Эти сертификаты используются только для шифрования и расшифровки закрытой конфигурации расширения. Дата окончания срока действия никогда не проверяется, поэтому истечение срока действия сертификата не имеет значения. 

Эти сертификаты можно игнорировать. Если вы хотите очистить сертификаты, можно попробовать удалить их все. Azure будет выдавать ошибку при попытке удалить сертификат, который используется.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Как создать запрос подписи сертификата (CSR) без обращения к экземпляру с использованием RDP?

Ответ см. в следующем руководстве:

[Получение сертификата для использования с веб-сайтами Microsoft Azure (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

CSR является в текстовый файл. Необязательно должен быть создан из компьютера сертификат будет в конечном счете использования. Хотя этот документ был написан для службы приложений, создание CSR является универсальным и подходит также для облачных служб.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Истекает срок действия сертификата управления My облачной службы. Как обновлять его?

Можно использовать следующие команды PowerShell для обновления сертификатов управления:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

**Get-AzurePublishSettingsFile** создаст новый сертификат управления в **подписки** > **сертификаты управления** на портале Azure. Имя нового сертификата выглядит как «YourSubscriptionNam]-[CurrentDate] - учетные данные».

### <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Как автоматизировать установку основной сертификат SSL (PFX) и промежуточный certificate(.p7b)?

Можно автоматизировать эту задачу с помощью скрипта запуска (пакета или cmd и PowerShell) и зарегистрировать этот скрипт запуска в файле определения службы. Добавьте скрипт запуска и сертификат (.p7b файл) в папке проекта том же каталоге, скрипта запуска.

Дополнительные сведения см. в следующих статьях:
- [Настройка и выполнение задач запуска для облачной службы](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-startup-tasks)
- [Общие задачи запуска облачной службы](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Мониторинг и ведение журнала

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Каковы возможности предстоящих облачной службы на портале Azure, что может помочь управление и наблюдение за приложениями?

В ближайшее время появится возможность создавать новый сертификат для удаленного рабочего стола (RDP). Также можно выполнить этот скрипт:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
В ближайшее время появится возможность выбора BLOB-объектов или локального расположения в качестве расположения для передачи csdef и cscfg. С помощью командлета [New-AzureDeployment](/powershell/module/azure/new-azuredeployment?view=azuresmps-4.0.0) можно задать значение каждого расположения.

Возможность наблюдать за метриками на уровне экземпляра. Дополнительные возможности мониторинга см. в статье [Мониторинг облачных служб](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Почему IIS останавливает запись в каталог журналов?
Вы израсходовали квоту локального хранилища для записи в каталог журналов. Чтобы исправить это, выполните одно из следующих трех действий.
* Включите диагностику для IIS и задайте периодическое перемещение этой диагностики в хранилище BLOB-объектов.
* Вручную удалите файлы журнала из каталога ведения журнала.
* Увеличьте квоту для локальных ресурсов.

Дополнительные сведения см. в следующих документах:
* [Хранение и просмотр диагностических данных в службе хранилища Azure](cloud-services-dotnet-diagnostics-storage.md)
* [Журналы IIS остановить запись в облачной службе](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

## <a name="network-configuration"></a>Конфигурация сети

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Как задать время ожидания простоя для Azure Load Balancer?
Время ожидания можно указать в файле определения службы (CSDEF-файле) следующим образом.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Дополнительные сведения см. в статье [Новое: настраиваемое время ожидания простоя для Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/).

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Как связать статический IP-адрес моей облачную службу?
Чтобы настроить статический IP-адрес, необходимо создать зарезервированный IP-адрес. Этот зарезервированный IP-адрес может быть связан с новой облачной службой или к существующему развертыванию. Дополнительные сведения см. в следующих документах.
* [Как создать зарезервированный IP-адрес](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Зарезервированный IP-адрес существующей облачной службы](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Связать зарезервированный IP-адрес для новой облачной службы](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Связывание зарезервированного IP-адреса с работающей развернутой системой](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Связать зарезервированный IP-адрес облачной службы с помощью файла конфигурации службы](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Каковы функции и возможности, предоставляемые базовыми IPS/IDS и DDOS Azure?
Azure имеет IPS/IDS на физических серверах в центре обработки данных для защиты от угроз. Кроме того, клиенты могут разворачивать сторонние решения по обеспечению безопасности, такие как брандмауэры приложений, сетевые брандмауэры, антивредоносное ПО, системы обнаружения вторжений, системы предотвращения (IDS/IPS) и многое другое. Дополнительные сведения см. в разделе [Защита данных и ресурсов и соответствие глобальным стандартам безопасности](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Корпорация Майкрософт осуществляет постоянный мониторинг серверов, сетей и приложений для обнаружения угроз. Комплексный подход к управлению угрозами в Azure использует системы обнаружения вторжений, предотвращения распределенных атак типа "отказ в обслуживании" (DDoS), тесты на проникновение, поведенческую аналитику, обнаружение аномалий и машинное обучение для постоянного усиления защиты и снижения рисков. Microsoft Antimalware для Azure защищает облачных служб Azure и виртуальных машин. Вы можете разворачивать сторонние решения по обеспечению безопасности, такие как брандмауэры приложений, сетевые брандмауэры, антивредоносное ПО, системы обнаружения вторжений, системы предотвращения (IDS/IPS) и многое другое.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Как включить HTTP/2 на виртуальной машине облачных служб?

Windows 10 и Windows Server 2016 в стандартной конфигурации поддерживают HTTP/2 как для клиента, так и для сервера. Если ваш клиент (браузер) подключается к серверу IIS через TLS и согласовывает HTTP/2 через расширения TLS, вам не нужно вносить никаких изменений на стороне сервера. Это связано с тем, что через TLS по умолчанию отсылается заголовок h2-14, описывающий использование HTTP/2. Если же ваш клиент отправляет заголовок Upgrade для обновления до HTTP/2, на стороне сервера следует внести указанные ниже изменения, чтобы обновление сработало правильно и вы смогли использовать подключение HTTP/2. 

1. Запустите regedit.exe.
2. Перейдите к разделу реестра HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Создайте новое значение DWORD с именем **DuoEnabled**.
4. Задайте для него значение 1.
5. Перезапустите сервер.
6. Перейдите к **веб-сайту по умолчанию** и в разделе **Привязки** создайте новую привязку TLS с только что созданным самозаверяющим сертификатом. 

Дополнительные сведения можно найти в разделе 

- [HTTP/2 on IIS](https://blogs.iis.net/davidso/http2) (HTTP/2 в IIS)
- [Video: HTTP/2 in Windows 10: Browser, Apps and Web Server](https://channel9.msdn.com/Events/Build/2015/3-88) (Видео: HTTP/2 в Windows 10 для браузера, приложений и веб-сервера)
         

Эти действия можно автоматизировать через задачу запуска, чтобы каждый раз, когда создается новый экземпляр PaaS, он может сделать изменения выше в системном реестре. Дополнительные сведения см. в разделе [способы настройки и запуска задачи запуска для облачной службы](cloud-services-startup-tasks.md).

 
Когда все будет готово, проверьте наличие HTTP/2 с помощью одного из следующих методов.

- Включите версию протокола для журналов IIS и изучите журналы IIS. HTTP/2 будет отображаться в журналах. 
- Включите средства разработчика клавишей F12 в браузере Internet Explorer или Edge и перейдите на вкладку "Сети", чтобы проверить версию протокола. 

Дополнительные сведения см. в статье [HTTP/2 on IIS](https://blogs.iis.net/davidso/http2) (HTTP/2 в IIS).

## <a name="permissions"></a>Разрешения

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Как реализовать доступ на основе ролей для облачных служб?
Облачные службы не поддерживают модель управления доступом на основе ролей (RBAC), так как она не является службой на основе Azure Resource Manager.

См. [Управление доступом на основе ролей в Azure и классические администраторы подписки](../active-directory/role-based-access-control-what-is.md#azure-rbac-vs-classic-subscription-administrators).

## <a name="remote-desktop"></a>Удаленный рабочий стол

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Можно внутренней инженеров удаленного рабочего стола Майкрософт для облачной службы экземпляры без разрешения?
Майкрософт следует strict процесс, который будет не позволяется внутренней удаленного рабочего стола в облачной службе без письменного разрешения (адрес электронной почты или других переписки) от владельца или она их.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Не удается выполнить удаленный рабочий стол для облачной службы ВМ с помощью RDP-файл. Я получить следующая ошибка: ошибка проверки подлинности (код: 0x80004005)

Эта ошибка может возникать при использовании RDP-файл с компьютера, присоединенного к Azure Active Directory. Чтобы устранить эту проблему, выполните следующие действия.

1. Щелкните правой кнопкой мыши загруженный RDP-файл, а затем выберите **изменить**.
2. Добавьте «&#92;» как префикс перед имя пользователя. Например, использовать **. \username** вместо **username**.

## <a name="scaling"></a>Масштабирование

### <a name="i-cannot-scale-beyond-x-instances"></a>Не удается выполнить масштабирование на более чем X экземпляров
В вашей подписке Azure есть ограничение на количество используемых ядер. При использовании всех доступных ядер масштабирование не сработает. Например если имеется ограничение в 100 ядер, это означает имеется 100 экземпляров A1 размера виртуальной машины для облачной службы или 50 A2 размера экземпляров виртуальных машин.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Как настроить Автомасштабирование на основе памяти

Автомасштабирование на основе памяти для облачных служб в настоящее время не поддерживается. 

Чтобы обойти эту проблему, можно использовать Application Insights. Автоматическое масштабирование приложения полезные сведения, как источник метрики и можно масштабировать число экземпляров роли, в зависимости от метрики виртуальной машине, как «Память».  Необходимо настроить Application Insights в файле пакета проекта облачной службы (*.cspkg) и включить расширение диагностики Azure в службе для реализации которого это было сделано.

Дополнительные сведения о том, как использовать пользовательские метрики через Application Insights, чтобы настроить автоматическое масштабирование в облачных службах см. в разделе [Приступая к работе с автоматическое масштабирование по метрике пользовательских в Azure](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md)

Дополнительные сведения о том, как интегрировать диагностики Azure с помощью Application Insights для облачных служб см. в разделе [отправки облачной службы, виртуальную машину или Service Fabric диагностических данных в Application Insights](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)

Дополнительные сведения об включить Application Insights для облачных служб см. в разделе [Application Insights для облачных служб Azure](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Дополнительные сведения о том, как включить ведение журнала диагностики Azure для облачных служб см. в разделе [Настройка диагностики для облачных служб Azure и виртуальных машин](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Универсальный

### <a name="how-do-i-add-nosniff-to-my-website"></a>Как добавить на веб-сайт запрет на сканирование?
Чтобы помешать клиентам сканировать типы MIME, добавьте соответствующий параметр в файл *web.config*.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Его можно также добавить как параметр в службах IIS. Используйте следующую команду из статьи [Распространенные задачи запуска](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Как настроить IIS для веб-роли?
Используйте сценарий запуска IIS из статьи [Распространенные задачи запуска](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Что такое предельную квоту для облака в службе
См. раздел [Ограничения определенных служб](../azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Почему диск на моей Виртуальной облачной службы определяет очень мало свободного места на диске
Это ожидаемое поведение, и оно не должно приводить к проблемам с вашим приложением. Ведение журнала включено для дисков % approot % на виртуальных машинах Azure PaaS, который фактически использует double объем пространства, файлы обычно занимают. Однако существует несколько моментов, которые следует учитывать, чтобы это не стало проблемой.

Размер диска %approot% вычисляется как <размер .cspkg + максимальный размер журнала + запас свободного пространства> или 1,5 ГБ, в зависимости от того, какое значение больше. Размер вашей ВМ никак не влияет на этот расчет. (Размер виртуальной машины влияет на размер временного диска C.) 

Запись на диск %approot% не поддерживается. Если вы записываете данные на виртуальную машину Azure, это необходимо делать во временный ресурс LocalStorage (или выбрать другой вариант, например хранилище BLOB-объектов, файлы Azure и т. п.). Поэтому объем свободного пространства в папке %approot% не имеет значения. Если вы не уверены, записывает ли ваше приложение данные на диск %approot%, всегда можно позволить службе поработать в течение нескольких дней, а затем сравнить размеры до и после. 

Azure ничего не будет записывать на диск %approot%. После создания VHD из CSPKG-файла и подключения его к виртуальной машине Azure запись данных на этот виртуальный жесткий диск может выполнять только ваше приложение. 

Параметры ведения журнала не подлежат настройке, поэтому его нельзя отключить.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Как автоматизировать добавление антивредоносного расширения для облачных служб?

Вы можете включить антивредоносное расширение с помощью сценария PowerShell в задаче запуска. Чтобы реализовать этот механизм, выполните действия в следующих статьях. 
 
- [Создание задачи запуска PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/Azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 ).

Дополнительные сведения о сценариях развертывания защиты от вредоносных программ и о том, как включить эту защиту на портале, вы найдете в разделе [Сценарии развертывания антивредоносного ПО](../security/azure-security-antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Как включить указание имени сервера для облачных служб?

Указание имени сервера в облачных службах можно включить, используя один из следующих методов.

**Метод 1: Использование PowerShell**

Привязка сетевого Адаптера можно настроить с помощью командлета PowerShell **New WebBinding** в задачу запуска для экземпляра роли облачной службы, как показано ниже:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Как описано [здесь](https://technet.microsoft.com/library/ee790567.aspx), $sslFlags может принимать одно из следующих значений.

|Значение|Значение|
------|------
|0|Указание имени сервера не используется|
|1|Указание имени сервера используется |
|2 |Привязка без указания имени сервера, которая использует центральное хранилище сертификатов|
|3|Привязка с указанием имени сервера, которая использует центральное хранилище сертификатов |
 
**Метод 2: Использование кода**

Также привязку с указанием имени сервера можно настроить, выполняя программный код при запуске роли, как описано в этой [записи блога](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
При использовании любого из представленных выше методов для работы привязки с указанием имени узла нужно сначала установить на экземплярах ролей соответствующие сертификаты (*.pfx) для конкретных имен узлов. Это можно сделать с помощью задачи запуска или в программном коде.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Как можно добавить теги к облачной службе Azure? 

Облачная служба представляет собой классический ресурс. Теги поддерживаются только ресурсами, созданными с использованием Azure Resource Manager. К классическим ресурсам, в том числе к облачной службе, теги применить нельзя. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Портал Azure не отображает версию пакета SDK для моей облачной службы. Каким образом, можно получить?

Мы работаем над перевод этот компонент на портале Azure. В то же время можно использовать следующие команды PowerShell получить версию пакета SDK:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Требуется завершение работы облачной службы для нескольких месяцев. Как сократить стоимость выставления счетов для облачной службы без потери IP-адрес?

Облачную службу уже развернутых возвращает плата за вычислений и хранения, он использует. Поэтому даже при выключении виртуальной Машины Azure, вы по-прежнему получить плата за хранилище. 

Вот, что делать, чтобы уменьшить расчетной без потери IP-адрес для службы.

1. [Зарезервировать IP-адрес](../virtual-network/virtual-networks-reserved-public-ip.md) перед удалением развертываний.  Только выставляется для этого IP-адреса. Дополнительные сведения о выставлении счетов IP адрес см. в разделе [цены IP-адреса](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Удаление развертывания. Не удаляйте xxx.cloudapp.net, которые можно использовать на будущее.
3. Если вы хотите повторно развернуть облачную службу с помощью того же зарезервированный IP-адрес, зарезервированным в подписке см. в разделе [зарезервированные IP-адреса для облачных служб и виртуальных машин](https://azure.microsoft.com/blog/reserved-ip-addresses/).

