---
title: Использование PowerShell для создания сертификатов X.509 | Документация Майкрософт
description: Использование PowerShell для создания сертификатов X.509 локально и включения защиты на основе сертификатов X.509 в Центре Интернета вещей Azure в моделируемой среде.
services: iot-hub
documentationcenter: ''
author: dsk-2015
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: dkshir
ms.openlocfilehash: 656799c76a87870a19018849dbeffea3b12a356e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>Использование сценариев PowerShell для управления сертификатами X.509, подписанными ЦС

Для обеспечения безопасности на основе сертификатов X.509 в Центре Интернета вещей вам понадобится [цепочка сертификатов X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), которая включает корневой сертификат, а также любые промежуточные сертификаты вплоть до конечного. Это *руководство* посвящено примерам сценариев PowerShell, использующих [OpenSSL](https://www.openssl.org/) для создания и подписывания сертификатов X.509. Мы рекомендуем использовать это руководство только для экспериментов, так как множество из этих шагов будут выполняться во время производственного процесса на практике. Эти сертификаты можно использовать для имитации безопасности в Центре Интернета вещей Azure с помощью *аутентификации на основе сертификата X.509*. При выполнении действий из этого руководства сертификаты создаются локально на компьютере Windows. 

## <a name="prerequisites"></a>предварительным требованиям
В этом руководстве предполагается, что вы получили двоичные файлы OpenSSL. Вы можете выбрать один из следующих вариантов:
    - скачать исходный кода OpenSSL и создать двоичные файлы на компьютере; 
    - скачать и установить любые [сторонние двоичные файлы OpenSS ](https://wiki.openssl.org/index.php/Binaries), например из [этого проекта на SourceForge](https://sourceforge.net/projects/openssl/).

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>Создание сертификатов X.509
Далее показан пример создания корневых сертификатов X.509 локально. 

1. Откройте окно PowerShell от имени *администратора*.  
   **Примечание.** Откройте его в PowerShell самостоятельно без использования интегрированной среды сценариев PowerShell, Visual Studio Code или других средств, которые являются оболочкой для базовой консоли PowerShell.  Если использовать PowerShell не на основе консоли, приведенные ниже команды `openssl` зависнут.

2. Перейдите в рабочий каталог. Запустите следующий сценарий, чтобы задать глобальные переменные. 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertCommonName      = "Azure IoT Root CA"
    $_rootCertSubject         = "CN=$_rootCertCommonName"
    $_intermediateCertSubject = "Azure IoT Intermediate {0} CA"
    $_privateKeyPassword      = "123"

    $rootCACerFileName          = "./RootCA.cer"
    $rootCAPemFileName          = "./RootCA.pem"
    $intermediate1CAPemFileName = "./Intermediate1.pem"
    $intermediate2CAPemFileName = "./Intermediate2.pem"
    $intermediate3CAPemFileName = "./Intermediate3.pem"

    $openSSLBinDir              = Join-Path $ENV:TEMP "openssl-bin"

    # Whether to use ECC or RSA.
    $useEcc                     = $true
    ```
3. Выполните следующий сценарий, который копирует двоичные файлы OpenSSL в рабочий каталог и устанавливает переменные среды:

    ```PowerShell
    function Initialize-CAOpenSSL()
    {
        Write-Host ("Beginning copy of openssl binaries to {0} (and setting up env variables...)" -f $openSSLBinDir)
        if (-not (Test-Path $openSSLBinDir))
        {
            mkdir $openSSLBinDir | Out-Null
        }

        robocopy $openSSLBinSource $openSSLBinDir * /s 
        robocopy $openSSLBinSource . * /s 

        Write-Host "Setting up PATH and other environment variables."
        $ENV:PATH += "; $openSSLBinDir"
        $ENV:OPENSSL_CONF = Join-Path $openSSLBinDir "openssl.cnf"

        Write-Host "Success"
    }
    Initialize-CAOpenSSL
    ```
4. Затем выполните следующий сценарий, который проверяет, установлен ли сертификат, по *имени субъекта* и правильно ли настроен OpenSSL на компьютере:
    ```PowerShell
    function Get-CACertBySubjectName([string]$subjectName)
    {
        $certificates = gci -Recurse Cert:\LocalMachine\ |? { $_.gettype().name -eq "X509Certificate2" }
        $cert = $certificates |? { $_.subject -eq $subjectName -and $_.PSParentPath -eq "Microsoft.PowerShell.Security\Certificate::LocalMachine\My" }
        if ($NULL -eq $cert)
        {
            throw ("Unable to find certificate with subjectName {0}" -f $subjectName)
        }
    
        write $cert
    }
    function Test-CAPrerequisites()
    {
        $certInstalled = $null
        try
        {
            $certInstalled = Get-CACertBySubjectName $_rootCertSubject
        }
        catch {}

        if ($NULL -ne $certInstalled)
        {
            throw ("Certificate {0} already installed.  Cleanup CA certs 1st" -f $_rootCertSubject)
        }

        if ($NULL -eq $ENV:OPENSSL_CONF)
        {
            throw ("OpenSSL not configured on this system.  Run 'Initialize-CAOpenSSL' (even if you've already done so) to set everything up.")
        }
        Write-Host "Success"
    }
    Test-CAPrerequisites
    ```
    Если все настроено правильно, отобразится сообщение "Успешно". 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>Создание цепочки сертификатов X.509
Создайте цепочку сертификатов с помощью корневого ЦС, например CN=Azure IoT Root CA, используемого в этом примере, выполнив следующий сценарий PowerShell. Этот сценарий также обновляет хранилище сертификатов ОС Windows, а также создает файлы сертификатов в локальной рабочей папке. 
    1. Следующий сценарий создает функцию PowerShell для создания самозаверяющего сертификата для заданного *имени субъекта* и заверителя подписи. 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$commonName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$commonName; 
                           "-CertStoreLocation"="cert:\LocalMachine\My";
                           "-NotAfter"=(get-date).AddDays(30); 
                          }

        if ($isASigner -eq $true)
        {
            $selfSignedArgs += @{"-KeyUsage"="CertSign"; }
            $selfSignedArgs += @{"-TextExtension"= @(("2.5.29.19={text}ca=TRUE&pathlength=12")); }
        }
        else
        {
            $selfSignedArgs += @{"-TextExtension"= @("2.5.29.37={text}1.3.6.1.5.5.7.3.2,1.3.6.1.5.5.7.3.1", "2.5.29.19={text}ca=FALSE&pathlength=0")  }
        }

        if ($signingCert -ne $null)
        {
            $selfSignedArgs += @{"-Signer"=$signingCert }
        }

        if ($useEcc -eq $true)
        {
            $selfSignedArgs += @{"-KeyAlgorithm"="ECDSA_nistP256";
                             "-CurveExport"="CurveName" }
        }

        # Now use splatting to process this
        Write-Host ("Generating certificate {0} which is for prototyping, NOT PRODUCTION.  It will expire in 30 days." -f $subjectName)
        write (New-SelfSignedCertificate @selfSignedArgs)
    }
    ``` 
    2. Следующая функция PowerShell создает промежуточные сертификаты X.509, использующие предыдущую функцию, а также двоичные файлы OpenSSL. 
    ```PowerShell
    function New-CAIntermediateCert([string]$commonName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($commonName + ".cer")
        $newCert = New-CASelfsignedCertificate $commonName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. Следующая функция PowerShell создает цепочку сертификатов X.509. Дополнительные сведения см. в разделе [Certificate chains and cross-certification](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification) (Цепочка сертификатов и перекрестная сертификация)
    ```PowerShell
    function New-CACertChain()
    {
        Write-Host "Beginning to install certificate chain to your LocalMachine\My store"
        $rootCACert =  New-CASelfsignedCertificate $_rootCertSubject $null
    
        Export-Certificate -Cert $rootCACert -FilePath $rootCACerFileName  -Type CERT
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\Root" -FilePath $rootCACerFileName

        openssl x509 -inform der -in $rootCACerFileName -out $rootCAPemFileName

        $intermediateCert1 = New-CAIntermediateCert ($_intermediateCertSubject -f "1") $rootCACert $intermediate1CAPemFileName
        $intermediateCert2 = New-CAIntermediateCert ($_intermediateCertSubject -f "2") $intermediateCert1 $intermediate2CAPemFileName
        $intermediateCert3 = New-CAIntermediateCert ($_intermediateCertSubject -f "3") $intermediateCert2 $intermediate3CAPemFileName
        Write-Host "Success"
    }    
    ```
    Этот сценарий создает файл с именем *RootCA.cer* в рабочей папке. 
    4. Наконец, используйте функции PowerShell, приведенные выше, для создания цепочки сертификатов X.509, выполнив команду `New-CACertChain` в окне PowerShell. 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>Проверка принадлежности сертификата ЦС X.509

Сценарий выполняет последовательность *проверки принадлежности* для сертификата X.509. 

В окне PowerShell на рабочем столе выполните следующий код:
   
   ```PowerShell
   function New-CAVerificationCert([string]$requestedSubjectName)
   {
       $verifyRequestedFileName = ".\verifyCert4.cer"
       $rootCACert = Get-CACertBySubjectName $_rootCertSubject
       Write-Host "Using Signing Cert:::" 
       Write-Host $rootCACert
   
       $verifyCert = New-CASelfsignedCertificate $requestedSubjectName $rootCACert $false

       Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
       if (-not (Test-Path $verifyRequestedFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
       }
   
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $requestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
   }
   New-CAVerificationCert "<your verification code>"
   ```

Этот код создает в рабочей папке подписанный ЦС сертификат с указанным именем субъекта в качестве файла с именем *VerifyCert4.cer*. Этот файл сертификата поможет проверить наличие разрешения на подпись (то есть закрытый ключ) этого ЦС с помощью Центра Интернета вещей.


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>Создание конечного сертификата X.509 для устройства

В этом разделе показано, как можно использовать сценарий PowerShell, создающий конечный сертификат устройства и соответствующую цепочку сертификатов. 

В окне PowerShell на локальном компьютере выполните следующий сценарий, чтобы создать сертификат X.509, подписанный ЦС для этого устройства:

   ```PowerShell
   function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
   {
       $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
       $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
       $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
       $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
   
       $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

       $newDeviceCertPfx = New-CASelfSignedCertificate $deviceName $signingCert $false
   
       $certSecureStringPwd = ConvertTo-SecureString -String $_privateKeyPassword -Force -AsPlainText

       # Export the PFX of the cert we've just created.  The PFX is a format that contains both public and private keys.
       Export-PFXCertificate -cert $newDeviceCertPfx -filePath $newDevicePfxFileName -password $certSecureStringPwd
       if (-not (Test-Path $newDevicePfxFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $newDevicePfxFileName)
       }

       # Begin the massaging.  First, turn the PFX into a PEM file which contains public key, private key, and other attributes.
       Write-Host ("When prompted for password by openssl, enter the password as {0}" -f $_privateKeyPassword)
       openssl pkcs12 -in $newDevicePfxFileName -out $newDevicePemAllFileName -nodes

       # Convert the PEM to get formats we can process
       if ($useEcc -eq $true)
       {
           openssl ec -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       else
       {
           openssl rsa -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       openssl x509 -in $newDevicePemAllFileName -out $newDevicePemPublicFileName
 
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnNewDeviceSubjectName, (Join-Path (get-location).path $newDevicePemPublicFileName)) 
   }
   ```

Затем запустите `New-CADevice "<yourTestDevice>"` в окне PowerShell, используя понятное имя, которое применялось для создания устройства. При появлении запроса на ввод пароля для закрытого ключа ЦС введите 123. При этом в рабочей папке создается файл _<yourTestDevice>PFX_.

## <a name="clean-up-certificates"></a>Очистка сертификатов

На панели запуска или в приложении **Параметры** найдите и выберите элемент **Управление сертификатами компьютеров**. Удалите все сертификаты, выданные **ЦС Azure IoT TestOnly***. Эти сертификаты должны находиться в следующих трех расположениях: 

* "Сертификаты — локальный компьютер" > "Личные" > "Сертификаты";
* "Сертификаты — локальный компьютер" > "Доверенные корневые центры сертификации" > "Сертификаты";
* "Сертификаты — локальный компьютер" > Intermediate Certificate Authorities (Промежуточные центры сертификации) > "Сертификаты".

   ![Удаление сертификатов ЦС Azure IoT TestOnly](./media/iot-hub-security-x509-create-certificates/cleanup.png)
