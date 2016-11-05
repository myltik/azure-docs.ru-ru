---
title: Отправка образов в хранилище BLOB-объектов Azure из универсального приложения для Windows | Microsoft Docs
description: Узнайте, как использовать серверную мобильную службу .NET для отправки образов в хранилище больших двоичных объектов Azure и доступа к ним из универсального приложения для Windows. приложения Windows.
documentationcenter: windows
author: ggailey777
services: mobile-services,storage
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Отправка образов в хранилище Azure с помощью мобильных служб
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[!INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

## Обзор
В этом разделе показано, как использовать мобильные службы Azure, чтобы передавать и сохранять созданные изображения в хранилище Azure через свое приложение. Для хранения данных мобильные службы используют базу данных SQL. Однако данные больших двоичных объектов (BLOB) более эффективно хранить в службе хранения больших двоичных объектов Azure.

Невозможно безопасно передать в клиентском приложении учетные данные, необходимые для безопасной передачи данных в службу хранения BLOB-объектов. Вместо этого необходимо сохранить эти учетные данные в мобильной службе и использовать их для создания подписи общего доступа (SAS), которая используется для передачи нового изображения. Подпись общего доступа является типом учетных данных с быстрым окончанием срока действия, в данном случае это 5 минут. Она безопасно возвращается мобильными службами в клиентское приложение. Затем приложение использует эти временные учетные данные для передачи изображения. В этом примере файлы, загружаемые из службы BLOB-объектов, являются общедоступными.

В этом учебнике вы добавите некоторые функциональные возможности к учебному приложению мобильных служб для съемки фотографий и передачи изображений в Azure с помощью учетных данных SAS, созданных мобильными службами.

## Предварительные требования
Для работы с данным учебником требуется следующее:

* Microsoft Visual Studio 2013 с обновлением 3 или более поздней версии
* [Учетная запись хранения Azure](../storage/storage-create-storage-account.md)
* Камера или другое устройство для съемки, подключенное к компьютеру.

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами].

[!INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

[!INCLUDE [mobile-services-windows-universal-dotnet-upload-to-blob-storage](../../includes/mobile-services-windows-universal-dotnet-upload-to-blob-storage.md)]

## Дальнейшие действия
Теперь, когда вы смогли безопасно передать изображения, интегрировав мобильную службу и службу BLOB-объектов, ознакомьтесь с некоторыми темами, посвященными серверным службам и интеграции:

* [Планирование серверных заданий в мобильных службах](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
  
     Узнайте, как использовать планировщик заданий мобильных служб для определения кода сценария на стороне сервера, который будет выполняться в соответствии с заданным расписанием.
* [Справочник принципов использования мобильных служб .NET](mobile-services-dotnet-how-to-use-client-library.md)
  
     Дополнительные сведения об использовании мобильных служб с .NET

<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Install the storage client in the mobile service project]: #storage-client-server
[Update the TodoItem definition in the data model]: #update-data-model
[Update the table controller to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Приступая к работе с мобильными службами]: mobile-services-windows-store-dotnet-get-started.md
[How To Create a Storage Account]: ../storage/storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866

<!---HONumber=AcomDC_0727_2016-->