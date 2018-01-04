---
title: "Защита содержимого HLS в Azure с помощью Apple FairPlay в автономном режиме | Документация Майкрософт"
description: "В этом разделе приводятся общие сведения и показывается, как использовать службы мультимедиа Azure для динамического шифрования содержимого HTTP Live Streaming (HLS) с использованием Apple FairPlay в автономном режиме."
services: media-services
keywords: "HLS, DRM, потоковая передача FairPlay (FPS), Offline, iOS 10"
documentationcenter: 
author: willzhan
manager: steveng
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: b68ceac2056f0a9a7a9c4df7984789858c77a626
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
---
# <a name="offline-fairplay-streaming"></a>Потоковая передача FairPlay в автономном режиме
Службы мультимедиа Microsoft Azure предоставляют набор хорошо спроектированных [служб системы защиты содержимого](https://azure.microsoft.com/services/media-services/content-protection/), которые охватывают:
- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- шифрование AES-128.

Шифрование содержимого DRM или AES выполняется динамически по запросу для различных протоколов потоковой передачи. Службы доставки ключей расшифровки AES и лицензии DRM также предоставляются службами мультимедиа Azure.

Помимо защиты содержимого для интерактивной потоковой передачи по различным протоколам потоковой передачи также часто запрашивается автономный режим для защищенного содержимого. Поддержка автономного режима необходима в следующих случаях:
1. Воспроизведение, когда подключение к Интернету недоступно, например во время путешествий.
2. Некоторые поставщики содержимого могут запретить доставку лицензии DRM за пределы страны. Если пользователь хочет смотреть содержимое во время поездок за границу, необходимо автономное скачивание.
3. В некоторых странах доступность Интернета и пропускная способность по-прежнему ограничены. Пользователи могут сначала скачать содержимое, чтобы иметь возможность смотреть его в достаточно высоком разрешении для удобного просмотра. В этом случае чаще всего проблема заключается не в доступности сети, а в ее ограниченной пропускной способности. Поставщики OTT/OVP запрашивают поддержку автономного режима.

В этой статье рассматривается поддержка автономного режима потоковой передачи FairPlay, предназначенная для устройств под управлением iOS 10 или более поздней версии. Эта функция не поддерживается для других платформ Apple, таких как watchOS, tvOS или Safari на macOS.

## <a name="preliminary-steps"></a>Предварительные действия
Перед реализацией автономного DRM для FairPlay на устройстве iOS 10+ вам потребуется:
1. Ознакомиться с интерактивной защитой содержимого FairPlay. Это подробно рассматривается в следующих статьях и примерах:
- [Apple FairPlay Streaming for Azure Media Services generally available](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/) (Общедоступная версия Apple FairPlay Streaming для служб мультимедиа Azure).
- [Защита содержимого HLS с помощью Apple FairPlay или Microsoft PlayReady](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay).
- [Образец для интерактивной потоковой передачи FPS](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/).
2. Получите пакет SDK FPS из сети разработчиков Apple. Пакет SDK FPS состоит из двух компонентов:
- Пакет SDK сервера FPS, который содержит KSM (модуль безопасности ключа), образцы клиентов, спецификацию и набор тестовых векторов.
- Пакет развертывания FPS, который содержит D Function, спецификации вместе с инструкциями о том, как создать сертификат FPS, пользовательский закрытый ключ и секретный код приложения (ASK). Apple выдает пакет развертывания FPS только лицензированным поставщикам содержимого.

## <a name="configuration-in-azure-media-services"></a>Конфигурация в службах мультимедиа Azure
Для конфигурации автономного режима FPS с помощью [пакета SDK служб мультимедиа для .NET](https://www.nuget.org/packages/windowsazure.mediaservices) следует использовать пакет SDK служб мультимедиа для .NET версии 4.0.0.4 или более поздней, который предоставляет API, необходимый для настройки автономного режима FPS.
Как указано выше, предполагается, что у вас есть рабочий код для настройки защиты содержимого FPS в оперативном режиме. После получения кода для настройки защиты содержимого в оперативном режиме для FPS вам понадобятся только следующие два изменения.

## <a name="code-change-in-fairplay-configuration"></a>Изменение кода в конфигурации FairPlay
Давайте определим логическое значение для включения автономного режима, objDRMSettings.EnableOfflineMode, которое является истинным при включении автономного сценария DRM. В зависимости от этого индикатора мы делаем следующее изменение конфигурации FairPlay:

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-asset-delivery-policy-configuration"></a>Изменение кода в конфигурации политики доставки ресурсов
Второе изменение — добавить третий ключ в словарь Dictionary<AssetDeliveryPolicyConfigurationKey, string>.
Третий ключ AssetDeliveryPolicyConfigurationKey необходимо добавить, как показано ниже: 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

После выполнения этого шага словарь Dictionary<AssetDeliveryPolicyConfigurationKey, string> в политике доставки ресурсов FPS будет содержать следующие три записи:
1. AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl или AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl в зависимости от таких факторов, как используемый FPS KSM или сервер ключей, и хотим ли мы повторно использовать одну и ту же политику доставки ресурсов в нескольких ресурсах.
2. AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs.
3. AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense.

Теперь ваша учетная запись мультимедийных служб настроена на предоставление автономных лицензий FairPlay.

## <a name="sample-ios-player"></a>Пример проигрывателя iOS
Во-первых, следует отметить, что поддержка режима автономной работы FPS доступна только в iOS 10 и более поздних версиях. Следует получить пакет SDK сервера FPS (версии 3.0 или более поздней), содержащий документ и пример автономного режима FPS. В частности, пакет SDK сервера FPS (версии 3.0 или более поздней версии) содержит следующие два элемента, связанные с автономным режимом:
1. Документ: автономное воспроизведение с помощью потоковой передачи FairPlay и HTTP Live Streaming. Apple, 14.9.2016. В пакете SDK сервера FPS версии 4.0 этот документ объединен с основным документом потоковой передачи FPS.
2. Пример кода: образец HLSCatalog для автономного режима FPS в \FairPlay Streaming Server SDK v3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. В примере приложения HLSCatalog следующие файлы кода предназначены, в частности, для реализации функций автономного режима:
- Файл кода AssetPersistenceManager.swift: AssetPersistenceManager — это основной класс, в этом примере, демонстрирующий:
    - Как управлять загрузкой потоков HLS, такими как API для запуска и отмены скачивания, удаления существующих активов с устройства пользователя.
    - Как отслеживать ход выполнения скачивания.
- AssetListTableViewController.swift и AssetListTableViewCell.swift файлов с кодом: AssetListTableViewController — это основной интерфейс этого примера. Он предоставляет список ресурсов, которые пример может воспроизводить, скачивать, удалять, а также загрузку которых он может отменять. 

Ниже приведены подробные инструкции по настройке выполняющегося плеера iOS. Предположим, вы начинаете с примера HLSCatalog в пакете SDK сервера FPS версии 4.0.1.  Необходимо внести следующие изменения в коде:

В HLSCatalog\Shared\Managers\ContentKeyDelegate.swift реализуйте метод `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` следующим образом: пусть drmUr будет переменной, назначенной URL-адресу потоковой передачи HLS.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

В HLSCatalog\Shared\Managers\ContentKeyDelegate.swift реализуйте метод `requestApplicationCertificate()`. Эта реализация зависит от того, внедряете ли вы сертификат (только открытый ключ) в устройство или размещаете сертификат в Интернете. Ниже приведена реализация с использованием сертификата размещенного приложения, используемого в наших тестовых примерах. Пусть certUrl будет переменной, которая содержит URL-адрес сертификата приложения.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

Для окончательного интегрированного теста URL-адрес видео и сертификата приложения будут указаны в разделе "Интегрированный тест".

В HLSCatalog\Shared\Resources\Streams.plist добавьте URL-адрес тестового видео, а для ключа идентификатора содержимого можно просто использовать URL-адрес для получения лицензии FairPlay с протоколом skd в качестве уникального значения.

![Автономные потоки FairPlay приложения iOS](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Для URL-адреса тестового видео, URL-адреса для получения лицензий FairPlay и URL-адреса сертификата приложения вы можете использовать собственные, если вы их настроили, или можете перейти к следующему разделу, содержащему тестовые примеры.

## <a name="integrated-test"></a>Интегрированный тест
В службах мультимедиа Azure созданы три тестовых образца, которые охватывают следующие три сценария:
1.  Защита FPS с видео, аудио и альтернативной звуковой дорожкой.
2.  Защита FPS с видео, аудио, но без альтернативной звуковой дорожки.
3.  Защита FPS с видео, но без аудио.

Эти примеры можно найти на этом [демо-сайте](http://aka.ms/poc#22) вместе с соответствующим сертификатом приложения, размещенным в веб-приложении Azure.
Мы заметили, что при использовании 3-й или 4-й версии примера пакета SDK сервера FPS, если главный плейлист содержит альтернативный аудио-поток, в автономном режиме воспроизводится только аудио. Таким образом, нам нужно убрать альтернативный аудио-поток. Другими словами, среди трех вышеприведенных примеров второй и третий работают как в интерактивном, так и в автономном режиме. Но в первом примере звук будет воспроизводиться только в автономном режиме, в то время как оперативная потоковая передача работает нормально.

## <a name="faq"></a>Часто задаваемые вопросы
Часто задаваемые вопросы по устранению неполадок:
- **Почему в автономном режиме воспроизводится только звук, но нет видео?** Такое поведение, по-видимому, связано с дизайном примера приложения. Когда присутствует альтернативная звуковая дорожка (что относится к HLS), в автономном режиме, как в iOS 10, так и в iOS 11 будет по умолчанию воспроизводиться альтернативная звуковая дорожка. Чтобы компенсировать это поведение в автономном режиме FPS, нам необходимо удалить альтернативную звуковую дорожку из потока. Чтобы сделать это на стороне служб мультимедиа Azure, мы можем просто добавить динамический фильтр манифеста audio-only=false. Другими словами URL-адрес HLS должен оканчиваться на .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Почему после добавления audio-only=false в автономном режиме по-прежнему воспроизводится аудио без видео?** В зависимости от структуры ключа кэша CDN содержимое может кэшироваться. Необходимо очистить кэш.
- **Режим автономной работы FPS также поддерживается на iOS 11 в дополнение к iOS 10?** Да, автономный режим FPS поддерживается как в iOS 10, так и в iOS 11.
- **Почему я не могу найти документ автономного воспроизведения с помощью потоковой передачи FairPlay и HTTP Live Streaming в пакете SDK сервера FPS?**. Начиная с 4-й версии пакета SDK сервера FPS, этот документ был объединен с документом руководства по потоковому программированию FairPlay.
- **Что означает последний параметр в следующем API для автономного режима FPS?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

Документацию по этому API можно найти на сайте [здесь](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). Параметр представляет продолжительность автономной аренды с часом в качестве единицы измерения.
- **Какова структура скачанного или автономного файла на устройствах iOS?** Структура скачанного файла на устройстве iOS выглядит как на снимке экрана ниже. В папке `_keys` хранятся скачанные лицензии FPS, один файл хранилища для каждого узла службы лицензий. В папке `.movpkg` хранится аудио- и видеосодержимое. Первая папка, имя которой заканчивается дефисом с числами, содержит видео. Числовое значение равно значению PeakBandwidth для представлений видео. Вторая папка с именем, заканчивающимся тире, за которым следует 0, содержит аудио. Третья папка с именем "Данные" содержит главный список воспроизведения содержимого FPS. В файле Boot.xml представлено полное описание содержимого папки `.movpkg` (см. пример файла boot.xml ниже).

![Структура файла примера приложения автономного FairPlay iOS](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Пример файла boot.xml:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="summary"></a>Сводка
В этом документе мы предоставили подробные шаги и сведения для реализации автономного режима FPS, в том числе:
1. Конфигурация защиты содержимого служб мультимедиа Azure через API .NET AMS. Таким образом можно настроить динамическое шифрование FairPlay и доставку лицензии FairPlay в AMS.
2. Проигрыватель iOS на основе примера пакета SDK сервера FPS от Apple. При этом настраивается проигрыватель iOS, который может воспроизводить содержимое FPS либо в режиме оперативной потоковой передачи, либо в автономном режиме.
3. Примеры видео FPS для тестирования как автономного, так и оперативного режима потоковой передачи.
4. Часто задаваемые вопросы об автономном режиме FPS.
