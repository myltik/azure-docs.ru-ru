---
title: Проверка сертификатов инфраструктуры открытых ключей Azure Stack для развертывания интегрированных систем Azure Stack | Документация Майкрософт
description: В этой статье описано, как проверить сертификаты PKI Azure Stack для интегрированных систем Azure Stack. В статье описывается использование средства проверки сертификатов Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cd917165804314f6ee4ee006e3f29263d8d4b4c5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>Проверка сертификатов PKI Azure Stack

Инструмент проверки готовности Azure Stack, описанный в этой статье, доступен в [коллекции PowerShell](https://aka.ms/AzsReadinessChecker). Вы можете использовать этот инструмент, чтобы проверить, что [сгенерированные сертификаты PKI](azure-stack-get-pki-certs.md) подходят для предварительного развертывания. Вы должны проверить сертификаты, оставив достаточно времени для проверки и повторной выдачи сертификатов при необходимости.

Средство проверки готовности выполняет следующие проверки сертификата:

- **Чтение PFX-файла**.  
    Проверяет наличие допустимого PFX-файла и правильность пароля, а также выдает предупреждения, если общедоступная информация не защищена паролем. 
- **Алгоритм подписи**.  
    Проверяет, не используется ли алгоритм подписи SHA1.
- **Закрытый ключ**.  
    Проверяет наличие закрытого ключа и то, экспортируется ли он с помощью атрибута локального компьютера. 
- **Цепочка сертификатов**.  
    Проверяет наличие и целостность цепочки сертификатов, включая самозаверяющиеся сертификаты.
- **DNS-имена**.  
    Проверяет, имеются ли в сети хранения данных соответствующие DNS-имена для каждой конечной точки или присутствует ли вспомогательный подстановочный знак.
- **Использование ключа**.  
    Проверяет, применяется ли цифровая подпись и шифрование при использовании ключа, а при расширенном использовании ключа проверяется аутентификация сервера и клиента.
- **Размер ключа**.  
    Проверяет, достигает ли размер ключа значения 2048.
- **Порядок в цепочке**.  
    Проверяет порядок других сертификатов, чтобы убедиться, что порядок правильный.
- **Другие сертификаты**.  
    Проверяет, не были ли упакованы в PFX-файл другие сертификаты (кроме соответствующего конечного сертификата и его цепочки).

> [!IMPORTANT]  
> PFX-файл и пароль сертификата PKI должны быть конфиденциальными.

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем начать проверку сертификатов PKI для развертывания Azure Stack, необходимо убедиться, что в системе присутствуют следующие компоненты и установлена нужная ОС.

- Инструмент проверки готовности Microsoft Azure Stack
- Сертификаты SSL, экспортированные в соответствии с [инструкциями по подготовке](azure-stack-prepare-pki-certs.md).
- Файл DeploymentData.json.
- Windows 10 или Windows Server 2016;

## <a name="perform-certificate-validation"></a>Выполнение проверки сертификатов

Для подготовки и проверки сертификатов PKI Azure Stack выполните следующие действия:

1. Установите AzsReadinessChecker из командной строки PowerShell (версии 5.1 или более поздней), выполнив следующий командлет:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker 
    ````

2. Создайте структуру каталога сертификатов. В приведенном ниже примере вы можете изменить `<c:\certificates>` на новый путь каталога по своему усмотрению.

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal' 

    $destination = 'c:\certificates' 

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
    ````

 - Поместите сертификаты в соответствующие каталоги, созданные на предыдущем шаге. Например:   
    - c:\certificates\ACSBlob\CustomerCertificate.pfx 
    - c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx 
    - c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx 
    - и т. д. 

3. В окне PowerShell выполните команду ниже:

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ````

4. Просмотрите выходные данные, чтобы проверить, что все сертификаты прошли тесты. Например: 

    ````PowerShell
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Validation

    Starting Azure Stack Certificate Validation 1.1803.405.3
    Testing: ARM Public\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ACSBlob\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessReport.json
    AzsReadinessChecker Completed
    ````

### <a name="known-issues"></a>Известные проблемы

**Признак**. Проверки пропускаются.

**Причина**. AzsReadinessChecker пропускает определенные проверки, если не установлена зависимость.

 - Проверка других сертификатов пропускается, если цепочка сертификатов нецелостна.

    ````PowerShell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

**Решение**. Следуйте указаниям в руководстве по инструментам в разделе сведений о каждом наборе проверок сертификата.

## <a name="using-validated-certificates"></a>Использование проверенных сертификатов

Как только ваши сертификаты будут проверены с помощью AzsReadinessChecker, их можно использовать в развертывании Azure Stack или для смены секретов Azure Stack. 

 - В целях развертывания безопасно передайте свои сертификаты специалисту по развертыванию, чтобы он смог скопировать их на узел развертывания, как указано в [документации по требованиям PKI для Azure Stack](azure-stack-pki-certs.md).
 - В целях смены секретов вы можете использовать сертификаты, чтобы обновить старые сертификаты для общедоступных конечных точек инфраструктуры среды Azure Stack, следуя инструкциям, приведенным в статье [Смена секретов в Azure Stack](azure-stack-rotate-secrets.md).

## <a name="next-steps"></a>Дополнительная информация

[Интеграция центра обработки данных Azure Stack: идентификация](azure-stack-integrate-identity.md)
