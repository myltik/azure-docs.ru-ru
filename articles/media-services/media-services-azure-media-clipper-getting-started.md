---
title: "Начало работы c Azure Media Clipper | Документация Майкрософт"
description: "Начало работы с Azure Media Clipper — инструмента для создания медиаклипов из ресурсов"
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8a4f2c79131664ca0d078fa58c6a75b54243e705
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
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
- `language` {необязательный, строка}. Задает язык для мини-приложения. Если не указан, мини-приложение пытается локализовать сообщения на языке браузера. Если язык не обнаруживается в браузере, мини-приложение по умолчанию будет использовать английский. Дополнительные сведения см. в разделе поддерживаемых языков.
- `languages` {необязательный, JSON}. Параметр languages заменяет словарь языков по умолчанию словарем, который определяется пользователем. Дополнительные сведения см. в разделе поддерживаемых языков.
- `extraLanguages` (необязательный, JSON). Параметр extraLanaguages добавляет новые языки в словарь по умолчанию. Дополнительные сведения см. в разделе поддерживаемых языков.

## <a name="typescript-definition"></a>Определение TypeScript
Файл определения [TypeScript](https://www.typescriptlang.org/) для Clipper можно найти [здесь](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>API Azure Media Clipper
В данном разделе описывается контактная зона API, предоставляемая Clipper.

- `load(assets)`. Загружает список ресурсов в область ресурсов (не следует использовать вместе с `assetsPanelLoaderCallback`). Дополнительные сведения о загрузке ресурсов в Clipper см. в [этой статье](media-services-azure-media-clipper-load-assets.md).
- `setLogLevel(level)`. Задает уровень ведения журнала, который будет отображаться в консоли браузера. Возможные значения: `info`, `warn`, `error`.
- `setHeight(height)`. Задает общую высоту мини-приложения в пикселях (минимальная высота составляет 600 пикселей без панели ресурсов и 850 пикселей с панелью ресурсов).
- `version`. Получает версию мини-приложения.

## <a name="configuring-azure-media-clipper"></a>Настройка Azure Media Clipper
Ознакомьтесь со следующими шагами по настройке Azure Media Clipper:
- [Loading assets into Azure Media Clipper](media-services-azure-media-clipper-load-assets.md) (Загрузка ресурсов в Azure Media Clipper)
- [Configure Azure Media Clipper keyboard shortcuts](media-services-azure-media-clipper-keyboard-shortcuts.md) (Настройка пользовательских сочетаний клавиш Azure Media Clipper)
- [Submit clipping jobs from Azure Media Clipper](media-services-azure-media-clipper-submit-job.md) (Отправка заданий обрезки из Azure Media Clipper)

## <a name="supported-languages"></a>Поддерживаемые языки
Мини-приложение Clipper доступно на 18 языках. Чтобы задать язык мини-приложения, необходимо определить параметр `language` во время инициализации. Передайте строку кода нужного языка из следующего списка:
- китайский (упрощенное письмо): zh-hans;
- китайский (традиционное письмо): zh-hant;
- чешский: cs;
- нидерландский, фламандский: nl;
- английский: en;
- французский: fr;
- немецкий: de;
- венгерский: hu;
- итальянский: it;
- японский: ja;
- корейский: ko;
- польский: pl;
- португальский (Бразилия): pt-BR;
- португальский (Португалия): pt-pt;
- русский: ru;
- испанский: es;
- шведский: sv;
- турецкий: tr.

Чтобы задать пользовательский словарь языка или расширить словарь языка по умолчанию, необходимо определить параметры `languages` или `extraLanguages` соответственно. Передайте пользовательский словарь, используя следующий формат JSON:

```javascript
{
      "{language-code}":
          "{message-id}": "{message}"
          ...
      }
      ...
}
```

Например, следующий пример определяет локализованные строки на английском языке:

```javascript
export default {
  'VideoPlayer.noPreview': 'No video preview',
  'VideoPlayer.loadAsset': 'You must provide a valid asset',
  'AssetsPanel.name': 'Name',
  'AssetsPanel.type': 'Asset type',
  'AssetsPanel.actions': 'Actions',
  'AssetsPanel.loading': 'Loading...',
  'AssetsPanel.duration': 'Duration',
  'AssetsPanel.resolution': 'Resolution',
  'AssetsPanel.pluralFiles': '{0} assets',
  'AssetsPanel.searchFiles': 'Search assets',
  'AssetsPanel.showTypes': 'Show:',
  'AssetsPanel.typesInfo': 'Rendered assets are actual MP4 files. Dynamic manifest filters are filters applied to a parent asset\'s video segment playlist.',
  'AssetsPanel.filterTypes': 'Filters',
  'AssetsPanel.assetTypes': 'Assets',
  'AssetsPanel.assetsAll': 'All',
  'AssetsPanel.addAsset': 'Add asset to the end',
  'AssetsPanel.addFilter': 'Add filter to the timeline',
  'AssetsPanel.invalidAsset': 'The metadata of this asset is not compatible with the other assets in the timeline',
  'AssetsPanel.addAssetWarning': 'Subclipping on assets with different resolutions may cause resolution autoscaling.',
  'AssetsPanel.live': 'LIVE',
  'AssetsPanel.unknown': 'UNKNOWN',
  'AssetsPanel.minimGapNotMet': 'The asset duration must be greater than the minimum clip duration ({0} seconds)',
  'VideoPlayer.openAdvancedSettings': 'Advanced settings',
  'VideoPlayer.singleBitrate': 'Single-bitrate MP4 (rendered)',
  'VideoPlayer.multiBitrate': 'Multi-bitrate MP4 (rendered)',
  'VideoPlayer.dynamicManifest': 'Dynamic manifest filter',
  'VideoPlayer.ErrorWithMessage': 'There was an error in the video player, code {0}, message: {1}',
  'Common.cancel': 'Cancel',
  'Common.OK': 'OK',
  'AdvancedSettings.framerate': 'Frame rate',
  'Dropdown.select': 'Select an option...',
  'InputAsset.RemoveInput': 'Remove source',
  'Zoom.startTime': 'Start time',
  'Zoom.endTime': 'End time',
  'VideoPlayer.subclips': 'Subclips:',
  'VideoPlayer.length': 'Clip length:',
  'Accordion.scrollLeft': 'Scroll to the left',
  'Accordion.scrollRight': 'Scroll to the right',
  'AdvancedSettings.title': 'Advanced settings',
  'AdvancedSettings.subclipName': 'Subclip name',
  'AdvancedSettings.subclipType': 'Subclipping mode',
  'AdvancedSettings.includeAudioTracks': 'Include audio tracks',
  'AdvancedSettings.subclipTypeInfo': 'Single-bitrate and multi-bitrate MP4s are frame accurate rendered assets. Dynamic manifest filters are group-of-pictures (GOP) accurate filters applied to a parent asset. Creating filters does not create a new asset and does not require encoding. Subclipping jobs on live assets are valid as long as their mark times are within the archive window of the parent asset. Filters are valid as long as the parent asset exists and mark times are within its archive window.',
  'AdvancedSettings.frameRateInfo': 'We autodetect frame rate under most scenarios. however, If we cannot autodetect, choose a frame rate from the dropdown for the selected asset(s).',
  'AdvancedSettings.frameRateError': 'Unable to determine frame rate',
  'AdvancedSettings.subclipNameInfo': 'Choose a name for your subclip.',
  'AdvancedSettings.singleAudioTrack': '1 audio track selected',
  'AdvancedSettings.allAudioTracks': 'All audio tracks selected',
  'AdvancedSettings.someAudioTracks': '{0} audio tracks selected',
  'AdvancedSettings.includeAllAudioTracks': 'Include all audio tracks',
  'AssetsPanel.loadingError': 'Failed to retreive assets from server.',
  'AssetsPanel.retry': 'Retry?',
  'CommandBar.prevFrameTitle': 'Back up one frame',
  'CommandBar.prevKeyFrameTitle': 'Back up one GOP',
  'CommandBar.cleanJob': 'Remove all assets',
  'CommandBar.cleanJobTitle': 'Remove all assets from timeline',
  'CommandBar.cleanJobMessage': 'This will empty all video clips from your timeline.',
  'CommandBar.update': 'Update filter',
  'CommandBar.createFilter': 'Create filter',
  'CommandBar.submit': 'Submit subclipper job',
  'CommandBar.jobErrorTitle': 'Operation failed',
  'CommandBar.jobErrorMessage': 'Your subclip failed to submit. Please try again.',
  'CommandBar.markInTitle': 'Set in at playhead',
  'CommandBar.markInPosition': 'Mark in timecode',
  'CommandBar.markOutTitle': 'Set out at playhead',
  'CommandBar.markOutPosition': 'Mark out timecode',
  'CommandBar.nextFrameTitle': 'Advance one frame',
  'CommandBar.nextKeyFrameTitle': 'Advance one GOP',
  'CommandBar.play': 'Play video',
  'CommandBar.pause': 'Pause video',
  'CommandBar.playPreviewTitle': 'Play subclip preview',
  'CommandBar.pausePreviewTitle': 'Pause subclip preview',
  'CommandBar.redoTitle': 'Redo last action',
  'CommandBar.removeAsset': 'Remove current asset',
  'CommandBar.undoTitle': 'Undo last action',
  'VideoPlayer.errorTitle': 'Error',
  'VideoPlayer.errorMessage': 'There was an error loading the selected asset.',
  'Timeline.markIn': 'Mark in bracket',
  'Timeline.markOut': 'Mark out bracket',
  'Timeline.playHead': 'Play head',
};
```