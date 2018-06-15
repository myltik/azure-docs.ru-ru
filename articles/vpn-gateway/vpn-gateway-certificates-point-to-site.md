---
title: Создание и экспорт сертификатов для подключений типа "точка-сеть" в Azure с помощью PowerShell | Документы Майкрософт
description: Создание самозаверяющего корневого сертификата, экспорт открытого ключа и создание сертификатов клиента с помощью PowerShell в Windows 10 или Windows Server 2016.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: cherylmc
ms.openlocfilehash: 385b6ed2e8104fd2e15e6e55d46dcd12b963ec6b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31423054"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Создание и экспорт сертификатов для подключений "точка — сеть" с помощью PowerShell

Для аутентификации подключений типа "точка — сеть" используются сертификаты. Эта статья поможет создать самозаверяющий корневой сертификат, а также сертификаты клиента с помощью PowerShell в Windows 10 или Windows Server 2016. Чтобы ознакомится с инструкциями по настройке подключения "точка — сеть", например как передать корневые сертификаты, выберите одну из статей в следующем списке:

> [!div class="op_single_selector"]
> * [Создание самозаверяющих сертификатов с помощью PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Создание и экспорт сертификатов для подключений типа "точка — сеть" с помощью MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Настройка подключения "точка — сеть" модели Resource Manager на портале Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Настройка подключения "точка-сеть" — Resource Manager — PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Настройка подключения "точка —сеть" классической модели на портале Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Выполните описанные действия на компьютере с Windows 10 или Windows Server 2016. Командлеты PowerShell, которые используются для создания сертификатов, являются частью операционной системы и не работают в других версиях Windows. Компьютер с Windows 10 или Windows Server 2016 требуется только для создания сертификатов. После создания сертификатов их можно отправить или установить в любой поддерживаемой клиентской операционной системе. 

При отсутствии доступа к компьютеру с Windows 10 или Windows Server 2016 для создания сертификатов можно использовать [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Сертификаты, созданные с помощью другого метода, можно установить в любой [поддерживаемой](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) клиентской операционной системе.

## <a name="rootcert"></a>1. Создание самозаверяющего корневого сертификата

Используйте командлет New-SelfSignedCertificate для создания самозаверяющего корневого сертификата. Дополнительные сведения о параметре см. в разделе [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. На компьютере под управлением Windows 10 или Windows Server 2016 откройте консоль Windows PowerShell с повышенными привилегиями.
2. Используйте следующий пример для создания самозаверяющего корневого сертификата. Следующий пример создает самозаверяющий корневой сертификат P2SRootCert, который автоматически устанавливается в папку Certificates-Current User\Personal\Certificates. Этот сертификат можно просмотреть, открыв файл *certmgr.msc* или раздел *Управление сертификатами пользователей*.

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

## <a name="clientcert"></a>2. Создание сертификата клиента

На каждом клиентском компьютере, который подключается к виртуальной сети с помощью подключения типа "точка —сеть", должен быть установлен сертификат клиента. Вы можете создать сертификат клиента из самозаверяющего корневого сертификата, а затем экспортировать и установить его. Если сертификат клиента не установлен, произойдет сбой аутентификации. 

Ниже описан способ создания сертификата клиента из самозаверяющего корневого сертификата. Из одного корневого сертификата можно создать несколько сертификатов клиента. При создании сертификатов клиента с помощью приведенных ниже инструкций сертификат клиента автоматически устанавливается на компьютер, который использовался для его создания. Если вы хотите установить сертификат клиента на другой клиентский компьютер, его можно экспортировать.

В примерах используется командлет New-SelfSignedCertificate для создания сертификата клиента, срок действия которого истекает через год. Дополнительные сведения о параметре, например о задании другого значения срока действия сертификата клиента, см. в разделе [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Пример 1

В этом примере используется переменная $cert, объявленная в предыдущем разделе. Если вы закрыли консоль PowerShell после создания самозаверяющего корневого сертификата или создаете дополнительные сертификаты клиента в новом сеансе консоли PowerShell, выполните инструкции, описанные в [примере 2](#ex2).

Измените и запустите пример, чтобы создать сертификат клиента. Если выполнить этот пример, не изменив его, то будет создан сертификат клиента P2SChildCert.  Если требуется указать другое имя дочернего сертификата, измените значение CN. Не изменяйте TextExtension при выполнении данного примера. Сертификат клиента, который создается, автоматически устанавливается в папку Certificates - Current User\Personal\Certificates на компьютере.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
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
  New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="cer"></a>3. Экспорт открытого ключа корневого сертификата (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]


### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Экспорт самозаверяющего корневого сертификата и закрытого ключа для его сохранения (необязательно)

Может возникнуть необходимость экспортировать самозаверяющий корневой сертификат и сохранить его как резервную копию в надежном месте. При необходимости позже можно будет установить его на другом компьютере и создать дополнительные сертификаты клиента. Чтобы экспортировать самозаверяющий корневой сертификат в формате PFX, выберите корневой сертификат и выполните те же действия, что описаны в разделе [Экспорт сертификата клиента](#clientexport).

## <a name="clientexport"></a>4. Экспорт сертификата клиента

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]


## <a name="install"></a>5. Установка экспортированного сертификата клиента

Для каждого клиента, который подключается к виртуальной сети через подключение "точка — сеть", сертификат должен быть установлен локально.

См. инструкции по [установке сертификата клиента для подключений типа "точка — сеть"](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="install"></a>6. Переход к настройке параметров подключения "точка — сеть"

Продолжайте настраивать параметры конфигурации типа "точка-сеть".

* При использовании модели развертывания на основе **Resource Manager** см. инструкции по [настройке подключения типа "точка — сеть" с использованием собственной аутентификации Azure на основе сертификата](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Инструкции для **классической** модели развертывания см. в статье [Настройка подключения типа "точка — сеть" к виртуальной сети с помощью портала Azure (классическая модель)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).
* Дополнительные сведения см. в руководстве по [устранению неполадок подключения типа "точка — сеть" в Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
