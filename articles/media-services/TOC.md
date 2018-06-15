# [Документация по Службам мультимедиа](index.md)

# [Обзор](media-services-overview.md)
## [Сценарии и доступность](scenarios-and-availability.md)
## [Основные понятия](media-services-concepts.md)

# Начало работы
## [Создание учетной записи и управление ею](media-services-portal-create-account.md)
## [Настройка среды разработки](media-services-set-up-computer.md)
### [.NET](media-services-dotnet-how-to-use.md)
### [REST](media-services-rest-how-to-use.md)  
## [Использование аутентификации AAD для доступа к API](media-services-use-aad-auth-to-access-ams-api.md)
### [Использование портала для управления аутентификацией AAD](media-services-portal-get-started-with-aad.md)
### [Доступ к API с помощью .NET](media-services-dotnet-get-started-with-aad.md)
### [Доступ к API с помощью REST](media-services-rest-connect-with-aad.md)
### [Использование интерфейса командной строки Azure для создания и настройки приложения AAD](media-services-cli-create-and-configure-aad-app.md)
### [Использование Azure PowerShell для создания и настройки приложения AAD](media-services-powershell-create-and-configure-aad-app.md)

## Предоставление видео по запросу
### [портал Azure](media-services-portal-vod-get-started.md)
### [ПАКЕТ SDK .NET](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## Потоковая трансляция в реальном времени
### [портал Azure](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# Практическое руководство
## управление
### Сущности
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [Конечные точки потоковой передачи](media-services-streaming-endpoints-overview.md)
#### [портал Azure](media-services-portal-manage-streaming-endpoints.md)
#### [.NET](media-services-dotnet-manage-streaming-endpoints.md)
### Хранилище
#### [Обновление служб мультимедиа после оборота ключей доступа к хранилищу](media-services-roll-storage-access-keys.md)
#### [Управление ресурсами в нескольких учетных записях хранения](meda-services-managing-multiple-storage-accounts.md)
### [Квоты и ограничения](media-services-quotas-and-limitations.md)
## [Настройка Postman](media-rest-apis-with-postman.md)
### [Коллекция для операций потокового воспроизведения по запросу](postman-collection.md)
### [Коллекция для операций потоковой трансляции в прямом эфире](postman-live-streaming-collection.md)
### [Среда](postman-environment.md)
## Передача содержимого
### Отправка файлов в учетную запись
#### [портал Azure](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [Отправка больших файлов с помощью Aspera](media-services-upload-files-with-aspera.md)
### [Отправка файлов с помощью StorSimple](media-services-upload-files-from-storsimple.md)
### [Копирование существующих больших двоичных объектов](media-services-copying-existing-blob.md)

## [Кодирование содержимого](media-services-encode-asset.md)
### [Сравнение кодировщиков](media-services-compare-encoders.md)
### [Управление скоростью и параллелизмом кодирования](media-services-manage-encoding-speed.md)
### Media Encoder Standard (MES)
#### [Форматы и кодеки Media Encoder Standard](media-services-media-encoder-standard-formats.md)
#### [Автоматическое создание схемы скоростей с помощью MES](media-services-autogen-bitrate-ladder-with-mes.md)
#### Кодирование с помощью Media Encoder Standard
##### [портал Azure](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
##### [REST](media-services-rest-encode-asset.md)
#### [Расширенное кодирование с помощью MES](media-services-advanced-encoding-with-mes.md)
##### [Настройка предустановок Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
##### [Создание эскизов с помощью Media Encoder Standard c использованием .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
##### [Обрезка видео с помощью стандартного кодировщика мультимедиа](media-services-crop-video.md)
#### Схемы MES
##### [Схема Media Encoder Standard](media-services-mes-schema.md)
##### [Входные метаданные](media-services-input-metadata-schema.md)
##### [Выходные метаданные](media-services-output-metadata-schema.md)
#### [Предустановки Media Encoder Standard](media-services-mes-presets-overview.md) 
##### [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md)
##### [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md)
##### [H264 Multiple Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md)
##### [H264 Multiple Bitrate 16x9 для iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md)
##### [H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md)
##### [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md)
##### [H264 Multiple Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md)
##### [H264 Multiple Bitrate 4x3 для iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md)
##### [H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md)
##### [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)
##### [H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md)
##### [H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md)
##### [H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md)
##### [H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md)
##### [H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md)
##### [H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md)
##### [H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md)
##### [H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md)
##### [H264 Single Bitrate 720p для Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md)
##### [H264 Single Bitrate High Quality SD для Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md)
##### [H264 Single Bitrate Low Quality SD для Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md)
### Расширенный рабочий процесс кодировщика мультимедиа
#### [Форматы и кодеки рабочего процесса Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)
#### Кодирование с помощью рабочего процесса Media Encoder Premium
##### [Рабочий процесс Media Encoder Premium](media-services-encode-with-premium-workflow.md)
##### [Руководства по рабочему процессу Media Encoder Premium](media-services-media-encoder-premium-workflow-tutorials.md)
##### [Создание рабочих процессов расширенной кодировки с помощью конструктора рабочих процессов](media-services-workflow-designer.md)
##### [Рабочий процесс Premium с несколькими элементами входных данных](media-services-media-encoder-premium-workflow-multiplefilesinput.md)
### [Создание задачи, формирующей блоки fMP4](media-services-generate-fmp4-chunks.md)
### Обработчики мультимедиа
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)
### [Коды ошибок](media-services-encoding-error-codes.md)
### Не рекомендуется
#### [Статическая упаковка и шифрование](media-services-static-packaging.md)

