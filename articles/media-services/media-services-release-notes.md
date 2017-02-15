---
title: "Заметки о выпуске служб мультимедиа | Документация Майкрософт"
description: "Заметки о выпуске служб мультимедиа"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 3ca2d7af-1cf0-45fa-9585-3b73f3ee057d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 09/19/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9c851bda4ec024c62f0d05796806fcff75fbf427


---
# <a name="azure-media-services-release-notes"></a>Заметки о выпуске служб мультимедиа Azure
В этих заметках описаны изменения по сравнению с предыдущими выпусками, а также известные проблемы.

> [!NOTE]
> Мы ждем отзывов от наших заказчиков и уделяем большое внимание исправлению проблем, влияющих на вашу работу. Сообщения о проблемах и вопросы размещайте на [форуме MSDN по службам мультимедиа Azure].
> 
> 

## <a name="a-idissuesacurrently-known-issues"></a><a id="issues"></a>Известные проблемы
### <a name="a-idgeneralissuesamedia-services-general-issues"></a><a id="general_issues"></a>Общие проблемы служб мультимедиа
| Проблема | Описание |
| --- | --- |
| В API-интерфейсе REST отсутствует ряд стандартных заголовков HTTP. |При разработке приложений служб мультимедиа с использованием API-интерфейса REST замечено, что ряд стандартных полей заголовков HTTP (включая CLIENT-REQUEST-ID, REQUEST-ID и RETURN-CLIENT-REQUEST-ID) не поддерживается. Эти заголовки будут добавлены в следующем обновлении. |
| URL-кодирование содержимого не допускается. |Службы мультимедиа используют значение свойства IAssetFile.Name при создании URL-адресов для потоковой передачи содержимого (например, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) По этой причине кодирование с помощью знака процента не допускается. Значение свойства **Name** не может содержать [знаки, зарезервированные для кодирования с помощью знака процента](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters), а именно: !*'();:@&=+$,/?%#[]".. Кроме того, может использоваться только один символ точки (.). Кроме того, может использоваться только один символ "." для расширения имени файла. |
| Метод ListBlobs, входящий в состав пакета SDK хранилища Azure версии 3.x, приводит к сбою. |Службы мультимедиа создают URL-адреса SAS на основе версии [2012-02-12](http://msdn.microsoft.com/library/azure/dn592123.aspx) . Если вы хотите использовать пакет SDK хранилища Azure для создания списка BLOB-объектов в контейнере BLOB-объектов, используйте метод [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) , входящий в пакет SDK хранилища Azure версии 2.x. Метод ListBlobs, входящий в состав пакета SDK хранилища Azure версии 3.x, будет вызывать сбой. |
| Механизм регулирования служб мультимедиа ограничивает использование ресурсов для приложений, создающих избыточный запрос к службе. Службой может быть возвращен код состояния HTTP "Service Unavailable (503)". |Дополнительные сведения см. в описании кода состояния HTTP 503 в статье с [кодами ошибок служб мультимедиа Azure](http://msdn.microsoft.com/library/azure/dn168949.aspx). |
| При запросе сущностей существует ограничение в 1000 сущностей, возвращаемых за один раз, так как в открытой версии 2 REST количество результатов запросов ограничено 1000. |Используйте **Skip** и **Take** (.NET) или **top** (REST), как описано в [этом](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) и [этом](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities) примерах. |
| Некоторые клиенты могут сталкиваться с проблемой тега повтора в манифесте Smooth Streaming. |Дополнительные сведения см. в [этом разделе](media-services-deliver-content-overview.md#known-issues). |
| Объекты в пакете SDK .NET для служб мультимедиа Azure не могут быть сериализованы и поэтому не работают со службой кэша Azure. |При попытке сериализации объекта SDK AssetCollection с целью его добавления в Azure Caching возникает исключение. |
| Задание кодирования завершается ошибкой с таким сообщением: "Stage: DownloadFile. Code: System.NullReferenceException". |Типичный рабочий процесс кодирования подразумевает передачу входных файлов видео во входной ресурс-контейнер и отправку одного или нескольких заданий кодирования для этого ресурса. При этом других изменений для входного ресурса-контейнера не происходит. Но если вы измените входной ресурс-контейнер (например, добавите, удалите или переименуете файлы в нем), последующие задания могут завершиться ошибкой DownloadFile. Чтобы обойти эту проблему, удалите входной ресурс-контейнер и повторно отправьте входные файлы в новый ресурс. |

## <a name="a-idrestversionhistoryarest-api-version-history"></a><a id="rest_version_history"></a>Журнал версий интерфейса API REST
Сведения о журнале версий интерфейса REST API служб мультимедиа см. в [этом справочнике].

## <a name="a-idjulychanges16ajuly-2016-release"></a><a id="july_changes16"></a>Выпуск: июль 2016 г.
### <a name="updates-to-manifest-file-ism-generated-by-encoding-tasks"></a>Обновления файла манифеста (ISM-файла), созданного задачами кодирования
Когда вы отправляете задачу кодирования в Media Encoder Standard или в кодировщик мультимедиа Azure, задача кодирования создает [файл манифеста потоковой передачи](media-services-deliver-content-overview.md) (ISM-файл) в ресурсе-контейнере выходных данных. В последнем выпуске службы обновлен синтаксис этого файла манифеста потоковой передачи.

> [!NOTE]
> Синтаксис файла манифеста потоковой передачи (ISM-файл) зарезервирован для внутреннего использования и может измениться в будущих выпусках. Не изменяйте содержимое этого файла.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Новый файл клиентского манифеста (ISMC-файл) создается в ресурсе-контейнере выходных данных, когда задача кодирования выводит один или несколько MP4-файлов
Начиная с последней версии службы, после завершения задачи кодирования, которая создает один или несколько MP4-файлов, ресурс-контейнер выходных данных также будет содержать файл клиентского манифеста потоковой передачи (ISMC-файл). ISMC-файл помогает повысить производительность динамической потоковой передачи. 

> [!NOTE]
> Синтаксис файла клиентского манифеста (ISMC-файла) зарезервирован для внутреннего использования и может измениться в будущих выпусках. Не изменяйте содержимое этого файла.
> 
> 

Дополнительную информацию см. в [этом](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) блоге.

### <a name="known-issues"></a>Известные проблемы
Некоторые клиенты могут сталкиваться с проблемой тега повтора в манифесте Smooth Streaming. Дополнительные сведения см. в [этом разделе](media-services-deliver-content-overview.md#known-issues).

## <a name="a-idaprchanges16aapril-2016-release"></a><a id="apr_changes16"></a>Выпуск: апрель 2016 г.
### <a name="azure-media-analytics"></a>Аналитика мультимедиа Azure
В службах мультимедиа Azure появилась возможность аналитики мультимедиа для интеллектуальной работы с видео. Подробные сведения см. в статье [Общие сведения об аналитике служб мультимедиа Azure](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (предварительная версия)
Службы мультимедиа Azure теперь позволяют применять динамическое шифрование содержимого HTTP Live Streaming (HLS) с помощью Apple FairPlay. Службу доставки лицензий для служб мультимедиа можно также использовать для доставки клиентам лицензий FairPlay. Подробные сведения см. в статье [Использование служб мультимедиа Azure для потоковой передачи содержимого HLS, защищенного с помощью Apple FairPlay](media-services-protect-hls-with-fairplay.md).

## <a name="a-idfebchanges16afebruary-2016-release"></a><a id="feb_changes16"></a>Выпуск: февраль 2016 г.
Последняя версия пакета SDK служб мультимедиа Azure для .NET (3.5.3) содержит исправление ошибки, связанной с Widevine. Устраненная проблема: AssetDeliveryPolicy не удавалось повторно использовать для нескольких ресурсов-контейнеров, зашифрованных с помощью Widevine. В рамках этого исправления в пакет SDK добавлено следующее свойство: **WidevineBaseLicenseAcquisitionUrl**.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a name="a-idjanchanges16ajanuary-2016-release"></a><a id="jan_changes_16"></a>Выпуск: январь 2016 г.
Зарезервированные единицы кодирования переименованы во избежание путаницы с именами кодировщика.

Зарезервированные модули кодирования "Базовый", "Стандартный" и "Премиум" переименованы в зарезервированные модули кодирования S1, S2 и S3 соответственно.  Теперь пользователи зарезервированных модулей кодирования Базовый будут видеть на портале Azure (а также счете) метку S1, а пользователи модулей Стандартный и Премиум — метки S2 и S3 соответственно. 

## <a name="a-iddecchanges15adecember-2015-release"></a><a id="dec_changes_15"></a>Выпуск: декабрь 2015 г.
Команда SDK Azure опубликовала новый выпуск пакета [SDK Azure для PHP](http://github.com/Azure/azure-sdk-for-php) , содержащий обновления и новые функции для служб мультимедиа Microsoft Azure. В частности, пакет SDK служб мультимедиа Azure для PHP теперь поддерживает новые функции [защиты контента](media-services-content-protection-overview.md) : динамическое шифрование с использованием AES и управления цифровыми правами (PlayReady и Widevine) с ограничением по маркеру или без него. Кроме того, он поддерживает масштабирование [единиц кодирования](media-services-dotnet-encoding-units.md).

Дополнительные сведения можно найти в разделе 

* Блог [SDK служб мультимедиа Microsoft Azure для PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) .
* Быстро приступить к работе вам помогут [следующие примеры](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) .
  * **vodworkflow_aes.php**: PHP-файл, показывающий, как использовать динамическое шифрование c помощью алгоритма AES-128 и службы доставки ключей. Он основан на примере кода .NET, подробно описанном в [этой](media-services-protect-with-aes128.md) статье.
  * **vodworkflow_aes.php**: PHP-файл, показывающий, как использовать динамическое шифрование c помощью алгоритма PlayReady и службы доставки лицензий. Он основан на примере кода .NET, подробно описанном в [этой](media-services-protect-with-drm.md) статье.
  * **scale_encoding_units.php**: PHP-файл, показывающий, как масштабировать зарезервированную единицу кодирования.

## <a name="a-idnovchanges15anovember-2015-release"></a><a id="nov_changes_15"></a>Выпуск: ноябрь 2015 г.
Теперь в службах мультимедиа Azure предлагается служба доставки лицензий Google Widevine. Для получения дополнительных сведений см. [этот блог объявлений](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). См. также [это руководство](media-services-protect-with-drm.md) и [репозиторий GitHub](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Обратите внимание, что службами мультимедиа Azure предоставляется предварительная версия служб доставки лицензий Widevine. Дополнительные сведения см. в [этом блоге](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a name="a-idoctchanges15aoctober-2015-release"></a><a id="oct_changes_15"></a>Выпуск: октябрь 2015 г.
Службы мультимедиа Azure (AMS) теперь доступны в следующих центрах обработки данных: южная Бразилия, западная Индия, южная Индия и центральная Индия. Кроме того, на классическом портале Azure можно [создавать учетные записи служб мультимедиа](media-services-portal-create-account.md) и выполнять различные задачи, описанные [здесь](https://azure.microsoft.com/documentation/services/media-services/). Но в этих центрах обработки данных не поддерживается кодирование в реальном времени. Кроме того, в этих центрах обработки данных доступны не все типы зарезервированных единиц кодирования.

* Южная Бразилия: доступны зарезервированные единицы кодирования только уровня "Стандартный" и "Базовый".
* Западная Индия, южная Индия и Центральная Индия: доступны зарезервированные единицы кодирования только уровня "Базовый".

## <a name="a-idseptemberchanges15aseptember-2015-release"></a><a id="september_changes_15"></a>Выпуск: сентябрь 2015 г.
* Теперь AMS позволяет защищать видео по запросу и потоки Live Stream с технологией Widevine Modular DRM. Вы можете использовать следующих партнеров служб доставки для доставки лицензий Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) и [castLabs](http://castlabs.com/company/partners/azure/). Дополнительную информацию см. в [этом блоге](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
    Чтобы настроить AssetDeliveryConfiguration для использования Widevine, можно воспользоваться пакетом [SDK AMS .NET](https://www.nuget.org/packages/windowsazure.mediaservices/) (начиная с версии 3.5.1) или REST API.  
* AMS поддерживает видео в формате Apple ProRes. Теперь вы можете отправлять исходные видеофайлы в формате QuickTime, использующие Apple ProRes или другие кодеки. Дополнительную информацию см. в [этом блоге](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Теперь можно использовать Media Encoder Standard для выполнения дополнительной обрезки и динамического извлечения из архивов. Дополнительную информацию см. в [этом блоге](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Были выполнены следующие обновления фильтрации: 
  
  * Теперь можно использовать формат Apple HTTP Live Streaming (HLS) с фильтром "только аудио". Это обновление позволяет удалить только звуковую дорожку, указав в URL-адресе "audio-only=false".
  * Теперь при определении фильтров для ресурсов можно объединить несколько фильтров (до 3) в одном URL-адресе.
    
    Дополнительную информацию см. в [этом](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) блоге.
* AMS теперь поддерживает I-Frames в HLS версии 4. Поддержка I-Frames оптимизирует операции перемотки вперед и назад. По умолчанию все выходные каналы HLS версии 4 включают список воспроизведения I-Frames (EXT-X-I-FRAME-STREAM-INF).
  
    Дополнительную информацию см. в [этом](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) блоге.

## <a name="a-idaugustchanges15aaugust-2015-release"></a><a id="august_changes_15"></a>Выпуск от августа 2015 г.
* В настоящее время доступны пакет SDK служб мультимедиа Azure для выпуска Java версии 0.8.0 и новые примеры. Дополнительные сведения можно найти в разделе 
  
  * [Запись блога](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
  * [Репозиторий примеров для Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
* Обновление мультимедиапроигрывателя Azure с поддержкой нескольких аудиопотоков. Дополнительные сведения можно найти в разделе 
  * [Запись блога](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

## <a name="a-idjulychanges15ajuly-2015-release"></a><a id="july_changes_15"></a>Выпуск: июль 2015 г.
* В общий доступ предоставлен стандартный кодировщик служб мультимедиа. Дополнительные сведения см. в [этой записи блога](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    В Media Encoder Standard используются предустановки, описанные в [этой](http://go.microsoft.com/fwlink/?LinkId=618336) статье. Обратите внимание, что при использовании предустановки для кодирования видео в формате 4K необходимо получить тип зарезервированной единицы **Премиум**. Дополнительные сведения см. в статье [Масштабирование кодирования](media-services-scale-media-processing-overview.md).
* Интерактивные субтитры в режиме реального времени в службах мультимедиа Azure и проигрывателе. Дополнительные сведения см. в [этой записи блога](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Обновления пакета SDK служб мультимедиа для .NET
Пакет SDK служб мультимедиа Azure для .NET обновлен до версии 3.4.0.0. В этом выпуске добавлены следующие функциональные возможности:  

* Реализована поддержка динамического архива. При этом загружать ресурсы, содержащие динамические архивы, нельзя.
* Реализована поддержка динамических фильтров.
* Реализованы функциональные возможности, позволяющие пользователям сохранять контейнер хранилища при удалении ресурса.
* Исправлены ошибки, связанные с политиками повтора в каналах.
* Включен **рабочий процесс Media Encoder Premium**.

## <a name="a-idjunechanges15ajune-2015-release"></a><a id="june_changes_15"></a>Выпуск: июнь 2015 г.
### <a name="media-services-net-sdk-updates"></a>Обновления пакета SDK служб мультимедиа для .NET
Пакет SDK служб мультимедиа Azure для .NET обновлен до версии 3.3.0.0. В этом выпуске добавлены следующие функциональные возможности:  

* поддержка спецификации OpenId подключения обнаружения,
* поддержка обработки смены ключей на стороне поставщика удостоверений. 

При использовании поставщика удостоверений, который предоставляет документ обнаружения OpenID Connect (как и другие поставщики: Azure Active Directory, Google, Salesforce), вы можете настроить службы мультимедиа Azure на получение ключей подписи для проверки токена JWT из спецификации обнаружения подключения OpenID. 

Дополнительные сведения см. в статье [Using Json Web Keys from OpenID Connect discovery spec to work with JWT token authentication in Azure Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/) (Использование токена Json Web из спецификации обнаружения OpenID Connect для работы с проверкой подлинности токена JWT в службах мультимедиа Azure).

## <a name="a-idmaychanges15amay-2015-release"></a><a id="may_changes_15"></a>Выпуск: май 2015 г.
Новые функции:

* [Предварительный просмотр кодирования в реальном времени с помощью служб мультимедиа](media-services-manage-live-encoder-enabled-channels.md)
* [Динамические манифесты](media-services-dynamic-manifest-overview.md)
* [Предварительный просмотр обработчика мультимедиа Azure Media Hyperlapse](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a name="a-idaprilchanges15aapril-2015-release"></a><a id="april_changes_15"></a>Выпуск: апрель 2015 г.
### <a name="general-media-services-updates"></a>Общие обновления служб мультимедиа
* [Выход мультимедиапроигрывателя Azure](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
* Начиная с интерфейса REST 2.10 служб мультимедиа, каналы, которые настроены для приема протокола RTMP, создаются с первичными и вторичными URL-адресами приема. Дополнительные сведения см. в разделе [Конфигурации входа (приема) канала](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Обновления индексатора мультимедийных данных Azure
* Поддержка испанского языка
* Формат xml новой конфигурации

Дополнительные сведения см. в [этом блоге](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Обновления пакета SDK служб мультимедиа для .NET
Пакет SDK служб мультимедиа Azure для .NET обновлен до версии 3.2.0.0.

Ниже приведены некоторые обновления для клиентов.

* **Критическое изменение**: **TokenRestrictionTemplate.Issuer** и **TokenRestrictionTemplate.Audience** изменено на тип строки.
* Обновления, связанные с созданием пользовательских политик повторов.
* Исправления ошибок, относящиеся к передаче или загрузке файлов.
* Класс **MediaServicesCredentials** теперь принимает первичные и дополнительные конечные точки службы контроля доступа для проверки подлинности.

## <a name="a-idmarchchanges15amarch-2015-release"></a><a id="march_changes_15"></a>Выпуск: март 2015 г.
### <a name="general-media-services-updates"></a>Общие обновления служб мультимедиа
* Теперь службы мультимедиа обеспечивают интеграцию Azure CDN. Для поддержки интеграции свойство **CdnEnabled** было добавлено к **StreamingEndpoint**.  **CdnEnabled** можно использовать с REST API, начиная с версии 2.9 (дополнительные сведения см. в статье [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)).  **CdnEnabled** можно использовать с пакетом SDK для .NET, начиная с версии 3.1.0.2 (дополнительные сведения см. в статье [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx)).
* Объявление о **рабочем процессе Premium обработчика мультимедиа**. Дополнительные сведения см. в статье [Знакомство с кодированием Premium в службах мультимедиа Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a name="a-idfebruarychanges15afebruary-2015-release"></a><a id="february_changes_15"></a>Выпуск: февраль 2015 года.
### <a name="general-media-services-updates"></a>Общие обновления служб мультимедиа
API REST служб мультимедиа обновлены до версии 2.9. Начиная с этой версии можно использовать интеграцию Azure CDN с конечными точками потоковой передачи. Дополнительные сведения см. в статье [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a name="a-idjanuarychanges15ajanuary-2015-release"></a><a id="january_changes_15"></a>Выпуск: январь 2015 г.
### <a name="general-media-services-updates"></a>Общие обновления служб мультимедиа
Объявления о поддержке защиты содержимого с помощью динамического шифрования в общедоступной версии. Дополнительные сведения см. в статье [Azure Media Services enhances streaming security with General Availability of DRM technology](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/) (Потоковая передача в службах мультимедиа Azure стала еще безопаснее благодаря поддержке технологии DRM в общедоступной версии).

### <a name="media-services-net-sdk-updates"></a>Обновления пакета SDK служб мультимедиа для .NET
Вышла версия 3.1.0.1 пакета SDK служб мультимедиа Azure для .NET.

В этом выпуске конструктор Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate по умолчанию отмечен как устаревший. Новый конструктор принимает в качестве аргумента TokenType.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a name="a-iddecemberchanges14adecember-2014-release"></a><a id="december_changes_14"></a>Выпуск: декабрь 2014 г.
### <a name="general-media-services-updates"></a>Общие обновления служб мультимедиа
* В Azure Indexer Media Processor добавлены некоторые обновления и функции. Дополнительные сведения см. в разделе [Azure Media Indexer Version 1.1.6.7 Release Notes](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/) (Заметки о выпуске Azure Media Indexer версии 1.1.6.7).
* Добавлен новый интерфейс API REST, который позволяет обновлять закодированные зарезервированные единицы: [EncodingReservedUnitType с REST](http://msdn.microsoft.com/library/azure/dn859236.aspx).
* Добавлена поддержка CORS для службы доставки ключей.
* Повышена производительность параметров политики запроса авторизации.
* В китайском центре обработки данных [URL-адрес доставки ключей](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) теперь распределяется на клиента (как и в других центрах обработки данных).
* Добавлено автоматическое назначение длительности HLS. При динамической потоковой передаче HLS всегда упаковывается динамически. По умолчанию службы мультимедиа автоматически вычисляют соотношение упаковки сегмента HLS (FragmentsPerSegment) на основе интервала опорного кадра (KeyFrameInterval ), который также называется "группа кадров", полученного из динамического кодировщика. Дополнительные сведения см. в статье [Общие сведения о динамической потоковой передаче с использованием служб мультимедиа Azure].

### <a name="media-services-net-sdk-updates"></a>Обновления пакета SDK служб мультимедиа для .NET
* [Пакет SDK служб мультимедиа Azure для .NET](http://www.nuget.org/packages/windowsazure.mediaservices/) обновлен до версии 3.1.0.0.
* Обновлена зависимость .Net SDK от .NET 4.5 Framework.
* Добавлен новый интерфейс API, который позволяет обновить закодированные зарезервированные единицы. Дополнительные сведения см. в статье [Масштабирование кодирования с помощью пакета SDK для .NET](http://msdn.microsoft.com/library/azure/jj129582.aspx).
* Добавлена поддержка токена JWT (веб-токена JSON) для проверки подлинности токенов. Дополнительные сведения см. в статье [JWT token Authentication in Azure Media Services and Dynamic Encryption](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/) (Проверка подлинности токена JWT в службах мультимедиа Azure и динамическое шифрование).
* Добавлены относительные смещения для BeginDate и ExpirationDate в шаблоне лицензий PlayReady.

## <a name="a-idnovemberchanges14anovember-2014-release"></a><a id="november_changes_14"></a>Выпуск: ноябрь 2014 г.
* Службы мультимедиа теперь позволяют принимать динамическое содержимое Smooth Streaming (FMP4) по SSL-подключению. Для приема по протоколу SSL измените URL-адрес приема на HTTPS.  Дополнительные сведения о потоковой передаче в реальном времени см. в статье [Общие сведения о динамической потоковой передаче с использованием служб мультимедиа Azure].
* Обратите внимание, что в настоящее время невозможно принять потоковую передачу RTMP по SSL-подключению.
* Также по SSL-подключению можно выполнять потоковую передачу содержимого. Для этого убедитесь, что URL-адреса потоковой передачи начинаются с HTTPS.
* Обратите внимание, что потоковую передачу через SSL-соединение можно выполнять, только если конечная точка потоковой передачи, из которой получено содержимое, была создана после 10 сентября 2014 г. Если URL-адреса потоковой передачи основаны на конечных точках потоковой передачи, созданных после 10 сентября, URL-адрес содержит "streaming.mediaservices.windows.net" (новый формат). URL-адреса потоковой передачи, который содержат "origin.mediaservices.windows.net" (старый формат), не поддерживают SSL. Если URL-адрес имеет старый формат и необходимо организовать передачу через SSL, [создайте новую конечную точку потоковой передачи](media-services-portal-manage-streaming-endpoints.md). Используйте URL-адреса, созданные на основе новой конечной точки потоковой передачи, для потоковой передачи содержимого по SSL.

## <a name="a-idoctoberchanges14aoctober-2014-release"></a><a id="october_changes_14"></a>Выпуск: октябрь 2014 г.
### <a name="a-idnewencoderreleaseamedia-services-encoder-release"></a><a id="new_encoder_release"></a>Выпуск кодировщика служб мультимедиа
Объявление о новом выпуске обработчика мультимедиа Media Services Azure. Новый кодировщик служб мультимедиа Azure тарифицируется только по количеству исходящих ГБ и функционально совместим с предыдущей версией кодировщика. Дополнительные сведения см. на странице [цен на службы мультимедиа].

### <a name="a-idoctsdkamedia-services-net-sdk"></a><a id="oct_sdk"></a>Пакет SDK служб мультимедиа для .NET
Пакет SDK служб мультимедиа для расширений .NET теперь имеет версию 2.0.0.3.

Пакет SDK служб мультимедиа для .NET теперь имеет версию 3.0.0.8.

Были внесены следующие изменения.

* Рефакторинг в классах политики повторов.
* Добавление строки агента пользователя в заголовки HTTP-запросов.
* Добавление шага восстановления сборки NuGet.
* Исправление тестов сценария: теперь они используют сертификат x509 из репозитория.
* Проверка параметров при обновлении канала и точки потоковой передачи.

### <a name="new-github-repository-to-host-media-services-samples"></a>Новый репозиторий GitHub для размещения примеров служб мультимедиа
Примеры расположены в [репозитории примеров GitHub служб мультимедиа Azure](https://github.com/Azure/Azure-Media-Services-Samples).

## <a name="a-idseptemberchanges14aseptember-2014-release"></a><a id="september_changes_14"></a>Выпуск: сентябрь 2014 г.
Метаданные REST служб мультимедиа теперь имеют версию 2.7. Дополнительные сведения о последних обновлениях REST см. в [этом справочнике].

Пакет SDK служб мультимедиа для .NET теперь имеет версию 3.0.0.7.

### <a name="a-idsept14breakingchangesabreaking-changes"></a><a id="sept_14_breaking_changes"></a>Критические изменения
* **Источник** переименован в [StreamingEndpoint].
* Изменено поведение по умолчанию при использовании **классического портала Azure** для кодирования и публикации MP4-файлов.

Ранее при использовании классического портала для публикации ресурса видео MP4, состоящего из одного файла, создавался URL-адрес SAS (URL-адреса SAS позволяют загружать видео из хранилища больших двоичных объектов). В настоящее время при кодировании и последующей публикации ресурса видео MP4, состоящего из одного файла, с помощью классического портала Azure создаваемый URL-адрес указывает на конечную точку потоковой передачи служб мультимедиа Azure.  Это изменение не затрагивает видеофайлы MP4, напрямую переданные в службы мультимедиа и опубликованные без кодирования с помощью служб мультимедиа Azure.

В настоящее время есть два варианта устранения этой проблемы.

* Включить единицы потоковой передачи и использовать динамическую упаковку для потоковой передачи актива .mp4 как пакета Smooth Streaming.
* Создать URL-адрес SAS для загрузки (или постепенного воспроизведения) файла .mp4. Дополнительные сведения о создании локатора SAS см. в статье о [доставке содержимого].

### <a name="a-idsept14gachangesanew-featuresscenarios-that-are-part-of-ga-release"></a><a id="sept_14_GA_changes"></a>Новые функции и сценарии, включенные в общедоступный выпуск
* **Indexer Media Processor.** Дополнительные сведения см. в статье [Индексирование файлов мультимедиа с помощью Azure Media Indexer].
* Сущность [StreamingEndpoint] теперь позволяет добавлять пользовательские имена доменов (узлов).
  
    Чтобы использовать пользовательское имя домена в качестве имени конечной точки потоковой передачи в службах мультимедиа, необходимо добавить пользовательские имена узлов в конечную точку потоковой передачи. Для добавления пользовательских имен узлов используйте API-интерфейсы REST служб мультимедиа или пакет SDK для .NET.
  
    Действительны следующие условия.
  
  * Вы должны являться владельцем пользовательского доменного имени.
  * Владелец доменного имени должен быть проверен службами мультимедиа Azure. Чтобы проверить домен, создайте запись CName, которая сопоставляет <MediaServicesAccountId>.<parent domain> с verifydns.<mediaservices-dns-zone>. 
  * Необходимо создать другую запись CName, которая сопоставляет имя пользовательского узла (например, sports.contoso.com) с именем узла StreamingEndpont служб мультимедиа (например, amstest.streaming.mediaservices.windows.net).

    Дополнительные сведения см. в описании свойства **CustomHostNames** в статье [StreamingEndpoint].

### <a name="a-idsept14previewchangesanew-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>Новые функции и сценарии, включенные в общедоступный предварительный выпуск
* Предварительный просмотр потоковой передачи в реальном времени. Дополнительные сведения см. в статье [Общие сведения о динамической потоковой передаче с использованием служб мультимедиа Azure].
* Служба доставки ключей. Дополнительные сведения см. в статье [Использование динамического шифрования AES-128 и службы доставки ключей].
* Динамическое шифрование на основе AES. Дополнительные сведения см. в статье [Использование динамического шифрования AES-128 и службы доставки ключей].
* Служба доставки лицензий PlayReady. Дополнительные сведения см. в статье [Использование общего динамического шифрования PlayReady и (или) Widevine DRM].
* Динамическое шифрование на основе PlayReady. Дополнительные сведения см. в статье [Использование общего динамического шифрования PlayReady и (или) Widevine DRM].
* Шаблон лицензии PlayReady для служб мультимедиа. Дополнительные сведения см. в статье [Обзор шаблонов лицензий PlayReady служб мультимедиа].
* Потоковая передача зашифрованных активов хранилища. Дополнительные сведения см. в статье [Настройка политик доставки ресурсов-контейнеров с помощью пакета SDK для .NET].

## <a name="a-idaugustchanges14aaugust-2014-release"></a><a id="august_changes_14"></a>Выпуск: август 2014 г.
После выполнения задания кодирования актива создается выходной актив. До этого выпуска кодировщик служб мультимедиа Azure создавал метаданные о выходных активах. Начиная с этого выпуска также создаются метаданные о входных активах. Дополнительные сведения см. в статьях о [входных] и [выходных метаданных].

## <a name="a-idjulychanges14ajuly-2014-release"></a><a id="july_changes_14"></a>Выпуск: июль 2014 г.
Исправлены следующие ошибки приложений Azure Media Services Packager и Encryptor.

* В процессе смены контейнера (с сохранением исходных видео- и аудиоданных) архивного актива на HTTP Live Streaming воспроизводились только аудиоданные. Эта ошибка исправлена. Теперь воспроизводится и аудио, и видео.
* При упаковке актива в контейнер HTTP Live Streaming и использовании 128-разрядного шифрования AES упакованные потоки не воспроизводились на устройствах Android. Эта ошибка исправлена. Теперь упакованный поток воспроизводится на устройствах Android, поддерживающих HTTP Live Streaming.

## <a name="a-idmaychanges14amay-2014-release"></a><a id="may_changes_14"></a>Выпуск: май 2014 г.
### <a name="a-idmay14changesageneral-media-services-updates"></a><a id="may_14_changes"></a>Общие обновления служб мультимедиа
Теперь можно использовать [динамическую упаковку] для потоков HTTP Live Streaming (HLS) v3. Для потоковой передачи HLS версии 3 добавьте следующий формат в исходный путь локатора: * .ism/manifest(format=m3u8-aapl-v3). Дополнительные сведения см. в [блоге Ника Дроуина (Nick Drouin)].

Динамическая упаковка теперь также поддерживает доставку потоков HLS (v3 и v4), зашифрованных с помощью PlayReady на основе Smooth Streaming со статическим шифрованием с использованием PlayReady. Дополнительные сведения о шифровании Smooth Streaming с помощью PlayReady см. в статье о [защите потоков Smooth Stream с помощью PlayReady].

### <a name="a-namemay14donnetchangesamedia-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>Обновления пакета SDK служб мультимедиа для .NET
Выпуск 3.0.0.5 пакета SDK служб мультимедиа для .NET имеет следующие усовершенствованные характеристики.

* Увеличена скорость и отказоустойчивость передачи и загрузки активов мультимедиа.
* Улучшена логика повторных попыток и обработка временных исключений. 
  
  * Улучшены обнаружение временных ошибок и логика повторных попыток для исключений при отправке запросов, сохранении изменений, отправке и загрузке файлов. 
  * При получении исключений веб-приложений (например, во время запроса маркера ACS) неустранимые ошибки теперь обрабатываются быстрее.

Дополнительные сведения см. в статье [Логика повторных попыток в пакете SDK служб мультимедиа для .NET].

## <a name="a-idaprilchanges14aapril-2014-encoder-release"></a><a id="april_changes_14"></a>Выпуск кодировщика: апрель 2014 г.
### <a name="a-nameapril14enocerchangesamedia-services-encoder-updates"></a><a name="april_14_enocer_changes"></a>Обновления кодировщика служб мультимедиа
* Добавлена поддержка поглощения файлов AVI, созданных в нелинейном редакторе Grass Valley EDIUS, в которых видеоданные незначительно сжаты с помощью кодека Grass Valley HQ/HQX. Дополнительные сведения см. в статье [Grass Valley представляет потоковую передачу EDIUS 7 в облаке].
* Добавлена поддержка для указания соглашения о присвоении имен файлов, созданных кодировщиком мультимедиа. Дополнительные сведения см. в статье [Управление именами выходных файлов кодировщика служб мультимедиа].
* Добавлена поддержка наложения видео- и/или аудиоданных. Дополнительные сведения см. в статье [Создание наложений].
* Добавлена поддержка стыковки нескольких сегментов видео. Дополнительные сведения см. в статье [Стыковка сегментов видео].
* Исправлена ошибка, связанная с перекодированием файлов MP4, в которых аудиопоток имеет кодировку MPEG-1 Audio Layer 3 (так называемый MP3).

## <a name="a-idjanfebchanges14ajanuaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>Выпуски: январь — февраль 2014 г.
### <a name="a-namejanfab14donnetchangesaazure-media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>Пакет SDK служб мультимедиа Azure для .NET 3.0.0.1, 3.0.0.2 и 3.0.0.3
В версиях 3.0.0.1 и 3.0.0.2 внесены следующие изменения.

* Исправлены ошибки, связанные с использованием запросов LINQ с инструкциями OrderBy.
* Тестовые решения в [GitHub] разделены на тесты на основе единиц и тесты на основе сценариев.

Дополнительные сведения об этих изменениях см. в статье [Выпуски 3.0.0.1 и 3.0.0.2 пакета SDK служб мультимедиа Azure для .NET].

В версии 3.0.0.3 внесены следующие изменения.

* Зависимости хранилища Azure обновлены для использования версии 3.0.3.0. 
* Исправлена проблема совместимости с предыдущими выпусками 3.0.*.* . 

## <a name="a-iddecemberchanges13adecember-2013-release"></a><a id="december_changes_13"></a>Выпуск: декабрь 2013 г.
### <a name="a-namedec13donnetchangesaazure-media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>Выпуск 3.0.0.0 пакета SDK служб мультимедиа Azure для .NET
> [!NOTE]
> Версии 3.0.x.x несовместимы с версиями 2.4.x.x.
> 
> 

Последним выпуском пакета SDK для служб мультимедиа в данный момент является 3.0.0.0. Последнюю версию пакета можно скачать из Nuget или получить код из [GitHub].

Приступив к работе с пакетом SDK версии 3.0.0.0 для служб мультимедиа, вы можете повторно использовать токены [службы контроля доступа Azure Active Directory (ACS)]. Дополнительные сведения см. в разделе "Повторное использование маркеров службы управления доступом" статьи [Подключение к службам мультимедиа с помощью пакета SDK служб мультимедиа для .NET].

### <a name="a-namedec13donnetextchangesaazure-media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>Расширения версии 2.0.0.0 пакета SDK служб мультимедиа Azure для .NET
Расширения пакета SDK служб мультимедиа Azure для .NET — это набор методов расширения и вспомогательных функций, которые упростят код и облегчат разработку приложений с помощью служб мультимедиа Azure. Новые фрагменты кода можно получить на странице [Расширения пакета SDK служб мультимедиа Azure для .NET].

## <a name="a-idnovemberchanges13anovember-2013-release"></a><a id="november_changes_13"></a>Выпуск: ноябрь 2013 г.
### <a name="a-namenov13donnetchangesaazure-media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>Изменения в пакете SDK служб мультимедиа Azure для .NET
Начиная с этой версии, пакет SDK служб мультимедиа для .NET обрабатывает временные ошибки, которые могут возникать при вызове уровня API-интерфейсов REST служб мультимедиа.

## <a name="a-idaugustchanges13aaugust-2013-release"></a><a id="august_changes_13"></a>Выпуск: август 2013 г.
### <a name="a-nameaug13powershellchangesamedia-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>Командлеты PowerShell служб мультимедиа включены в инструменты SDK Azure
Следующие командлеты PowerShell служб мультимедиа включены в [azure-sdk-tools].

* Get-AzureMediaServices 
  
    Пример: `Get-AzureMediaServicesAccount`.
* New-AzureMediaServicesAccount 
  
    Пример: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.
* New-AzureMediaServicesKey 
  
    Пример: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.
* Remove-AzureMediaServicesAccount 
  
    Пример: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

## <a name="a-idjunechanges13ajune-2013-release"></a><a id="june_changes_13"></a>Выпуск: июнь 2013 г.
### <a name="a-namejune13generalchangesaazure-media-services-changes"></a><a name="june_13_general_changes"></a>Изменения в службах мультимедиа Azure
Изменения, перечисленные в этом разделе — это обновления, включенные в выпуски служб мультимедиа в июне 2013 г.

* Возможность связать несколько учетных записей хранилища с учетной записью служб мультимедиа. 
  
    StorageAccount
  
    Asset.StorageAccountName и Asset.StorageAccount
* Возможность обновления Job.Priority. 
* Сущности и свойства, связанные с уведомлением: 
  
    JobNotificationSubscription
  
    NotificationEndPoint
  
    Задание
* Asset.Uri 
* Locator.Name 

### <a name="a-namejune13dotnetchangesaazure-media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>Изменения в пакете SDK служб мультимедиа Azure для .NET
В выпуски пакета SDK служб мультимедиа в июне 2013 г. включены следующие изменения. Последняя версия пакета SDK служб мультимедиа доступна в GitHub.

* Начиная с версии 2.3.0.0, пакет SDK служб мультимедиа поддерживает связь нескольких учетных записей хранилища с учетной записью служб мультимедиа. Эту функцию поддерживают следующие API-интерфейсы:
  
    Тип IStorageAccount.
  
    Свойство Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
  
    Свойство StorageAccount.
  
    Свойство StorageAccountName.
  
    Дополнительные сведения см. в статье [Управление активами служб мультимедиа в нескольких учетных записях хранения].
* API-интерфейсы, связанные с уведомлениями. Начиная с версии 2.2.0.0, можно прослушивать уведомления хранилища очередей Azure. Дополнительные сведения см. в статье [Обработка уведомлений из заданий служб мультимедиа].
  
    Свойство Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
  
    Тип Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
  
    Тип Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
  
    Тип Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
  
    Тип Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
  
    Тип Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.
* Зависимость от пакета SDK клиента хранилища Azure 2.0 (Microsoft.WindowsAzure.StorageClient.dll).
* Зависимость от OData 5.5 (Microsoft.Data.OData.dll).

## <a name="a-iddecemberchanges12adecember-2012-release"></a><a id="december_changes_12"></a>Выпуск: декабрь 2012 г.
### <a name="a-namedec12dotnetchangesaazure-media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>Изменения в пакете SDK служб мультимедиа Azure для .NET
* Intellisense. Добавлена отсутствующая документация Intellisense по многим типам.
* Microsoft.Practices.TransientFaultHandling.Core: исправлена проблема зависимости пакета SDK от прежней версии этой сборки. Пакет SDK теперь указывает на версию 5.1.1209.1 этой сборки.

Исправлены проблемы, обнаруженные в выпусках пакета SDK за ноябрь 2012 г.

* IAsset.Locators.Count. Теперь этот счетчик правильно показывает данные по новым интерфейсам IAsset после удаления всех локаторов.
* IAssetFile.ContentFileSize. Теперь это значение устанавливается правильно после отправки из IAssetFile.Upload(путь к файлу).
* IAssetFile.ContentFileSize. Теперь это свойство можно задать при создании файла актива. Ранее оно было доступно только для чтения.
* IAssetFile.Upload(filepath). Исправлена проблема, при которой этот метод синхронной отправки создавал следующую ошибку при отправке в актив нескольких файлов. Ошибка: "Server failed to authenticate the request. Make sure the value of Authorization header is formed correctly including the signature" (Серверу не удалось проверить подлинность запроса. Проверьте правильность формата значения в заголовке "Авторизация", включая подпись).
* IAssetFile.UploadAsync. Исправлена проблема, при которой не удавалось одновременно отправить более 5 файлов.
* IAssetFile.UploadProgressChanged. Это событие теперь предоставляется пакетом SDK.
* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken). Теперь предоставляется перезагрузка этого метода.
* IAssetFile.UploadAsync. Исправлена проблема, при которой не удавалось одновременно загрузить более 5 файлов.
* IAssetFile.Delete(). Исправлена проблема, при которой вызов функции удаления мог создать исключение, если отсутствуют файлы, отправленные в IAssetFile.
* Jobs. Исправлена проблема, при которой объединение задач "MP4 to Smooth Streams task" и "PlayReady Protection Task" с помощью шаблона заданий не заканчивалось созданием задачи.
* EncryptionUtils.GetCertificateFromStore(). Этот метод больше не создает исключение о пустой ссылке, связанное со сбоем при поиске сертификата в связи с ошибками конфигурации сертификата.

## <a name="a-idnovemberchanges12anovember-2012-release"></a><a id="november_changes_12">Выпуск: ноябрь 2012 г.</a>
Изменения, указанные в этом разделе — это обновления, включенные в пакет SDK в ноябре 2012 г. (версия 2.0.0.0). В связи с этими изменениями может потребоваться внести изменения или переработать код, созданный для предварительного выпуска пакета SDK за июнь 2012 г.

* Активы
  
    IAsset.Create(имяАктива) — ЕДИНСТВЕННАЯ функция создания активов. IAsset.Create больше не отправляет файлы в составе вызова метода. Для отправки используйте функцию IAssetFile.
  
    Метод IAsset.Publish и перечисляемое значение AssetState.Publish удалены из пакета SDK служб. Необходимо переработать код, связанный с этим значением.
* FileInfo
  
    Этот класс удален и заменен классом IAssetFile.
  
    IAssetFiles
  
    IAssetFile заменяет класс FileInfo и используется иначе. Для его использования создайте экземпляр объекта IAssetFiles, а затем отправьте файл с помощью пакета SDK служб мультимедиа или пакета SDK хранилища Azure. Можно использовать следующие перезагрузки IAssetFile.Upload.
  
  * IAssetFile.Upload(filePath). Синхронный метод, который блокирует поток и рекомендуется только для отправки отдельного файла.
  * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken). Асинхронный метод. Это рекомендуемый механизм отправки. 
    
    Известная ошибка: при использовании cancellationToken отправка будет отменена; однако состояние отмены для задач может быть любым. Необходимо правильно перехватывать и обрабатывать исключения.
* Локаторы
  
    Удалены версии для отдельных источников. Метод context.Locators.CreateSasLocator(asset, accessPolicy) для SAS будет помечен как устаревший или удален общедоступным выпуском. Сведения о новом поведении см. в подразделе "Локаторы" раздела "Новые функции".

## <a name="a-idjunechanges12ajune-2012-preview-release"></a><a id="june_changes_12"></a>Предварительный выпуск: июнь 2012 г.
В ноябрьском выпуске пакета SDK добавлена следующая новая функциональность.

* Удаление сущностей
  
    Объекты IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey теперь удаляются на уровне объектов, например IObject.Delete(), вместо удаления в коллекции, то есть cloudMediaContext.ObjCollection.Delete(objInstance).
* Локаторы
  
    Теперь локаторы необходимо создавать с помощью метода CreateLocator и использовать перечисляемые значения LocatorType.SAS или LocatorType.OnDemandOrigin в качестве аргумента для определенного типа создаваемого локатора.
  
    В локаторы добавлены новые свойства, облегчающие получение доступных идентификаторов URI для контента. Изменения, внесенные в локаторы, увеличивают гибкость для последующих расширений сторонних разработчиков и облегчают использование для клиентских приложений мультимедиа.
* Поддержка асинхронных методов
  
    Поддержка асинхронного выполнения добавлена для всех методов.

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[форуме MSDN по службам мультимедиа Azure]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[этом справочнике]: http://msdn.microsoft.com/library/azure/hh973617.aspx 
[цен на службы мультимедиа]: http://azure.microsoft.com/pricing/details/media-services/
[входных]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[выходных метаданных]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[доставке содержимого]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Индексирование файлов мультимедиа с помощью Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Общие сведения о динамической потоковой передаче с использованием служб мультимедиа Azure]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Использование динамического шифрования AES-128 и службы доставки ключей]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Использование общего динамического шифрования PlayReady и (или) Widevine DRM]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Функции предварительной версии]: http://azure.microsoft.com/services/preview/
[Обзор шаблонов лицензий PlayReady служб мультимедиа]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Настройка политик доставки ресурсов-контейнеров с помощью пакета SDK для .NET]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Классический портал Azure]: https://manage.windowsazure.com
[динамическую упаковку]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[блоге Ника Дроуина (Nick Drouin)]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[защите потоков Smooth Stream с помощью PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Логика повторных попыток в пакете SDK служб мультимедиа для .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley представляет потоковую передачу EDIUS 7 в облаке]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Управление именами выходных файлов кодировщика служб мультимедиа]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Создание наложений]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Стыковка сегментов видео]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Выпуски 3.0.0.1 и 3.0.0.2 пакета SDK служб мультимедиа Azure для .NET]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[службы контроля доступа Azure Active Directory (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[Подключение к службам мультимедиа с помощью пакета SDK служб мультимедиа для .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Расширения пакета SDK служб мультимедиа Azure для .NET]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Управление активами служб мультимедиа в нескольких учетных записях хранения]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Обработка уведомлений из заданий служб мультимедиа]: http://msdn.microsoft.com/library/azure/dn261241.aspx




<!--HONumber=Nov16_HO3-->


