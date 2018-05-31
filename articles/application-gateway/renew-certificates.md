---
title: Обновление сертификата шлюза приложений Azure
description: Узнайте, как обновить сертификат, связанный с прослушивателем шлюза приложений.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/17/2018
ms.author: victorh
ms.openlocfilehash: b125f707e8de17764701e981736a53492e5e756c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356959"
---
# <a name="renew-application-gateway-certificates"></a>Обновление сертификатов шлюза приложений

Если вы настроили в шлюзе приложений SSL-шифрование, рано или поздно вам придется обновить сертификаты.

Сертификат, связанный с прослушивателем, можно обновить с помощью портала Azure или Azure PowerShell.

## <a name="azure-portal"></a>Портал Azure

Чтобы обновить сертификат прослушивателя на портале, перейдите к прослушивателям шлюза приложений. Щелкните прослушиватель, сертификат которого необходимо обновить, и выберите **Продлить или изменить выбранный сертификат**.

![Обновление сертификата](media/renew-certificate/ssl-cert.png)

Передайте новый сертификат в формате PFX, присвойте ему имя, введите пароль и нажмите кнопку **Сохранить**.

## <a name="azure-powershell"></a>Azure PowerShell

Чтобы обновить сертификат с помощью Azure PowerShell, используйте следующий командлет:

```PowerShell
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-azureRmApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password
```

## <a name="next-steps"></a>Дополнительная информация

Сведения по настройке разгрузки SSL с использованием шлюза приложений Azure см. в статье [Настройка шлюза приложений для разгрузки SSL с помощью портала](application-gateway-ssl-portal.md)
