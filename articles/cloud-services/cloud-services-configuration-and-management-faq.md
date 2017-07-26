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
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 7d7676be26a8b68ab9fda18388e2b8cd5ed5f60b
ms.contentlocale: ru-ru
ms.lasthandoff: 06/15/2017


---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Проблемы конфигурации и управления для облачных служб Azure. Вопросы и ответы (FAQ)

В этой статье приведены часто задаваемые вопросы по конфигурации и управлению для [облачных служб Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Сведения о размерах приводятся в статье [Размеры для облачных служб](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-add-nosniff-to-my-website"></a>Как добавить на веб-сайт запрет на сканирование?
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

## <a name="how-do-i-customize-iis-for-a-web-role"></a>Как настроить IIS для веб-роли?
Используйте сценарий запуска IIS из статьи [Распространенные задачи запуска](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

## <a name="i-cannot-scale-beyond-x-instances"></a>Не удается выполнить масштабирование на более чем X экземпляров
В вашей подписке Azure есть ограничение на количество используемых ядер. При использовании всех доступных ядер масштабирование не сработает. Например, если установлено ограничение в 100 ядер, это означает, что в облачной службе можно создать 100 экземпляров виртуальной машины размера A1 или 50 экземпляров виртуальной машины размера A2.

## <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Как реализовать доступ на основе ролей для облачных служб?
Облачные службы не поддерживают модель управления доступом на основе ролей (RBAC), так как она не является службой на основе Azure Resource Manager.

См. [Управление доступом на основе ролей в Azure и классические администраторы подписки](../active-directory/role-based-access-control-what-is.md#azure-rbac-vs-classic-subscription-administrators).

## <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Как задать время ожидания простоя для Azure Load Balancer?
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

## <a name="can-microsoft-internal-engineers-rdp-to-cloud-service-instances-without-permission"></a>Могут ли инженеры Майкрософт использовать протокол удаленного рабочего стола для доступа к экземплярам облачной службы без разрешения?
Корпорация Майкрософт следует строгому процессу, который не позволит внутренним инженерам осуществлять удаленный доступ к облачной службе без письменного разрешения (электронного письма или другого варианта письменного разрешения) от владельца или его уполномоченного лица.

## <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Почему цепочка сертификатов моего SSL-сертификата облачной службы неполная?
Корпорация Майкрософт рекомендует пользователям установить полную цепочку сертификатов (конечный сертификат, промежуточные сертификаты и корневой сертификат) вместо только конечного сертификата. При установке только конечного сертификата вы зависите от того, как Windows построит цепочку сертификатов, проходя список доверия сертификатов. Если при попытке Windows проверить сертификат происходит перебой в сети или проблема DNS, сертификат может посчитаться недействительным. Установив полную цепочку сертификатов, можно избежать этой проблемы. В публикации в блоге [Как установить связанный в цепочку SSL-сертификат](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) показано, как это сделать.

## <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Как связать статический IP-адрес с моей облачной службой?
Чтобы настроить статический IP-адрес, необходимо создать зарезервированный IP-адрес. Этот зарезервированный IP-адрес можно привязать к новой облачной службе или к существующему развертыванию. Дополнительные сведения см. в следующих документах.
* [Как создать зарезервированный IP-адрес](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Резервирование IP-адреса существующей облачной службы](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Связывание зарезервированного IP-адреса с новой облачной службой](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Связывание зарезервированного IP-адреса с работающей развернутой системой](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Связывание зарезервированного IP-адреса с облачной службой с помощью файла конфигурации службы](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

## <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Что такое квота для облачной службы?
См. раздел [Ограничения определенных служб](../azure-subscription-service-limits.md#subscription-limits).

## <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Почему на диске виртуальной машины облачной службы определяется очень мало свободного места?
Это ожидаемое поведение, и оно не должно приводить к проблемам с вашим приложением. Для диска %uproot% в виртуальных машинах PaaS Azure включено ведение журнала, что фактически вдвое увеличивает объем пространства, который обычно занимают файлы. Однако существует несколько моментов, которые следует учитывать, чтобы это не стало проблемой.

Размер диска %approot% вычисляется как <размер .cspkg + максимальный размер журнала + запас свободного пространства> или 1,5 ГБ, в зависимости от того, какое значение больше. Размер вашей ВМ никак не влияет на этот расчет. (Размер виртуальной машины влияет на размер временного диска C.) 

Запись на диск %approot% не поддерживается. Если вы записываете данные на виртуальную машину Azure, это необходимо делать во временный ресурс LocalStorage (или выбрать другой вариант, например хранилище BLOB-объектов, файлы Azure и т. п.). Поэтому объем свободного пространства в папке %approot% не имеет значения. Если вы не уверены, записывает ли ваше приложение данные на диск %approot%, всегда можно позволить службе поработать в течение нескольких дней, а затем сравнить размеры до и после. 

Azure ничего не будет записывать на диск %approot%. После создания VHD из CSPKG-файла и подключения его к виртуальной машине Azure запись данных на этот виртуальный жесткий диск может выполнять только ваше приложение. 

Параметры ведения журнала не подлежат настройке, поэтому его нельзя отключить.

## <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Каковы функции и возможности, предоставляемые базовыми IPS/IDS и DDOS Azure?
Azure имеет IPS/IDS на физических серверах в центре обработки данных для защиты от угроз. Кроме того, клиенты могут разворачивать сторонние решения по обеспечению безопасности, такие как брандмауэры приложений, сетевые брандмауэры, антивредоносное ПО, системы обнаружения вторжений, системы предотвращения (IDS/IPS) и многое другое. Дополнительные сведения см. в разделе [Защита данных и ресурсов и соответствие глобальным стандартам безопасности](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Корпорация Майкрософт осуществляет постоянный мониторинг серверов, сетей и приложений для обнаружения угроз. Комплексный подход к управлению угрозами в Azure использует системы обнаружения вторжений, предотвращения распределенных атак типа "отказ в обслуживании" (DDoS), тесты на проникновение, поведенческую аналитику, обнаружение аномалий и машинное обучение для постоянного усиления защиты и снижения рисков. Антивредоносное ПО Майкрософт для Azure защищает облачные службы и виртуальные машины Azure. Вы можете разворачивать сторонние решения по обеспечению безопасности, такие как брандмауэры приложений, сетевые брандмауэры, антивредоносное ПО, системы обнаружения вторжений, системы предотвращения (IDS/IPS) и многое другое.

## <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Почему IIS останавливает запись в каталог журналов?
Вы израсходовали квоту локального хранилища для записи в каталог журналов. Чтобы исправить это, выполните одно из следующих трех действий.
* Включите диагностику для IIS и задайте периодическое перемещение этой диагностики в хранилище BLOB-объектов.
* Вручную удалите файлы журнала из каталога ведения журнала.
* Увеличьте квоту для локальных ресурсов.

Дополнительные сведения см. в следующих документах:
* [Хранение и просмотр диагностических данных в службе хранилища Azure](cloud-services-dotnet-diagnostics-storage.md)
* [Журналы IIS прекращают запись в облачной службе](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

## <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>В чем заключается цель сертификата шифрования Microsoft Azure Tools для расширений?
Эти сертификаты автоматически создаются каждый раз при добавлении расширения в облачную службу. Чаще всего это расширение WAD или RDP, но может быть и другое расширение, например антивредоносное ПО или сборщик журналируемых данных. Эти сертификаты используются только для шифрования и расшифровки закрытой конфигурации расширения. Дата окончания срока действия никогда не проверяется, поэтому истечение срока действия сертификата не имеет значения. 

Эти сертификаты можно игнорировать. Если вы хотите очистить сертификаты, можно попробовать удалить их все. Azure будет выдавать ошибку при попытке удалить сертификат, который используется.

## <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Как создать запрос подписи сертификата (CSR) без обращения к экземпляру с использованием RDP?
Ответ см. в следующем руководстве:

>[Получение сертификата для использования с веб-сайтами Microsoft Azure (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Обратите внимание, что CSR является просто текстовым файлом. Он НЕ должен быть создан на компьютере, на котором в конечном счете будет использоваться сертификат. Хотя этот документ был написан для службы приложений, создание CSR является универсальным и подходит также для облачных служб.