## [Потоковая трансляция в реальном времени](media-services-manage-channels-overview.md)
### [Локальные кодировщики](media-services-live-streaming-with-onprem-encoders.md)
#### [Рекомендуемые локальные кодировщики](media-services-recommended-encoders.md)
#### [портал Azure](media-services-portal-live-passthrough-get-started.md)
#### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
### [Потоковая трансляция с использованием облачного кодировщика](media-services-manage-live-encoder-enabled-channels.md)
#### [портал Azure](media-services-portal-creating-live-encoder-enabled-channel.md)
#### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
### [Настройка локальных кодировщиков для использования с облачными кодировщиками](media-services-live-encoders-overview.md)
#### [Кодировщик Elemental Live](media-services-configure-elemental-live-encoder.md)
#### [Кодировщик FMLE](media-services-configure-fmle-live-encoder.md)
#### [Кодировщик Haivision KB](media-services-configure-kb-live-encoder.md)
#### [Кодировщик NewTek TriCaster](media-services-configure-tricaster-live-encoder.md)
#### [Кодировщик Wirecast](media-services-configure-wirecast-live-encoder.md)
### [Обработка длительных операций](media-services-dotnet-long-operations.md)
### [Спецификация Fragmented MP4 Live Ingest](media-services-fmp4-live-ingest-overview.md)

## [Обрезка содержимого](media-services-azure-media-clipper-overview.md)
### [Приступая к работе](media-services-azure-media-clipper-getting-started.md)
### [Загрузка видео](media-services-azure-media-clipper-load-assets.md)
### [Настройка сочетаний клавиш](media-services-azure-media-clipper-keyboard-shortcuts.md)
### [Настройка локализации](media-services-azure-media-clipper-localization.md)
### [Отправка заданий обрезки](media-services-azure-media-clipper-submit-job.md)
### [портал Azure](media-services-azure-media-clipper-portal.md)

## [Защита содержимого](media-services-content-protection-overview.md)
### [Шифрование хранилища](media-services-rest-storage-encryption.md)
### [Шифрование AES-128](media-services-protect-with-aes128.md)
### [Защита потокового содержимого с помощью PlayReady или Widevine](media-services-protect-with-playready-widevine.md)
### [Защита потокового содержимого с помощью FairPlay](media-services-protect-hls-with-fairplay.md)
### [PlayReady для Windows 10 (автономный режим)](https://blogs.msdn.microsoft.com/playready4/2016/10/26/does-azure-media-services-support-offline-mode/)
### [FairPlay для iOS (автономный режим)](media-services-protect-hls-with-offline-fairplay.md)
### [Widevine для Android (автономный режим)](offline-widevine-for-android.md)
### [Настройка на портале Azure](media-services-portal-protect-content.md)
### [Доставка лицензий DRM](media-services-deliver-keys-and-licenses.md)
### Создание ContentKey
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### Общие сведения о шаблонах лицензий
#### [Шаблон лицензии PlayReady](media-services-playready-license-template-overview.md)
#### [Шаблон лицензии Widevine](media-services-widevine-license-template-overview.md)
### Настройка политик доставки ресурсов
#### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
#### [REST](media-services-rest-configure-asset-delivery-policy.md)
### Настройка политики авторизации ключей содержимого
#### [портал Azure](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)
### [Передача маркеров проверки подлинности в AMS](media-services-pass-authentication-tokens.md)
### Образцы структуры
#### [Гибридная структура подсистем DRM](hybrid-design-drm-sybsystem.md)
#### [Образец структуры с несколькими подсистемами DRM](media-services-cenc-with-multidrm-access-control.md)

