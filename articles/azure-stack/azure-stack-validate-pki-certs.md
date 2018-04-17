---
title: Проверка сертификатов инфраструктуры открытых ключей Azure Stack для развертывания интегрированных систем Azure Stack | Документация Майкрософт
description: В этой статье описано, как проверить сертификаты PKI Azure Stack для интегрированных систем Azure Stack.
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
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 0bdadadb1f4ee5f76cde9d05b11e8d57b99ac191
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>Проверка сертификатов PKI Azure Stack

Средство проверки сертификатов Azure Stack, описанное в этой статье, предоставляется изготовителем оборудования вместе с файлом deploymentdata.json. Оно проверяет, что [созданные сертификаты PKI](azure-stack-get-pki-certs.md) подходят для подготовки к развертыванию. Стадия проверки сертификатов требует некоторого времени. Может даже понадобиться получить новые сертификаты.

Средство проверки сертификатов (Certchecker) выполняет следующие проверки.

- **Чтение PFX-файла**. Проверяет наличие допустимого PFX-файла и правильность пароля, а также выдает предупреждения, если общедоступная информация не защищена паролем. 
- **Алгоритм подписи**. Проверяет, не используется ли алгоритм подписи SHA1.
- **Закрытый ключ**. Проверяет наличие закрытого ключа и то, экспортируется ли он с помощью атрибута локального компьютера. 
- **Цепочка сертификатов**. Проверяет наличие и целостность цепочки сертификатов, включая самозаверяющиеся сертификаты. 
- **DNS-имена**. Проверяет, имеются ли в сети хранения данных соответствующие DNS-имена для каждой конечной точки или присутствует ли вспомогательный подстановочный знак. 
- **Использование ключа**. Проверяет, применяется ли цифровая подпись и шифрование при использовании ключа, а при расширенном использовании ключа проверяется аутентификация сервера и клиента.
- **Размер ключа**. Проверяет, достигает ли размер ключа значения 2048.
- **Порядок в цепочке**. Проверяет порядок других сертификатов, чтобы убедиться, что цепочка правильная.
- **Другие сертификаты**. Проверяет, не были ли упакованы в PFX-файл другие сертификаты (кроме соответствующего конечного сертификата и его цепочки).
- **Отсутствие профиля**. Проверяет, может ли новый пользователь скачать PFX-файл без загрузки профиля пользователя, что аналогично поведению групповых управляемых учетных записей службы во время обслуживания сертификатов.

> [!IMPORTANT]  
> PFX-файл и пароль сертификата PKI должны быть конфиденциальными.

## <a name="prerequisites"></a>предварительным требованиям
Прежде чем начать проверку сертификатов PKI для развертывания Azure Stack, необходимо убедиться, что в системе присутствуют следующие компоненты и установлена нужная ОС.
- Служба CertChecker (в **PartnerToolKit** в каталоге **\utils\certchecker**)
- Сертификаты SSL, экспортированные в соответствии с [инструкциями по подготовке](azure-stack-prepare-pki-certs.md).
- Файл DeploymentData.json.
- Windows 10 или Windows Server 2016;

## <a name="perform-certificate-validation"></a>Выполнение проверки сертификатов

Для подготовки и проверки сертификатов PKI Azure Stack сделайте следующее: 

1. Извлеките содержимое каталога <partnerToolkit>\utils\certchecker в новый каталог, например **c:\certchecker**.

2. Войдите в PowerShell от имени администратора и перейдите из каталога в папку certchecker.

  ```powershell
  cd c:\certchecker
  ```
 
3. Создайте структуру каталогов для сертификатов, выполнив следующие команды PowerShell.

  ```powershell 
  $directories = "ACS","ADFS","Admin Portal","ARM Admin","ARM Public","Graph","KeyVault","KeyVaultInternal","Public Portal" 
  $destination = '.\certs' 
  $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
  ```

  >  [!NOTE]
  >  Если для развертывания Azure Stack используется поставщик удостоверений Azure AD, удалите каталоги **ADFS** и **Graph**. 

4. Поместите сертификаты в соответствующие каталоги, созданные на предыдущем шаге, например: 
  - c:\certchecker\Certs\ACS\CustomerCertificate.pfx,  
  - c:\certchecker\Certs\Admin Portal\CustomerCertificate.pfx,  
  - c:\certchecker\Certs\ARM Admin\CustomerCertificate.pfx  
  - и т. д. 

