<properties linkid="manage-linux-common-tasks-manage-certs" urlDisplayName="Manage certificates" pageTitle="Manage certificates for Linux virtual machines in Azure" metaKeywords="Azure management certs, uploading management certs, Azure Service Management API" description="Learn how to create and upload management certificates for Linux in Azure. The certificate is required if you use the Service Management API." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create management certificates for Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Создание сертификатов управления для Linux в Azure

Сертификат управления необходим, если вы собираетесь использовать API-интерфейс управления службой для взаимодействия с платформой образов Azure.

Документацию по созданию этих сертификатов и управлению ими см. по адресу [][]<http://msdn.microsoft.com/ru-ru/library/azure/gg981929.aspx></a>. Можно также использовать OpenSSL для создания сертификата управления. Дополнительные сведения см. на сайте [OpenSSL][OpenSSL]. Однако в этой документации основное внимание уделяется использованию портала Silverlight, который может быть доступен не всем пользователям Linux. В ней рассказывается, как можно получить доступ к этим сертификатам и интегрировать их с другими средствами и партнерами, а также использовать их самостоятельно, пока эта функция не будет добавлена на портал управления Azure.

## Оглавление

-   [Получение сертификата управления из файла publishsettings][Получение сертификата управления из файла publishsettings]
-   [Установка сертификата управления с помощью портала управления Azure][Установка сертификата управления с помощью портала управления Azure]

## <span id="publishsettings"></span></a>Практическое руководство. Создание и отправка сертификата управления

Мы предлагаем простой способ создания сертификата управления для Azure по следующему адресу: . [][1]<https://windows.azure.com/download/publishprofile.aspx></a>

На этот веб-сайт необходимо войти с использованием учетных данных портала, а затем создать сертификат управления, поставляемый вместе с вашим идентификатором подписки в файле publishsettings, который будет предложено загрузить.

![linuxcredentials][linuxcredentials]

Обязательно сохраните этот файл в безопасном месте, поскольку вы не сможете его восстановить и будете вынуждены создать новый сертификат управления. (Общее количество сертификатов, которые можно использовать в системе, ограничено. Чтобы убедиться в этом, см. соответствующий раздел на веб-сайте.) Затем вы можете использовать сертификат разными способами.

### В Visual Studio

![VSpublish][VSpublish]

### В командной строке Azure для Linux.

Вы можете импортировать сертификат, чтобы использовать его с помощью команды импорта учетной записи Azure:

![cmdlinepublish][cmdlinepublish]

Если вам потребуется это средство при работе с другим партнером или программным обеспечением, вы должны будете извлечь сертификат управления из самого файла и декодировать его из Base 64. Некоторые партнеры, например ScaleXtreme и SUSE Studio, будут использовать файл непосредственно в его текущей форме.

Чтобы извлечь сертификат управления, необходимо выполнить описанную здесь процедуру.

Необходимо извлечь из файла содержимое в кодировке base 64, расположенное между кавычками после ManagementCertificate.

    ?xml version="1.0" encoding="utf-8"?>
    <PublishData>
      <PublishProfile
        PublishMethod="AzureServiceManagementAPI"
        Url="https://management.core.windows.net/"
        ManagementCertificate="xxxxx">
        <Subscription
          Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
          Name="hjereztest" />
      </PublishProfile>
    </PublishData>

Это нужно будет скопировать в файл (например, в encodedCert.txt), а затем декодировать его с помощью декодера base64:

    base64 -d [encodedCert.txt] > [decodedCert.pfx]

При этом вы получите PFX-файл, который можно преобразовать в другие форматы, используя при необходимости openssl для извлечения закрытого ключа.

    openssl pkcs12 -in [decodedCert.pfx] -out [cert.pem] -nodes

В Windows можно декодировать и извлечь PFX-файл с помощью PowerShell или бесплатного декодера base 64 Windows, такого как <http://www.fourmilab.ch/webtools/base64/base64.zip></a>, выполнив следующую команду:

    base64 -d key.txt ->key.pfx

#### Ограничение на создание сертификатов