## [Анализ](media-services-analytics-overview.md)
### [Анализ мультимедиа с помощью портала Azure](media-services-portal-analyze.md)
### [Обработка с помощью индексатора 2](media-services-process-content-with-indexer2.md)
### [Обработка с помощью индексатора](media-services-index-content.md)
#### [Предустановка задачи](indexer-task-preset.md)
### [Обработка с помощью Hyperlapse](media-services-hyperlapse-content.md)
### [Обработка с помощью Face Detector](media-services-face-and-emotion-detection.md)
### [Обработка с помощью Motion Detector](media-services-motion-detection.md)
### [Обработка с помощью скрытия лиц](media-services-face-redaction.md)
#### [Пошаговое руководство по скрытию лиц](media-services-redactor-walkthrough.md)
### [Обработка с помощью эскизов видео](media-services-video-summarization.md)
### [Обработка с помощью распознавания текста](media-services-video-optical-character-recognition.md)
### [Использование Content Moderator](media-services-content-moderation.md)

## [Настройка телеметрии](media-services-telemetry-overview.md)
###[.NET](media-services-dotnet-telemetry.md)
###[REST](media-services-rest-telemetry.md)

## Масштаб
### [Обработка мультимедиа](media-services-scale-media-processing-overview.md)
#### [портал Azure](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
### Конечные точки потоковой передачи
#### [портал Azure](media-services-portal-scale-streaming-endpoints.md)

## [Доставка содержимого](media-services-deliver-content-overview.md)
### [Динамическая упаковка](media-services-dynamic-packaging-overview.md)
### [Обзор фильтров и динамических манифестов](media-services-dynamic-manifest-overview.md)
#### [Создание фильтров с помощью .NET](media-services-dotnet-dynamic-manifest.md)
#### [Создание фильтров с помощью REST](media-services-rest-dynamic-manifest.md)
### [Политика кэширования CDN в расширении служб мультимедиа](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### Публикация содержимого
#### [портал Azure](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
### [Доставка путем скачивания](media-services-deliver-asset-download.md)
### [Сценарий потоковой передачи отработки отказа](media-services-implement-failover.md)

## Использование
### [Воспроизведение мультимедиа с помощью существующих проигрывателей](media-services-playback-content-with-existing-players.md)
### [Воспроизведение мультимедиа с помощью Проигрывателя мультимедиа](media-services-develop-video-players.md)
### Другие параметры воспроизведения
#### [Приложение Магазина Windows с потоковой передачей Smooth Streaming](media-services-build-smooth-streaming-apps.md)
#### [Приложение HTML5 с DASH.js](media-services-embed-mpeg-dash-in-html5.md)
#### [Проигрыватели Adobe Open Source Media Framework](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [Вставка рекламы на стороне клиента](media-services-inserting-ads-on-client-side.md)
### [Лицензирование пакета для переноса клиента потоковой передачи Microsoft Smooth Streaming](media-services-sspk.md)

## Интеграция
### [Использование функций Azure и служб мультимедиа](media-services-dotnet-how-to-use-azure-functions.md)
### [Использование функций Azure и примеров служб мультимедиа](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

## Мониторинг
### Проверка хода выполнения задания
#### [REST](media-services-rest-check-job-progress.md)
#### [портал Azure](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [Мониторинг уведомлений о заданиях с помощью хранилища очередей](media-services-dotnet-check-job-progress-with-queues.md)
### [Мониторинг уведомлений о заданиях с помощью вызовов webhook](media-services-dotnet-check-job-progress-with-webhooks.md)

## Устранение неполадок
### [Часто задаваемые вопросы](media-services-frequently-asked-questions.md)
### [Руководство по устранению неполадок потоковой трансляции](media-services-troubleshooting-live-streaming.md)
### [Коды ошибок](media-services-error-codes.md)
### [Логика повтора](media-services-retry-logic-in-dotnet-sdk.md)

# Справочные материалы
## [Примеры кода](https://azure.microsoft.com/resources/samples/?service=media-services)
## [Azure PowerShell (Resource Manager)](/powershell/module/azurerm.media)
## [Azure PowerShell (управление службами)](/powershell/module/azure/?view=azuresmps-3.7.0)
## [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client)
## [REST](/rest/api/media/mediaservice)
## спецификации;
### [Прием в реальном времени: спецификация приема фрагментированного MP4 в реальном времени](media-services-fmp4-live-ingest-overview.md)
### [Прием в реальном времени: сигнализация метаданных времени в потоковой трансляции](media-services-specifications-live-timed-metadata.md)
### [Smooth Streaming для HEVC](media-services-specifications-ms-sstr-amendment-hevc.md)

# Ресурсы
## [Сообщество служб мультимедиа Azure](media-services-community.md)
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/?category=web-mobile)
## [Цены](https://azure.microsoft.com/pricing/details/media-services/)
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Заметки о выпуске](media-services-release-notes.md)
## [Видеоролики](https://azure.microsoft.com/resources/videos/index/?services=media-services)
