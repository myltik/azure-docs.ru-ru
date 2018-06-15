---
title: Начало работы c Azure Media Clipper | Документация Майкрософт
description: Начало работы с Azure Media Clipper — инструментом для создания видеоклипов из ресурсов AMS
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ac64d97aeeef6147aa62658c9ee440bf058f4db1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783293"
---
# <a name="create-clips-with-azure-media-clipper"></a>Создание клипов с помощью Azure Media Clipper
В этом разделе приведены основные этапы по началу работы с Azure Media Clipper. В следующих разделах содержатся подробные сведения по настройке Azure Media Clipper.

- Сначала добавьте следующие ссылки для Проигрывателя мультимедиа Azure и Azure Media Clipper в заголовок документа. Рекомендуется явно указывать версию Clipper и Проигрывателя мультимедиа Azure в URL-адресах. Не используйте последнюю версию этих ресурсов в рабочей среде, так как они могут быть изменены по требованию.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Затем добавьте следующие классы в элемент div, где необходимо создать экземпляр Clipper.

```javascript
<div id="root" class="azure-subclipper" />
```

Если требуется включить темную тему, добавьте класс темной обложки:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Затем создайте экземпляр Clipper с помощью следующего вызова API:

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

Ниже приведены параметры, используемые для вызова метода инициализации:
- `selector` {обязательный, строка}. Селектор каскадных таблиц стилей совпадающего элемента HTML, где необходимо отобразить мини-приложение;
- `restVersion` {обязательный, строка}. Требуемая версия REST API служб мультимедиа Azure. Версия REST определяет формат выходных данных, создаваемых мини-приложением. В настоящее время поддерживается только версия 2.0.
- `submitSubclipCallback` {обязательный, обещание}. Функция обратного вызова, вызываемая при нажатии в мини-приложении кнопки "Отправить". Функция обратного вызова должна ожидать выходные данные, создаваемые мини-приложением (конфигурация задания преобразования для просмотра или определение фильтра). Дополнительные сведения см. в разделе об отправке обратного вызова субклипа.
- `logLevel` {необязательный, {"info", "warn", "error"}}. Уровень ведения журнала, отображаемый в консоли браузера. Значение по умолчанию: error.
- `minimumMarkerGap` {необязательный, целое}. Минимальный размер субклипа (в секундах). Примечание. Значение должно быть больше или равно 6, которое также используется по умолчанию.
- `singleBitrateMp4Profile` {необязательный, объект JSON}. Односкоростной профиль MP4, используемый для конфигурации задания преобразования для просмотра, созданной мини-приложением. Если не указано, используется [односкоростной профиль MP4 по умолчанию](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile` {необязательный, объект JSON}. Многоскоростной профиль MP4, используемый для конфигурации задания преобразования для просмотра, созданной мини-приложением. Если не указано, используется [многоскоростной профиль MP4 по умолчанию](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap` {необязательный, объект json}. Позволяет настраивать сочетания клавиш мини-приложения. Дополнительные сведения см. в статье [Configure Azure Media Clipper keyboard shortcuts](media-services-azure-media-clipper-keyboard-shortcuts.md) (Настройка сочетания клавиш Azure Media Clipper).
- `assetsPanelLoaderCallback` {необязательный, обещание}. Функция обратного вызова, которая вызывается для загрузки (асинхронно) новой страницы ресурсов на панели "Ресурсы" каждый раз, когда пользователь прокручивает к нижней части области. Дополнительные сведения см. в разделе об обратном вызове загрузчика панели ресурсов.
- `height` {необязательный, число}. Общая высота мини-приложения (минимальная высота составляет 600 пикселей без панели ресурсов и 850 пикселей с панелью ресурсов).
- `subclippingMode` (необязательный, {"all", "render", "filter"}). Разрешенные режимы работы с субклипами. По умолчанию используется значение all.
- `filterAssetsTypes` (необязательный, логическое значение). filterAssetsTypes позволяет показать или скрыть раскрывающийся список фильтров на панели ресурсов. По умолчанию используется значение true.
- `speedLevels` (необязательно, массив). speedLevels позволяет настроить уровни с различной скоростью для видеопроигрывателя. Дополнительные сведения см. в документации по [Проигрывателю мультимедиа](http://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions).
- `resetOnJobDone` (необязательный, логическое значение): resetOnJobDone позволяет Clipper выполнить сброс субклипа до исходного состояния после успешного выполнения задания.
- `autoplayVideo` (необязательный, логическое значение). autoplayVideo позволяет Clipper автоматически воспроизводить видео при загрузке. По умолчанию используется значение true.
- `language` {необязательный, строка}. Задает язык для мини-приложения. Если не указан, мини-приложение пытается локализовать сообщения на языке браузера. Если язык не обнаруживается в браузере, мини-приложение по умолчанию будет использовать английский. Дополнительные сведения см. в статье [Configure localization](media-services-azure-media-clipper-localization.md) (Настройка локализации).
- `languages` {необязательный, JSON}. Параметр languages заменяет словарь языков по умолчанию словарем, который определяется пользователем. Дополнительные сведения см. в статье [Configure localization](media-services-azure-media-clipper-localization.md) (Настройка локализации).
- `extraLanguages` (необязательный, JSON). Параметр extraLanaguages добавляет новые языки в словарь по умолчанию. Дополнительные сведения см. в статье [Configure localization](media-services-azure-media-clipper-localization.md) (Настройка локализации).

## <a name="typescript-definition"></a>Определение TypeScript
Файл определения [TypeScript](https://www.typescriptlang.org/) для Clipper можно найти [здесь](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>API Azure Media Clipper
В данном разделе описывается контактная зона API, предоставляемая Clipper.

- `ready(handler)`. Предлагает способ запуска JavaScript, как только Clipper будет полностью загружен и готов для использования.
- `load(assets)`. Загружает список ресурсов в область временной шкалы мини-приложения (не следует использовать вместе с assetsPanelLoaderCallback). Дополнительные сведения о загрузке ресурсов в Clipper см. в [этой статье](media-services-azure-media-clipper-load-assets.md).
- `setLogLevel(level)`. Задает уровень ведения журнала, который будет отображаться в консоли браузера. Возможные значения: `info`, `warn`, `error`.
- `setHeight(height)`. Задает общую высоту мини-приложения в пикселях (минимальная высота составляет 600 пикселей без панели ресурсов и 850 пикселей с панелью ресурсов).
- `version`. Получает версию мини-приложения.

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь со следующими шагами по настройке Azure Media Clipper:
- [Loading assets into Azure Media Clipper](media-services-azure-media-clipper-load-assets.md) (Загрузка ресурсов в Azure Media Clipper)
- [Configure Azure Media Clipper keyboard shortcuts](media-services-azure-media-clipper-keyboard-shortcuts.md) (Настройка пользовательских сочетаний клавиш Azure Media Clipper)
- [Submit clipping jobs from Azure Media Clipper](media-services-azure-media-clipper-submit-job.md) (Отправка заданий обрезки из Azure Media Clipper)
- [Configuring localization](media-services-azure-media-clipper-localization.md) (Настройка локализации)