С помощью этого средства можно создать на платформе не более 10 сертификатов.
К сожалению, не существует способа восстановления созданных ключей после их создания, поскольку мы не храним эти закрытые ключи в системе.
При достижении ограничения на количество сертификатов в системе необходимо обратиться в службу поддержки через форумы Azure, чтобы удалить ваши сертификаты, или использовать браузер, который может отображать старый портал Silverlight для выполнения этих задач.

#### Если ваш закрытый ключ взломан

В случае взлома закрытого ключа необходимо с помощью браузера, поддерживающего Silverlight, зайти на старый портал и удалить соответствующие сертификаты управления в файле или обратиться в службу поддержки через форумы.
Создания новых сертификатов недостаточно, поскольку все 10 сертификатов действительны, и старый взломанный ключ по-прежнему может использоваться для доступа к веб-сайту.

## <span id="management"></span></a>Установка сертификата управления с помощью портала управления Azure

Сертификат управления можно создать различными способами. Дополнительные сведения о создании сертификатов см. в разделе [Создание сертификата управления в Azure][Создание сертификата управления в Azure]. После создания сертификата добавьте его в свою подписку в Azure.

1.  Выполните вход на портал управления Azure.

2.  В области навигации щелкните пункт **Параметры**.

3.  В разделе **Сертификаты управления** выберите команду **Отправить сертификат управления**.

4.  В окне **отправки сертификата управления** перейдите к файлу сертификата и нажмите кнопку **ОК**.

### Получение отпечатка сертификата и идентификатора подписки

Для передачи VHD-файла в Azure вам необходимы отпечаток добавленного сертификата управления и идентификатор подписки.

1.  На портале управления щелкните **Параметры**.

2.  В разделе **Сертификаты управления** щелкните ваш сертификат, а затем запишите отпечаток с панели **Свойства**, скопировав и вставив его туда, откуда его можно извлечь позднее.

Для передачи VHD-файла необходим также идентификатор подписки.

1.  На портале управления щелкните **Все элементы**.

2.  В центральной области в разделе **Подписка** скопируйте подписку и вставьте ее туда, откуда ее можно извлечь позднее.

### Передача этих сведений в средства, если создан собственный ключ

#### Для CSUPLOAD

1.  Откройте окно командной строки Azure SDK как администратор.
2.  Задайте строку подключения с помощью следующей команды, заменив **Subscriptionid** и **CertThumbprint** полученными ранее значениями:

        csupload Set-Connection "SubscriptionID=<Subscriptionid>;CertificateThumbprint=<Thumbprint>;ServiceManagementEndpoint=https://management.core.windows.net"

#### Для программ командной строки Linux в Azure

PFX-файл, созданный с использованием openssl, необходимо преобразовать в кодировку base 64 с помощью команды:

        Base64 [file] > [econded file]

Затем идентификатор подписки и PFX-файл в кодировке base64 необходимо объединить в один файл, имеющий следующую структуру:

        ?xml version="1.0" encoding="utf-8"?>
        <PublishData>
          <PublishProfile
            PublishMethod="AzureServiceManagementAPI"
            Url="https://management.core.windows.net/"
            ManagementCertificate="xxxxx">
            <Subscription
              Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
              Name="hjereztest" />
          </PublishProfile>
        </PublishData>
        

Здесь xxxxx — содержимое файла [enconded file], который будет использоваться для передачи сведений в средство командной строки Linux в Azure с помощью команд:
Azure account import (File)

  []: http://msdn.microsoft.com/ru-ru/library/azure/gg981929.aspx
  [OpenSSL]: http://openssl.org/
  [Получение сертификата управления из файла publishsettings]: #createcert
  [Установка сертификата управления с помощью портала управления Azure]: #management
  [1]: https://windows.azure.com/download/publishprofile.aspx
  [linuxcredentials]: ./media/virtual-machines-linux-create-management-cert/linuxcredentials.png
  [VSpublish]: ./media/virtual-machines-linux-create-management-cert/VSpublish.png
  [cmdlinepublish]: ./media/virtual-machines-linux-create-management-cert/cmdlinepublish.png

  [Создание сертификата управления в Azure]: http://msdn.microsoft.com/library/azure/gg551722.aspx
