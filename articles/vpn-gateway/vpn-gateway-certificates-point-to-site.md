---
title: "Создание и экспорт сертификатов для подключений типа \"точка-сеть\" в Azure с помощью PowerShell | Документы Майкрософт"
description: "В этой статье приведены инструкции по созданию самозаверяющего корневого сертификата, экспорту открытого ключа и созданию сертификатов клиента с помощью PowerShell в Windows 10."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2017
ms.author: cherylmc
ms.openlocfilehash: be2e8fe12dee88ccf81faaa114056a29e03881bd
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-powershell-on-windows-10"></a>Создание и экспорт сертификатов для подключений типа "точка-сеть" с помощью PowerShell в Windows 10

Для аутентификации подключений типа "точка — сеть" используются сертификаты. Эта статья поможет создать самозаверяющий корневой сертификат, а также сертификаты клиента с помощью PowerShell в Windows 10. Чтобы ознакомиться с инструкциями по настройке подключения "точка-сеть", например по отправке корневых сертификатов, выберите одну из статей в следующем списке.

> [!div class="op_single_selector"]
> * [Создание самозаверяющих сертификатов с помощью PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Создание и экспорт сертификатов для подключений типа "точка — сеть" с помощью MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Настройка подключения "точка — сеть" модели Resource Manager на портале Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Настройка подключения "точка-сеть" — Resource Manager — PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Настройка подключения "точка —сеть" классической модели на портале Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 


Выполните описанные действия на компьютере с Windows 10. Командлеты PowerShell, которые используются для создания сертификатов, являются частью операционной системы Windows 10 и не работают в других версиях Windows. Компьютер Windows 10 требуется только для создания сертификатов. После создания сертификатов их можно отправить или установить в любой поддерживаемой клиентской операционной системе. 

При отсутствии доступа к компьютеру с Windows 10 для создания сертификатов можно использовать средство [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Сертификаты, созданные с помощью другого метода, можно установить в любой [поддерживаемой](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) клиентской операционной системе.

## <a name="rootcert"></a>Создание самозаверяющего корневого сертификата

Используйте командлет New-SelfSignedCertificate для создания самозаверяющего корневого сертификата. Дополнительные сведения о параметре см. в разделе [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. На компьютере под управлением Windows 10 откройте консоль Windows PowerShell с повышенными привилегиями.
2. Используйте следующий пример для создания самозаверяющего корневого сертификата. Следующий пример создает самозаверяющий корневой сертификат P2SRootCert, который автоматически устанавливается в папку Certificates-Current User\Personal\Certificates. Этот сертификат можно просмотреть, открыв файл *certmgr.msc* или раздел *Управление сертификатами пользователей*.

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

### <a name="cer"></a>Экспорт открытого ключа (CER)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Файл exported.cer необходимо отправить в Azure. Дополнительные сведения см. в разделе [Подготовка CER-файла корневого сертификата к передаче](vpn-gateway-howto-point-to-site-rm-ps.md#upload). Чтобы добавить дополнительные доверенные корневые сертификаты, ознакомьтесь с [этим разделом](vpn-gateway-howto-point-to-site-rm-ps.md#addremovecert) статьи.

### <a name="export-the-self-signed-root-certificate-and-public-key-to-store-it-optional"></a>Экспорт самозаверяющего корневого сертификата и открытого ключа для его сохранения (необязательно)

Может возникнуть необходимость экспортировать самозаверяющий корневой сертификат и сохранить его в надежном месте. При необходимости позднее можно будет установить его на другом компьютере и создать дополнительные сертификаты клиента или экспортировать другой CER-файл. Чтобы экспортировать самозаверяющий корневой сертификат в формате PFX, выберите корневой сертификат и выполните те же действия, что описаны в разделе [Экспорт сертификата клиента](#clientexport).

## <a name="clientcert"></a>Создание сертификата клиента

На каждом клиентском компьютере, который подключается к виртуальной сети с помощью подключения типа "точка —сеть", должен быть установлен сертификат клиента. Вы можете создать сертификат клиента из самозаверяющего корневого сертификата, а затем экспортировать и установить его. Если сертификат клиента не установлен, произойдет сбой аутентификации. 

Ниже описан способ создания сертификата клиента из самозаверяющего корневого сертификата. Из одного корневого сертификата можно создать несколько сертификатов клиента. При создании сертификатов клиента с помощью приведенных ниже инструкций сертификат клиента автоматически устанавливается на компьютер, который использовался для его создания. Если вы хотите установить сертификат клиента на другой клиентский компьютер, его можно экспортировать.

В примерах используется командлет New-SelfSignedCertificate для создания сертификата клиента, срок действия которого истекает через год. Дополнительные сведения о параметре, например о задании другого значения срока действия сертификата клиента, см. в разделе [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Пример 1

В этом примере используется переменная $cert, объявленная в предыдущем разделе. Если вы закрыли консоль PowerShell после создания самозаверяющего корневого сертификата или создаете дополнительные сертификаты клиента в новом сеансе консоли PowerShell, выполните инструкции, описанные в [примере 2](#ex2).

Измените и запустите пример, чтобы создать сертификат клиента. Если выполнить этот пример, не изменив его, то будет создан сертификат клиента P2SChildCert.  Если требуется указать другое имя дочернего сертификата, измените значение CN. Не изменяйте TextExtension при выполнении данного примера. Сертификат клиента, который создается, автоматически устанавливается в папку Certificates - Current User\Personal\Certificates на компьютере.

```powershell
New-SelfSignedCertificate -Type Custom -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Пример 2

Если вы создаете дополнительные сертификаты клиента или не используете тот же сеанс PowerShell, в котором был создан самозаверяющий корневой сертификат, выполните следующее.

1. Определите самозаверяющий корневой сертификат, установленный на компьютере. Этот командлет возвращает список сертификатов, установленных на компьютере.

  ```powershell
  Get-ChildItem -Path “Cert:\CurrentUser\My”
  ```
2. Найдите имя субъекта в полученном списке, а затем скопируйте отпечаток, расположенный рядом с ним, в текстовый файл. В следующем примере указано два сертификата. CN-имя — это имя самозаверяющего корневого сертификата, на основе которого требуется создать дочерний сертификат. В данном случае это P2SRootCert.

  ```
  Thumbprint                                Subject
  
  AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
  7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
  ```
3. Объявите переменную для корневого сертификата, используя отпечаток из предыдущего шага. Замените THUMBPRINT отпечатком корневого сертификата, на основе которого требуется создать дочерний сертификат.

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
  ```

  Например, если использовать отпечаток для P2SRootCert из предыдущего шага, то переменная будет выглядеть следующим образом.

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
  ```
4.  Измените и запустите пример, чтобы создать сертификат клиента. Если выполнить этот пример, не изменив его, то будет создан сертификат клиента P2SChildCert. Если требуется указать другое имя дочернего сертификата, измените значение CN. Не изменяйте TextExtension при выполнении данного примера. Сертификат клиента, который создается, автоматически устанавливается в папку Certificates - Current User\Personal\Certificates на компьютере.

  ```powershell
  New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="clientexport"></a>Экспорт сертификата клиента   

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

## <a name="install"></a>Установка экспортированного сертификата клиента

См. инструкции по [установке сертификата клиента для подключений типа "точка — сеть"](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Дальнейшие действия

Продолжайте настраивать параметры конфигурации типа "точка-сеть".

* При использовании модели развертывания на основе **Resource Manager** см. инструкции по [настройке подключения типа "точка — сеть" с использованием собственной аутентификации Azure на основе сертификата](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Инструкции для **классической** модели развертывания см. в статье [Настройка подключения типа "точка — сеть" к виртуальной сети с помощью портала Azure (классическая модель)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).