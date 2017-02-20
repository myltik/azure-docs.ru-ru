---
title: "Копирование больших двоичных объектов из учетной записи хранения в файл служб мультимедиа Microsoft Azure | Документация Майкрософт"
description: "В этом разделе показано, как копировать существующий большой двоичный объект в ресурс служб мультимедиа. В примере кода используются расширения пакета SDK служб мультимедиа Azure для .NET."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6a63823f-f3c9-424c-91b8-566f70bec346
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/31/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 197bf0f0d95422f5bd696a8e9b0abc799f2951fc
ms.openlocfilehash: f71adf1f12e2be0c63465eff7d61a35d3256d4f2


---
# <a name="copying-existing-blobs-into-a-media-services-asset"></a>Копирование существующих BLOB-объектов в файл служб мультимедиа
В этом разделе показано, как копировать большие двоичные объекты из учетной записи хранения в файл служб мультимедиа Microsoft Azure с помощью [расширений пакета SDK служб мультимедиа Azure для .NET](https://github.com/Azure/azure-sdk-for-media-services-extensions/).

Этот метод расширения работает со следующими компонентами:

- обычные файлы;
- файлы динамического архива (в формате FragBlob);
- исходные и целевые файлы, принадлежащие к разным учетным записям служб мультимедиа (или даже расположенные в разных центрах обработки данных). Но за это может взиматься плата. Дополнительные сведения о ценах см. [здесь](https://azure.microsoft.com/pricing/#header-11).

> [!NOTE]
> Не следует пытаться изменить содержимое контейнеров больших двоичных объектов, созданных службами мультимедиа, без использования интерфейсов API служб мультимедиа.
> 

## <a name="download-sample"></a>Скачивание образца
Можно выполнить действия, описанные в этой статье, или просто [скачать](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/) пример с используемым в этой статье кодом.

## <a name="prerequisites"></a>Предварительные требования
* Две учетные записи служб мультимедиа в новой или существующей подписке Azure. Дополнительные сведения см. в статье [Создание учетной записи служб мультимедиа Azure с помощью портала Azure](media-services-portal-create-account.md).
* Операционные системы: Windows 10, Windows 7, Windows 2008 R2 или Windows 8.
* .NET Framework 4.5
* Visual Studio 2010 SP1 (Professional, Premium, Ultimate или Express) или более поздняя версия.

## <a name="set-up-your-project"></a>Настройка проекта
В этом разделе вы создадите и настроите проект консольного приложения на C#.

1. Чтобы создать новое решение, содержащее проект консольного приложения на C#, используйте Visual Studio. 
2. В поле "Имя" введите CopyExistingBlobsIntoAsset и нажмите кнопку "ОК".
3. Используйте пакет [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices.extensions), чтобы установить расширения пакета SDK служб мультимедиа Azure для .NET. При установке этого пакета также устанавливается пакет SDK .NET служб мультимедиа и добавляются все остальные необходимые зависимости.
4. Добавьте другие ссылки, необходимые для этого проекта: System.Configuration.
6. Добавьте раздел appSettings в файл CONFIG и обновите значения в соответствии со значениями учетных записей служб мультимедиа и хранилища, а также идентификатора исходного файла. 
   
        <appSettings>
              <add key="MediaServicesSourceAccountName" value="name"/>
              <add key="MediaServicesSourceAccountKey" value="key"/>
              <add key="MediaServicesDestAccountName" value="name"/>
              <add key="MediaServicesDestAccountKey" value="key"/>
              <add key="DestStorageAccountName" value="name"/>
              <add key="DestStorageAccountKey" value="key"/>
              <add key="SourceAssetID" value="nb:cid:UUID:assetID"/>       
        </appSettings>

## <a name="copy-blobs-from-a-storage-account-into-a-media-services-asset"></a>Копирование BLOB-объектов из учетной записи хранения в файл служб мультимедиа

В следующем коде используется метод расширения **IAsset.Copy** для копирования всех файлов из исходного ресурса (файла) в целевой с помощью одного расширения. Обратите внимание на дополнительные перегрузки, связанные с асинхронной поддержкой.

Замените код в файле Program.cs следующим кодом.

    using System;
    using System.Linq;
    using System.Configuration;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.Storage.Auth;
    
    namespace CopyExistingBlobsIntoAsset
    {
        class Program
        {
            static string _sourceAaccountName = ConfigurationManager.AppSettings["MediaServicesSourceAccountName"];
            static string _sourceAccountKey = ConfigurationManager.AppSettings["MediaServicesSourceAccountKey"];
            static string _destAccountName = ConfigurationManager.AppSettings["MediaServicesDestAccountName"];
            static string _destAccountKey = ConfigurationManager.AppSettings["MediaServicesDestAccountKey"];
            static string _destStorageAccountName = ConfigurationManager.AppSettings["DestStorageAccountName"];
            static string _destStorageAccountKey = ConfigurationManager.AppSettings["DestStorageAccountKey"];
            static string _sourceAssetID = ConfigurationManager.AppSettings["SourceAssetID"];
            
    
            private static CloudMediaContext _sourceContext = null;
            private static CloudMediaContext _destContext = null;
    
            static void Main(string[] args)
            {
                // Create the context for your source Media Services account.
    
                // Use the cached credentials to create CloudMediaContext.
                _sourceContext = new CloudMediaContext(new MediaServicesCredentials(
                    _sourceAaccountName,
                    _sourceAccountKey));

                // Create the context for your destination Media Services account.
                _destContext = new CloudMediaContext(new MediaServicesCredentials(
                    _destAccountName,
                    _destAccountKey));
    
                // Get the credentials of the default Storage account bound to your destination Media Services account.
                StorageCredentials destinationStorageCredentials =
                    new StorageCredentials(_destStorageAccountName, _destStorageAccountKey);
    
                // Get a reference to the source asset in the source context.
                IAsset sourceAsset = _sourceContext.Assets.Where(a => a.Id == _sourceAssetID).First();
    
                // Create an empty destination asset in the destination context.
                IAsset destinationAsset = _destContext.Assets.Create(sourceAsset.Name, AssetCreationOptions.None);
    
                // Copy the files in the source asset instance into the destination asset instance.
                // There is an additional overload with async support.
                sourceAsset.Copy(destinationAsset, destinationStorageCredentials);

                
            }
        }
    }


## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO1-->


