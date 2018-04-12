---
title: Создание сертификатов инфраструктуры открытых ключей Azure Stack для развертывания интегрированных систем Azure Stack | Документация Майкрософт
description: В этой статье описано, как развернуть сертификаты PKI Azure Stack для интегрированных систем Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: fc2ec96113310f54d32a67ea5fa31725600046c9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="generate-pki-certificates-for-azure-stack-deployment"></a>Создание сертификатов PKI для развертывания Azure Stack
Теперь, когда вы знаете [требования к сертификату PKI](azure-stack-pki-certs.md) для развертываний Azure Stack, вам необходимо получить эти сертификаты из выбранного вами центра сертификации. 

## <a name="request-certificates-using-an-inf-file"></a>Создание запросов на сертификаты с помощью INF-файла
С помощью INF-файла можно запросить сертификаты из общедоступного или внутреннего центра сертификации. Встроенная программа Windows certreq.exe может с помощью INF-файла указать сведения сертификата для создания файла запроса, как описано в этом разделе. 

### <a name="sample-inf-file"></a>Пример INF-файла 
Пример INF-файла для запроса на сертификат можно использовать для создания автономного файла запроса для отправки в центр сертификации (внутренний или общедоступный). INF-файл собирает все необходимые конечные точки (в том числе дополнительные службы PaaS) в единый групповой сертификат. 

В примере INF-файла предполагается, что регион равен **sea**, а значение внешнего полного доменного имени — **sea.contoso.com**. Замените эти значения значениями своей среды, прежде чем создавать INF-файл для развертывания. 

    
    [Version] 
    Signature="$Windows NT$"

    [NewRequest] 
    Subject = "C=US, O=Microsoft, L=Redmond, ST=Washington, CN=portal.sea.contoso.com"

    Exportable = TRUE                   ; Private key is not exportable 
    KeyLength = 2048                    ; Common key sizes: 512, 1024, 2048, 4096, 8192, 16384 
    KeySpec = 1                         ; AT_KEYEXCHANGE 
    KeyUsage = 0xA0                     ; Digital Signature, Key Encipherment 
    MachineKeySet = True                ; The key belongs to the local computer account 
    ProviderName = "Microsoft RSA SChannel Cryptographic Provider" 
    ProviderType = 12 
    SMIME = FALSE 
    RequestType = PKCS10
    HashAlgorithm = SHA256

    ; At least certreq.exe shipping with Windows Vista/Server 2008 is required to interpret the [Strings] and [Extensions] sections below

    [Strings] 
    szOID_SUBJECT_ALT_NAME2 = "2.5.29.17" 
    szOID_ENHANCED_KEY_USAGE = "2.5.29.37" 
    szOID_PKIX_KP_SERVER_AUTH = "1.3.6.1.5.5.7.3.1" 
    szOID_PKIX_KP_CLIENT_AUTH = "1.3.6.1.5.5.7.3.2"

    [Extensions] 
    %szOID_SUBJECT_ALT_NAME2% = "{text}dns=*.sea.contoso.com&dns=*.blob.sea.contoso.com&dns=*.queue.sea.contoso.com&dns=*.table.sea.contoso.com&dns=*.vault.sea.contoso.com&dns=*.adminvault.sea.contoso.com&dns=*.dbadapter.sea.contoso.com&dns=*.appservice.sea.contoso.com&dns=*.scm.appservice.sea.contoso.com&dns=api.appservice.sea.contoso.com&dns=ftp.appservice.sea.contoso.com&dns=sso.appservice.sea.contoso.com&dns=adminportal.sea.contoso.com&dns=management.sea.contoso.com&dns=adminmanagement.sea.contoso.com" 
    %szOID_ENHANCED_KEY_USAGE% = "{text}%szOID_PKIX_KP_SERVER_AUTH%,%szOID_PKIX_KP_CLIENT_AUTH%"

    [RequestAttributes]
    

## <a name="generate-and-submit-request-to-the-ca"></a>Создание запроса и его отправка в центр сертификации
Следующий рабочий процесс описывает, как настроить и использовать пример INF-файла, созданный ранее, для создания запроса на сертификат из центра сертификации:

1. **Изменение и сохранение INF-файла.** Скопируйте пример файла и сохраните его в новом текстовом файле. Замените значения имени субъекта и внешнего доменного имени значениями, которые соответствуют развертыванию, и сохраните файл как INF-файл.
2. **Создание запроса с помощью программы certreq.** На компьютере Windows откройте командную строку от имени администратора и запустите следующую команду, чтобы создать файл запроса (REQ): `certreq -new <yourinffile>.inf <yourreqfilename>.req`.
3. **Отправка в центр сертификации.** Отправьте созданный REQ-файл в центр сертификации (внутренний или общедоступный).
4. **Импорт CER-файла.** Центр сертификации возвращает CER-файл. На том же компьютере Windows, где вы создали файл запроса, импортируйте возвращенный CER-файл в личное хранилище или хранилище компьютера. 
5. **Экспорт и копирование PFX-файла в папки развертывания.** Экспортируйте сертификат (в том числе закрытый ключ) как PFX-файл и скопируйте этот файл в папки развертывания, описанные в [требованиях к PKI для развертывания Azure Stack](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Дополнительная информация
[Подготовка сертификатов PKI Azure Stack](azure-stack-prepare-pki-certs.md)
