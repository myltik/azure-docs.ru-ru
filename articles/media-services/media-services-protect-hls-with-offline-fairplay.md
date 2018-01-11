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
ms.openlocfilehash: 15f6d422f3171ae5161e0d4d4bcd8ec98529c766
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="offline-fairplay-streaming"></a>Потоковая передача FairPlay в автономном режиме
 Службы мультимедиа Azure предоставляет набор хорошо спроектированной [содержимого служб защиты](https://azure.microsoft.com/services/media-services/content-protection/) , титульных:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- шифрование AES-128.

Управление цифровыми правами (DRM) / Advanced Encryption Standard (AES) шифрование содержимого динамически выполняется по запросу для различных протоколов потоковой передачи. Службы доставки ключей расшифровки лицензии или AES DRM также предоставляются службами мультимедиа.

Помимо защиты содержимого для интерактивной потоковой передачи по различным протоколам потоковой передачи также часто запрашивается автономный режим для защищенного содержимого. Поддержка автономного режима, необходимо в следующих случаях:

* Воспроизведение, когда подключение к Интернету недоступно, например во время движения.
* Некоторые поставщики содержимого могут запретить доставки лицензии DRM за пределы страны границы. Если пользователям нужно просматривать содержимое дороге за пределами страны, автономные загрузки требуется.
* В некоторых странах услуг Интернета или полосы пропускания по-прежнему ограничена. Пользователи могут выбрать для загрузки сначала, чтобы иметь возможность просматривать содержимое в разрешении, достаточно большое для удовлетворительные обозреваемых объектов. В этом случае проблему обычно не доступности сети, но ограниченной пропускной способности сети. Over--top (OTT) / online видео платформы (OVP) поставщиков запроса поддержки автономный режим.

В этой статье рассматриваются поддержки автономный режим FairPlay потоковой передачи (кадров/с), предназначенного для устройств под управлением iOS, 10 или более поздней версии. Эта функция не поддерживается для других Apple платформ, таких как watchOS, tvOS или Safari на macOS.

## <a name="preliminary-steps"></a>Предварительные действия
При реализации автономного DRM FairPlay на устройстве iOS 10 +:

* Ознакомиться с интерактивной защитой содержимого FairPlay. Дополнительные сведения см. следующие статьи и образцы.

    - [Потоковая передача FairPlay Apple для служб мультимедиа Azure находится в общем доступе](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Защита содержимого HLS с помощью Apple FairPlay или Microsoft PlayReady](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay).
    - [Образец для интерактивной потоковой передачи FPS](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/).

* Получите пакет SDK кадров в Секунду из сети разработчика Apple. Число кадров в Секунду SDK состоит из двух компонентов:

    - Число кадров в Секунду сервера пакета SDK, который содержит ключ безопасности модуля (KSM), образцы клиента, спецификацию и набор тестов векторов.
    - Пакет развертывания кадров в Секунду, содержащий спецификацию функции D вместе с инструкциями о том, как создать сертификат кадров/с, закрытый ключ заказчика и секретный ключ приложения. Apple выдает пакет развертывания кадров в Секунду только Лицензированные поставщиков содержимого.

## <a name="configuration-in-media-services"></a>Конфигурации в службах мультимедиа
Для конфигурации автономный режим кадров в Секунду через [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices), используйте Media Services .NET SDK версии 4.0.0.4 или более поздней версии, который предоставляет API, необходимые для настройки автономного режима кадров/с.
Также необходим работающий код для настройки защиты содержимого оперативном режиме кадров в Секунду. Получив код, чтобы настроить защиту содержимого оперативном режиме для кадров в Секунду, необходимо только следующие два изменения.

## <a name="code-change-in-the-fairplay-configuration"></a>Изменение кода в конфигурации FairPlay
Первое изменение состоит в определении «включить автономный режим» значение типа Boolean, вызывается objDRMSettings.EnableOfflineMode, которое равно true, если она позволяет DRM в случае автономной работы. В зависимости от того, этот индикатор внесите следующие изменения в конфигурацию FairPlay:

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

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Изменение кода в настройке политики доставки активов
Второе изменение — добавить третий ключ в словаре < AssetDeliveryPolicyConfigurationKey строки >.
Добавьте AssetDeliveryPolicyConfigurationKey, как показано ниже:
 
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

После выполнения этого шага строка < Dictionary_AssetDeliveryPolicyConfigurationKey > в политики доставки активов кадров в Секунду, содержит следующие три записи:

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl или AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl в зависимости от таких факторов, как KSM кадров/с-ключ сервера, используемого в и можно повторно использовать же доставки активов политики на нескольких активов
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs.
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense.

Теперь для доставки автономной лицензии FairPlay настраивается учетную запись служб мультимедиа.

## <a name="sample-ios-player"></a>Пример проигрывателя iOS
Число кадров в Секунду автономный режим поддерживается только для iOS 10 и более поздней версии. Пакет SDK сервера кадров/с (версия 3.0 или более поздней версии) содержит документ и образец для автономного режима кадров в Секунду. В частности пакет SDK для сервера кадров/с (версия 3.0 или более поздней версии) содержит следующие два элемента, связанные в автономный режим:

* Документ: «автономного воспроизведения с FairPlay Streaming и HTTP Live потоковой передачи.» Apple 14 сентября 2016 г. В версии 4.0 Server SDK кадров в Секунду в этом документе объединяется в основном документе кадров/с.
* Пример кода: образец HLSCatalog для автономного режима кадров в Секунду в 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ версии \FairPlay SDK сервера потоковой передачи. В примере приложения HLSCatalog следующие файлы кода используются для реализации возможностей в автономном режиме:

    - Файл кода AssetPersistenceManager.swift: AssetPersistenceManager — основной класс, в этот пример, демонстрирующий, как:

        - Управление загрузка потоков HLS, такие, как использовать API-интерфейсы для запуска и отмены загрузки и удаления существующих ресурсов, отключение устройств.
        - Отслеживать ход выполнения загрузки.
    - AssetListTableViewController.swift и AssetListTableViewCell.swift файлов с кодом: AssetListTableViewController — это основной интерфейс этого примера. Он предоставляет список ресурсов, образец можно использовать для воспроизведения, загрузки, удалить или отменить загрузку. 

Следующие действия показывают, как настройка проигрывателе выполнение операций ввода-вывода. При условии, что запуск из образца HLSCatalog в число кадров в Секунду Server SDK версии 4.0.1, внесите следующие изменения в код:

В HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, реализуйте метод `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` , используя следующий код. Позвольте «drmUr» быть назначен URL-адрес HLS переменной.

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

В HLSCatalog\Shared\Managers\ContentKeyDelegate.swift реализуйте метод `requestApplicationCertificate()`. Эта реализация зависит от того, внедряете ли вы сертификат (только открытый ключ) в устройство или размещаете сертификат в Интернете. Следующая реализация использует сертификат размещенного приложения, используемые в примерах теста. Позвольте «certUrl» быть переменная, содержащая URL-адрес сертификата приложения.

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

Для последнего теста интеграции видео URL-адреса и URL-адрес сертификата приложения приведены в разделе «Интегрированная Test».

В HLSCatalog\Shared\Resources\Streams.plist добавьте URL-адрес видео теста. Для содержимого ИД ключа, используется URL-адрес приобретения лицензии FairPlay с протоколом skd как уникальное значение.

![Автономные потоки FairPlay приложения iOS](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Используйте видео тестирования URL-адрес, URL-адрес приобретения лицензии FairPlay и URL-адрес сертификата приложения, при наличии их настроить. Или можно продолжать в следующем разделе содержатся образцы теста.

## <a name="integrated-test"></a>Встроенная теста
Три примера теста в службах мультимедиа рассматриваются следующие три варианта:

* Число кадров в Секунду защищены с помощью видео, аудио- и альтернативный звуковой дорожки
* Число кадров в Секунду, защищена, видео и аудио, но не альтернативной звуковой дорожки
* Защищенные не аудио и видео только кадров/с

Можно найти в этих примерах на [этого сайта](http://aka.ms/poc#22), с соответствующим сертификатом приложения, размещенные в Azure веб-приложение.
С версии 3 или версии 4 образца пакета SDK сервера кадров/с Если главный список воспроизведения содержит альтернативный аудио, во время автономного режима его воспроизводящего звук только. Таким образом необходимо убрать альтернативный аудио. Другими словами второй и третий примерам, указанным ранее работать в подключенном и автономном режиме. Образец в списке сначала воспроизводящего звук только во время автономного режима, пока сети потоковой передачи работает правильно.

## <a name="faq"></a>Часто задаваемые вопросы
Следующие часто задаваемые вопросы оказывать помощь в устранении неполадок:

- **Почему только звук воспроизводится, кроме не видео в режиме вне сети?** Такое поведение, по-видимому, связано с дизайном примера приложения. Когда альтернативной звуковой дорожки, присутствует (что происходит в случае HLS) во время автономного режима, операций ввода-вывода 10 и iOS 11 по умолчанию для альтернативной звуковой дорожки. Чтобы компенсировать это поведение FPS автономный режим, удалите альтернативной звуковой дорожки из потока. Для этого в службах мультимедиа, добавить динамический фильтр манифеста «только со звуком = false.» Другими словами URL-адрес HLS заканчивается .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Почему он по-прежнему играет аудио только без видео в режиме вне сети после добавления только со звуком = false?** В зависимости от доставки содержимого (CDN) кэша ключа архитектуры могут быть кэшированы содержимое. Очистить кэш.
- **Режим автономной работы FPS также поддерживается на iOS 11 в дополнение к iOS 10?** Да. Для iOS 10 и iOS 11 поддерживается автономный режим кадров в Секунду.
- **Почему не удается найти документ «Автономный режим воспроизведения с FairPlay Streaming и HTTP Live Streaming» в пакете SDK сервера кадров/с** С момента кадров/с сервера SDK версии 4 в этом документе были включены в «FairPlay Streaming руководство по программированию.»
- **Что означает последний параметр в следующем API для автономного режима FPS?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Документацию для этого API см. в разделе [FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration метод](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). Параметр представляет время работы вне сети аренды часа в качестве единицы измерения.
- **Какова структура скачанного или автономного файла на устройствах iOS?** Структура загруженный файл на устройстве iOS выглядит как следующий снимок экрана. `_keys` Папок для скачивания лицензий кадров/с с помощью файла одного хранилища для каждого узла службы лицензирования. `.movpkg` Папке хранятся содержимому аудио и видео. Первая папка с именем, оканчивающимся на тире, следуют числовой содержит видео-контента. Числовое значение равно PeakBandwidth видео представлений. Вторую папку с именем, которое заканчивается с дефисом, за которым следует 0 содержит звукового содержимого. Третья папка с именем "Данные" содержит главный список воспроизведения содержимого FPS. Наконец, boot.xml предоставляет полное описание `.movpkg` содержимого папки. 

![Структура файла приложения образец автономного FairPlay iOS](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

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
Данный документ содержит следующие шаги и сведения, которые можно использовать для реализации автономного режима кадров/с:

* Конфигурация защиты контента служб мультимедиа через API .NET служб мультимедиа настраивает динамического шифрования FairPlay и доставки лицензии FairPlay в службах мультимедиа.
* Проигрыватель операций ввода-вывода на основе выборки из пакета SDK сервера кадров/с устанавливает iOS проигрыватель для воспроизведения кадров в Секунду содержимого, либо в режиме потоковой передачи в сети или в автономном режиме.
* Образцы видео кадров/с используются для проверки в автономном режиме и интерактивной потоковой передачи.
* Часто задаваемые вопросы о ответы на вопросы об автономном режиме кадров в Секунду.
