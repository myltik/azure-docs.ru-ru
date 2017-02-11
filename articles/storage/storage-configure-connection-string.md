---
title: "Настройка строки подключения к службе хранилища Azure | Документация Майкрософт"
description: "Настройка строки подключения к учетной записи хранения Azure. Строка подключения содержит сведения, необходимые для проверки подлинности при доступе к учетной записи хранения из приложения во время выполнения."
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: ecb0acb5-90a9-4eb2-93e6-e9860eda5e53
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 1a3c754bf2f2b73d0bf72cbf48b906d8085eaef1
ms.openlocfilehash: f4410c10ce66d50b64307e364e64a3367b9397f9


---
# <a name="configure-azure-storage-connection-strings"></a>Настройка строк подключения службы хранилища Azure
## <a name="overview"></a>Обзор
Строка подключения содержит сведения для проверки подлинности, необходимые для доступа к данным в учетной записи хранения из приложения во время выполнения. Можно настроить строку подключения, чтобы сделать следующее.

* подключиться к эмулятору хранилища Azure;
* получить доступ к учетной записи хранения в Azure;
* получить доступ к указанным ресурсам Azure через подписанный URL-адрес (SAS).

[!INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Хранение строки подключения
Приложению потребуется получать доступ к строке подключения во время выполнения, чтобы проверять подлинность запросов к службе хранилища Azure. Есть несколько вариантов для хранения строки подключения.

* Для приложения, работающего на рабочем столе или на устройстве, можно хранить строки подключения в файле **app.config** или **web.config**. Добавьте строку подключения в раздел **AppSettings** .
* Для приложения, работающего в облачной службе Azure, можно хранить строки подключения в [файле схемы конфигурации службы Azure (.cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Добавьте строку подключения в раздел **ConfigurationSettings** файла конфигурации службы.
* Также можно использовать строку подключения непосредственно в коде. Однако в большинстве случаев рекомендуется хранить строки подключения в файле конфигурации.

Хранение строки подключения в файле конфигурации упрощает обновление строки подключения для переключения между эмулятором хранения и учетной записью хранения Azure в облаке. Вам потребуется только изменить строку подключения, чтобы указать целевую среду.

Вы можете использовать класс [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) для доступа к строке подключения во время выполнения независимо от того, где выполняется приложение.

## <a name="create-a-connection-string-to-the-storage-emulator"></a>Создание строки подключения в эмуляторе хранения
[!INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Дополнительные сведения об эмуляторе хранения см. в разделе [Использование эмулятора хранения Azure для разработки и тестирования](storage-use-emulator.md).

## <a name="create-a-connection-string-to-an-azure-storage-account"></a>Создание строки подключения к учетной записи хранения Azure
Чтобы создать строку подключения к учетной записи хранения Azure, используйте формат строки подключения, указанный ниже. Укажите, следует ли подключиться к учетной записи хранения с помощью протокола HTTPS (рекомендуется) или HTTP, задайте вместо параметра *myAccountName* имя учетной записи хранения, а вместо параметра *myAccountKey* — свой ключ доступа к учетной записи.

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Созданная строка подключения будет выглядеть так, как строка из следующего примера:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

> [!NOTE]
> Служба хранилища Azure поддерживает как HTTP, так и HTTPS в строке подключения; однако настоятельно рекомендуется использовать HTTPS.
>
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Создание строки подключения с помощью подписанного URL-адреса
[!INCLUDE [storage-use-sas-in-connection-string-include](../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="creating-a-connection-string-to-an-explicit-storage-endpoint"></a>Создание строки подключения к явной конечной точке хранилища
Вы можете явно указать конечные точки службы в строке подключения вместо конечных точек по умолчанию. Чтобы создать строку подключения, определяющую явную конечную точку, укажите полную конечную точку для каждой службы, включая спецификацию протокола (HTTPS (рекомендуется) или HTTP), в следующем формате:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Один из сценариев, когда может потребоваться указать явную конечную точку, — если конечная точка хранилища BLOB-объектов сопоставлена с личным доменом. В этом случае в строке подключения можно указать пользовательскую конечную точку хранилища BLOB-объектов и дополнительно указать конечные точки по умолчанию для других служб, если приложение их использует.

Ниже приведены примеры допустимых строк подключения, в которых указывается явная конечная точка для службы BLOB-объектов:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=www.mydomain.com;
AccountName=storagesample;
AccountKey=account-key

# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=www.mydomain.com;
FileEndpoint=myaccount.file.core.windows.net;
QueueEndpoint=myaccount.queue.core.windows.net;
TableEndpoint=myaccount;
AccountName=storagesample;
AccountKey=account-key
```

Значение конечной точки из строки подключения применяется для создания универсальных кодов ресурса (URI) к службе BLOB-объектов, а также определяет форму любых универсальных кодов ресурса (URI), возвращаемых в код.

Обратите внимание, что, если вы решили не указывать конечную точку в строке подключения, вы не сможете получить доступ к данным в этой службе с помощью этой строки подключения.

### <a name="creating-a-connection-string-with-an-endpoint-suffix"></a>Создание строки подключения с суффиксом конечной точки
Чтобы создать строку подключения для службы хранилища в регионах или экземплярах с разными суффиксами конечных точек, например Azure China (Azure для Китая) или Azure Governance (Azure для правительственных организаций), используйте следующий формат строки подключения. Укажите, следует ли подключиться к учетной записи хранения с помощью протокола HTTP или HTTPS, задайте вместо параметра *myAccountName* имя учетной записи хранения, вместо параметра *myAccountKey* — свой ключ доступа к учетной записи, затем замените *mySuffix* суффиксом универсального кода ресурса (URI).

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Созданная строка подключения должна выглядеть так, как строка из следующего примера:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Анализ строки подключения
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
* [Использование эмулятора хранения Azure для разработки и тестирования](storage-use-emulator.md)
* [Обучающие ресурсы для хранилища Azure](storage-explorers.md)
* [Использование подписанных URL-адресов (SAS)](storage-dotnet-shared-access-signature-part-1.md)




<!--HONumber=Dec16_HO2-->


