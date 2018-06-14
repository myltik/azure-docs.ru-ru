---
title: Использование отправленного SSL-сертификата в коде приложения службы приложений Azure | Документация Майкрософт
description: ''
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cephalin
ms.openlocfilehash: 6800bf766deb2044d400f92dbe370fa15bdd5f00
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2017
ms.locfileid: "26047605"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Использование SSL-сертификата в коде приложения службы приложений Azure

Это руководство продемонстрирует, как использовать в коде приложения SSL-сертификаты, отправленные или импортированные в приложение службы приложений. Это будет удобно, если приложение обращается к внешней службе, для которой требуется аутентификация на основе сертификата. 

Подход с использованием SSL-сертификатов в коде основывается на поддержке SSL в службе приложений, а значит ваше приложение должно быть размещено на уровне **Базовый** или выше. Есть и другой вариант — включить файл сертификата в каталог приложения и загрузить его напрямую (см. раздел [Альтернативный вариант. Загрузка сертификата в виде файла](#file)). Но этот вариант не позволяет скрывать закрытый ключ сертификата от кода и разработчика приложения. Более того, если код приложения размещен в репозитории с открытым исходным кодом, хранение в репозитории сертификата с закрытым ключом недопустимо.

Доверив управление SSL-сертификатами службе приложений, вы сможете разделить сертификаты и код приложения, защитив таким образом конфиденциальные данные.

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством должны быть выполнены следующие условия.

- [Создано приложение службы приложений](/azure/app-service/).
- [С вашим веб-приложением сопоставлено настраиваемое DNS-имя](app-service-web-tutorial-custom-domain.md).
- В веб-приложение [отправлен SSL-сертификат](app-service-web-tutorial-custom-ssl.md) или [импортирован сертификат службы приложений](web-sites-purchase-ssl-web-site.md)


## <a name="load-your-certificates"></a>Загрузка сертификатов

Чтобы использовать сертификат, загруженный или импортированный в службу приложений, сделайте его доступным для кода приложения. Для этого применяется параметр приложения `WEBSITE_LOAD_CERTIFICATES`.

Откройте страницу своего веб-приложения на <a href="https://portal.azure.com" target="_blank">портале Azure</a>.

В области навигации слева щелкните **SSL-сертификаты**.

![Сертификат добавлен](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

Здесь показаны все SSL-сертификаты, загруженные или импортированные для этого веб-приложения, и соответствующие отпечатки. Скопируйте отпечаток того сертификата, который вы хотите использовать.

На панели навигации слева щелкните **Параметры приложения**.

Добавьте параметр приложения с именем `WEBSITE_LOAD_CERTIFICATES` и присвойте ему значение отпечатка сертификата. Чтобы предоставить доступ к нескольким сертификатам сразу, укажите несколько значений отпечатков, разделив их запятыми. Чтобы сделать доступными все сертификаты, установите здесь значение `*`. 

![Настройка параметров приложения](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

По завершении щелкните **Сохранить**.

Итак, сертификат настроен, и его можно использовать в коде приложения.

## <a name="use-certificate-in-c-code"></a>Использование сертификата в коде C#

Когда сертификат будет доступен, к нему можно обращаться в коде C# через отпечаток сертификата. Следующий код загружает сертификат с отпечатком `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="alternative-load-certificate-as-a-file"></a>Альтернативный вариант. Загрузка сертификата в виде файла

В этом разделе показано, как загрузить файл сертификата, размещенный в каталоге приложения. 

Следующий пример кода на C# загружает сертификат с именем `mycert.pfx` из каталога `certs` в репозитории вашего приложения.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