5. Скопируйте файл **deploymentdata.json** в каталог **c:\certchecker**.

6. В окне PowerShell выполните следующие команды. 

  ```powershell
  $password = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
  .\CertChecker.ps1 -CertificatePath .\Certs\ -pfxPassword $password -deploymentDataJSONPath .\DeploymentData.json  
  ```

7. В выходных данных рядом с каждым сертификатом и атрибутом должно быть значение "OK". 

  ```powershell
  Starting Azure Stack Certificate Validation 1.1802.221.1
  Testing: ADFS\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: KeyVaultInternal\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: ACS\ContosoSSL.pfx
  WARNING: Pre-1803 certificate structure. The folder structure for Azure Stack 1803 and above is: ACSBlob, ACSQueue, ACSTable instead of ACS folder. Refer to deployment documentation for further informat
  ion.
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Detailed log can be found C:\CertChecker\CertChecker.log 
  ```

### <a name="known-issues"></a>Известные проблемы 
**Признак**. Certchecker завершает работу преждевременно и отображается следующая ошибка. 
> Сбой

> Подробности. Эту команду нельзя выполнять из-за ошибки The directory name is invalid. (Имя каталога недопустимо). 

**Причина**. Файл certchecker.ps1 запущен из запрещенной папки (например, c:\temp или %temp%). 

**Решение**. Переместите средство certchecker в новый каталог (например, C:\CertChecker). 


**Признак**. Certchecker выдает предупреждение об использовании сертификата до версии 1803 (как в приведенном выше примере из шага 7).

> [!WARNING]
> Структура сертификата до версии 1803. Структура папки для Azure Stack 1803 и более поздних версий — ACSBlob, ACSQueue, ACSTable вместо папки ACS. Дополнительные сведения см. в документации по развертыванию.

**Причина**. В CertChecker обнаружено использование одной папки ACS (это подходит для развертываний с помощью версий до 1803). В развертываниях Azure Stack версии 1803 и выше структура папок изменяется на ACSTable, ACSQueue, ACSBlob. В Certchecker уже включена поддержка этих функциональных возможностей.

**Решение**. Если развертывание выполняется с помощью версии 1802, никаких действий не требуется. Если выполняется развертывание с помощью версии 1803 и более поздних, замените ACS на ACSTable, ACSQueue, ACSBlob и скопируйте сертификаты ACS в эти папки.

**Признак**. Проверки пропускаются.

**Причина**. CertChecker пропускает определенные проверки, если не установлена зависимость.
- Проверка других сертификатов пропускается, если цепочка сертификатов нецелостна.
- Проверка на отсутствие профиля пропускается в следующих случаях.
  - Установлена политика безопасности, ограничивающая возможность создания временных пользователей и запуска PowerShell от имени этого пользователя.
  - Проверка на наличие закрытого ключа завершается неудачно.

**Решение**. Следуйте указаниям в руководстве по инструментам в разделе сведений о каждом наборе проверок сертификата.


## <a name="prepare-deployment-script-certificates"></a>Подготовка сертификатов для скрипта развертывания 
На последнем этапе поместите все подготовленные сертификаты в соответствующие каталоги на узле развертывания. Там же создайте папку с именем Certificates** и поместите экспортированные файлы сертификатов в соответствующие вложенные папки, указанные в разделе [Обязательные сертификаты](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).

```
\Certificates
\ACS\ssl.pfx
\Admin Portal\ssl.pfx
\ARM Admin\ssl.pfx
\ARM Public\ssl.pfx
\KeyVault\ssl.pfx
\KeyVaultInternal\ssl.pfx
\Public Portal\ssl.pfx
\ADFS\ssl.pfx*
\Graph\ssl.pfx*
```

<sup>*</sup> Сертификаты, отмеченные знаком *, необходимы только, когда в качестве хранилища идентификаторов используются службы федерации Active Directory (AD FS).


## <a name="next-steps"></a>Дополнительная информация
[Интеграция центра обработки данных Azure Stack: идентификация](azure-stack-integrate-identity.md)
