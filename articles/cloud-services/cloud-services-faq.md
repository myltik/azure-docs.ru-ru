---
title: "Часто задаваемые вопросы о ролях облачных служб Azure | Документация Майкрософт"
description: "Часто задаваемые вопросы об облачных службах Azure. Ответы на некоторые распространенные вопросы о сертификатах, веб-ролях и рабочих ролях."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: a0fd75e0797319aecac29b48df75e7a268063e04
ms.lasthandoff: 04/11/2017


---
# <a name="cloud-services-faq"></a>Часто задаваемые вопросы об облачных службах
В этой статье содержатся ответы на некоторые часто задаваемые вопросы об облачных службах Microsoft Azure. Вы также можете посетить страницу [Часто задаваемые вопросы о поддержке Azure](http://go.microsoft.com/fwlink/?LinkID=185083) , где содержатся общие сведения о расценках и поддержке Azure. Сведения о размерах приводятся в статье [Размеры для облачных служб](cloud-services-sizes-specs.md) .

## <a name="certificates"></a>Сертификаты
### <a name="where-should-i-install-my-certificate"></a>Где следует разместить мой сертификат?
* **My**  
  Сертификаты приложений с закрытым ключом (\*.pfx, \*.p12).
* **CA**  
  — в это хранилище помещаются все промежуточные сертификаты (политики и вложенные ЦС).
* **ROOT**  
  — корневое хранилище ЦС, сюда следует поместить основной сертификат корневого ЦС.

### <a name="i-cant-remove-expired-certificate"></a>Не удается удалить сертификат с истекшим сроком действия
Azure не позволяет удалить сертификат, пока он используется. Сначала следует удалить развертывание, в котором используется этот сертификат, или задать для этого развертывания другой или обновленный сертификат.

### <a name="delete-an-expired-certificate"></a>Удаление сертификата с истекшим сроком действия
Если сертификат не используется, его можно удалить с помощью командлета PowerShell [Remove-AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) .

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>Истек срок действия сертификатов с именем "Службы управления Windows Azure для расширений"
Эти сертификаты создаются каждый раз, когда к облачной службе добавляется расширение, например расширение удаленного рабочего стола. Сертификаты используются только для шифрования и расшифровки закрытой конфигурации расширения. Не страшно, если срок их действия истечет. Дата действия сертификата не проверяется.

### <a name="certificates-i-have-deleted-keep-reappearing"></a>Удаленные сертификаты снова появляются
Такое обычно происходит из-за используемых средств, например Visual Studio. Всякий раз при подключении через средство, которое использует сертификат, этот сертификат снова передается в Azure.

### <a name="my-certificates-keep-disappearing"></a>Мои сертификаты исчезают
При перезапуске экземпляра виртуальной машины все локальные изменения будут утеряны. Используйте [задачи запуска](cloud-services-startup-tasks.md) , чтобы устанавливать сертификаты на виртуальную машину при каждом запуске роли.

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>Не удается найти мои сертификаты управления на портале
[Сертификаты управления](../azure-api-management-certs.md) доступны только на классическом портале Azure. Текущий портал Azure не использует сертификаты управления. 

### <a name="how-can-i-disable-a-management-certificate"></a>Как можно отключить сертификат управления?
[Сертификаты управления](../azure-api-management-certs.md) нельзя отключить. Если вы больше не хотите их использовать, то можете удалить их на классическом портале Azure.

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>Как можно создать SSL-сертификат для конкретного IP-адреса?
Следуйте указаниям в [учебнике по созданию сертификата](cloud-services-certs-create.md). Используйте IP-адрес в качестве DNS-имени.

## <a name="security"></a>Безопасность
### <a name="disable-ssl-30"></a>Отключение SSL 3.0
Для отключения SSL 3.0 и использования безопасности TLS создайте задачу запуска, описанную в этой записи блога: https://azure.microsoft.com/en-us/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/.

### <a name="add-nosniff-to-your-website"></a>Добавление **nosniff** на веб-сайт
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

### <a name="customize-iis-for-a-web-role"></a>Настройка IIS для веб-роли
Используйте сценарий запуска IIS из статьи [Распространенные задачи запуска](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

## <a name="scaling"></a>Масштабирование
### <a name="i-cannot-scale-beyond-x-instances"></a>Не удается выполнить масштабирование на более чем X экземпляров
В вашей подписке Azure есть ограничение на количество используемых ядер. При использовании всех доступных ядер масштабирование не сработает. Например, если установлено ограничение в 100 ядер, это означает, что в облачной службе можно создать 100 экземпляров виртуальной машины размера A1 или 50 экземпляров виртуальной машины размера A2.

## <a name="networking"></a>Сеть
### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Не удается зарезервировать IP-адрес в облачной службе с несколькими виртуальными IP-адресами
Сначала убедитесь, что экземпляр виртуальной машины, для который вы пытаетесь зарезервировать IP-адрес, включен. Во-вторых, убедитесь, что вы используете зарезервированные IP-адреса и для промежуточного, и для рабочего развертываний. **Не изменяйте** параметры во время обновления развертывания.

## <a name="remote-desktop"></a>Удаленный рабочий стол
### <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>Как использовать удаленный рабочий стол при наличии NSG?
Добавьте в NSG правила, разрешающие передачу трафика через порты **3389** и **20000**.  Удаленный рабочий стол использует порт **3389**.  В экземплярах облачной службы реализована балансировка нагрузки, поэтому вы не можете напрямую управлять подключением к экземпляру.  Агенты *RemoteForwarder* и *RemoteAccess* управляют трафиком RDP и позволяют клиенту отправлять файлы cookie по RDP и указывать отдельный экземпляр для подключения.  Агентам *RemoteForwarder* и *RemoteAccess* требуется, чтобы порт **20000*** был открыт. При использовании группы безопасности сети он может быть заблокирован.

