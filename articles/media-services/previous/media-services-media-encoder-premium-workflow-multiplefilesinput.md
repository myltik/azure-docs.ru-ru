---
title: Несколько входных файлов и свойства компонентов в кодировщике ценовой категории "Премиум" | Документация Майкрософт
description: В этой статье описано, как с помощью свойства setRuntimeProperties можно использовать несколько входных файлов в обработчике мультимедиа "Расширенный рабочий процесс обработчика мультимедиа" и передавать в него пользовательские данные.
services: media-services
documentationcenter: ''
author: xpouyat
manager: cfowler
editor: ''
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: xpouyat;anilmur;juliako
ms.openlocfilehash: 66aec76e5af399e1909446b8ddf7a79aa1384d52
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783763"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Использование нескольких входных файлов и свойств компонентов в кодировщике Premium
## <a name="overview"></a>Обзор
Есть сценарии, в которых при отправке задачи с помощью обработчика мультимедиа **Расширенный рабочий процесс кодировщика мультимедиа** может потребоваться настроить свойства компонентов, указать содержимое XML-файла со списком клипов или отправить несколько входных файлов. Ниже приведены некоторые примеры.

* Наложение текста на видео и задание текстового значения (например, текущей даты) во время выполнения для каждого входного видео.
* Настройка XML-файла со списком клипов (для указания одного или нескольких исходных файлов с обрезкой или без нее и т. д.).
* Наложение изображения логотипа на входное видео во время кодирования.
* Кодирование нескольких аудиодорожек на разных языках.

Чтобы **расширенный рабочий процесс кодировщика мультимедиа** знал, что в рабочем процессе при создании задания или отправке нескольких входных файлов некоторые свойства будут изменены, необходимо использовать строку конфигурации, содержащую элементы **setRuntimeProperties** и (или) **transcodeSource**. В этом разделе объясняется, как их использовать.

## <a name="configuration-string-syntax"></a>Синтаксис строки конфигурации
В строке конфигурации, которую необходимо задать в задаче кодирования, используется XML-документ, выглядящий следующим образом.

```xml
<?xml version="1.0" encoding="utf-8"?>
<transcodeRequest>
  <transcodeSource>
  </transcodeSource>
  <setRuntimeProperties>
    <property propertyPath="Media File Input/filename" value="VideoFileName.mp4" />
  </setRuntimeProperties>
</transcodeRequest>
```

Ниже приведен код C#, который считывает XML-файл конфигурации, обновляет его, добавляя правильное имя видеофайла, и передает его в задачу в задании.

```csharp
string premiumConfiguration = ReadAllText(@"D:\home\site\wwwroot\Presets\SetRuntime.xml").Replace("VideoFileName", myVideoFileName);

// Declare a new job.
IJob job = _context.Jobs.Create("Premium Workflow encoding job");

// Get a media processor reference, and pass to it the name of the 
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

// Create a task with the encoding details, using a string preset.
ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                              processor,
                              premiumConfiguration,
                              TaskOptions.None);

// Specify the input assets
task.InputAssets.Add(workflow); // workflow asset
task.InputAssets.Add(video); // video asset with multiple files

// Add an output asset to contain the results of the job. 
// This output is specified as AssetCreationOptions.None, which 
// means the output asset is not encrypted. 
task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
```

## <a name="customizing-component-properties"></a>Настройка свойств компонентов
### <a name="property-with-a-simple-value"></a>Свойство с простым значением
В некоторых случаях рекомендуется настраивать свойство компонента вместе с файлом рабочего процесса, который будет выполняться обработчиком "Расширенный рабочий процесс кодировщика мультимедиа".

Предположим, что разработан рабочий процесс, который накладывает текст на видео, и этот текст (например, текущая дата) необходимо задать во время выполнения. Это можно сделать, отправив текст в качестве нового значения для свойства текста компонента наложения из задачи кодирования. Этот механизм можно использовать для изменения других свойств компонента в рабочем процессе (например, положения или цвета наложения, скорости кодировщика AVC и т. д.).

**setRuntimeProperties** используется для переопределения свойства в компонентах рабочего процесса.

Пример:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
      <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
  </setRuntimeProperties>
</transcodeRequest>
```

### <a name="property-with-an-xml-value"></a>Свойство со значением XML
Для задания свойства, которое ожидает значение XML, необходима инкапсуляция с использованием синтаксиса `<![CDATA[ and ]]>`.

Пример:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

> [!NOTE]
> Не ставьте знак возврата каретки сразу после `<![CDATA[`.

### <a name="propertypath-value"></a>Значение propertyPath
В предыдущих примерах значение propertyPath было /Media File Input/filename, /inactiveTimeout или clipListXml.
Обычно это имя компонента, а не свойства. Путь может содержать больше или меньше уровней, например /primarySourceFile (так как свойство находится в корне рабочего процесса) или /Video Processing/Graphic Overlay/Opacity (так как наложение находится в группе).    

Чтобы проверить путь и имя свойства, нажмите управляющую кнопку, которая находится непосредственно возле каждого свойства. Вы можете нажать эту управляющую кнопку и выбрать команду **Изменить**. Отобразится фактическое имя свойства, а непосредственно над ним — пространство имен.

![Действие/Изменить](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Свойство](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Несколько входных файлов
Для каждой задачи, которую нужно передать в обработчик **Расширенный рабочий процесс кодировщика мультимедиа** , требуются два ресурса.

* Первый — *ресурс рабочего процесса* , в котором содержится файл рабочего процесса. Файлы рабочих процессов можно спроектировать с помощью [конструктора рабочих процессов](media-services-workflow-designer.md).
* Второй — *ресурс мультимедиа* , содержащий файлы мультимедиа, которые требуется закодировать.

При отправке нескольких файлов мультимедиа в кодировщик **Расширенный рабочий процесс кодировщика мультимедиа** применяются следующие ограничения.

* Все файлы мультимедиа должны находиться в одном *ресурсе мультимедиа*. Использование нескольких ресурсов мультимедиа не поддерживается.
* В этом ресурсе мультимедиа необходимо настроить основной файл (в идеале это основной видеофайл, который должен обработать кодировщик).
* В обработчик необходимо передать данные конфигурации, в том числе элемент **setRuntimeProperties** и (или) **transcodeSource**.
  * **setRuntimeProperties** используется для переопределения свойства имени файла или другого свойства в компонентах рабочего процесса.
  * **transcodeSource** используется для указания содержимого XML-файла со списком клипов.

Соединения в рабочем процессе

* Если используется один или несколько компонентов ввода файлов мультимедиа и планируется использовать элемент **setRuntimeProperties** для указания имени файла, то не присоединяйте к ним компонент основного файла. Убедитесь в отсутствии соединения между объектом основного файла и компонентами ввода файлов мультимедиа.
* Если вы предпочитаете использовать компонент "XML-файл со списком клипов" и один компонент "Источник мультимедиа", их можно присоединить друг к другу.

![Компонент "Основной исходный файл" не присоединен к компоненту "Входные файлы мультимедиа"](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*При использовании элемента setRuntimeProperties для задания имени файла не должно быть соединения между основным файлом и компонентами ввода файлов мультимедиа*

![Соединение компонентов "XML-файл со списком клипов" и "Источник списка клипов"](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Вы можете соединить XML-файл со списком клипов с источником мультимедиа и использовать элемент transcodeSource*

### <a name="clip-list-xml-customization"></a>Настройка XML-файла со списком клипов
XML-файл со списком клипов можно указать в рабочем процессе во время его выполнения с помощью элемента **transcodeSource** в XML-файле строки конфигурации. Для этого требуется, чтобы XML-файл со списком клипов был присоединен к компоненту "Источник мультимедиа" в рабочем процессе.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <transcodeSource>
      <clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
      </clipList>
    </transcodeSource>
    <setRuntimeProperties>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Если вы хотите указать /primarySourceFile, чтобы использовать это свойство для именования выходных файлов с помощью параметра Expressions, то рекомендуется передать XML-файл со списком клипов в качестве свойства *после* свойства /primarySourceFile, чтобы избежать переопределения списка клипов параметром этого свойства.

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Ниже приведен код с дополнительной точной обрезкой кадров.

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Пример 1. Наложение изображения поверх видео

### <a name="presentation"></a>Уровень представления
Рассмотрим пример, в котором необходимо наложить изображение логотипа на входное видео во время кодирования. В этом примере используются входной видеофайл Microsoft_HoloLens_Possibilities_816p24.mp4 и логотип logo.png. Нужно выполнить следующие действия.

* Создайте ресурс рабочего процесса с файлом рабочего процесса (пример приведен ниже).
* Создайте ресурс мультимедиа, который содержит два файла: MyInputVideo.mp4 в качестве основного файла и MyLogo.png.
* Отправьте задачу в обработчик мультимедиа "Расширенный рабочий процесс кодировщика мультимедиа" с помощью указанных выше входных ресурсов и укажите следующую строку конфигурации.

Конфигурация:

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

В приведенном выше примере имя видеофайла передается в компонент ввода файлов мультимедиа и свойство primarySourceFile. Имя файла логотипа передается в другой компонент ввода файлов мультимедиа, подключенный к компоненту наложения изображений.

> [!NOTE]
> Имя видеофайла передается в свойство primarySourceFile, так как это свойство используется в рабочем процессе для создания правильного имени выходного файла, например с помощью параметра Expressions.

### <a name="step-by-step-workflow-creation"></a>Пошаговое создание рабочего процесса
Ниже приведена процедура создания рабочего процесса, который в качестве входных данных принимает два файла: видео и изображение. В этом процессе изображение будет наложено поверх видео.

Откройте **конструктор рабочих процессов** и последовательно щелкните **Файл** > **Создать рабочую область** > **Схема перекодирования**.

В новом рабочем процессе отобразятся три элемента:

* основной исходный файл;
* XML-файл списка клипов;
* выходной файл и ресурс-контейнер.  

![Новый рабочий процесс кодирования](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Новый рабочий процесс кодирования*

Чтобы принять входной файл мультимедиа, в область входного файла мультимедиа нужно добавить компонент "Входные файлы мультимедиа". Чтобы добавить компонент в рабочий процесс, найдите его в репозитории и перетащите нужную запись в соответствующую область конструктора.

Теперь добавьте видеофайл, используемый для создания рабочего процесса. Для этого щелкните фоновую область в конструкторе рабочих процессов и найдите свойство основного исходного файла на панели свойств справа. Щелкните значок папки и выберите соответствующий видеофайл.

![Основной источник файла](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Основной источник файла*

Затем укажите видеофайл в компоненте "Входные файлы мультимедиа".   

![Источник входного файла мультимедиа](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Источник входного файла мультимедиа*

После этого компонент ввода файлов мультимедиа проверит файл и создаст для него выходные точки.

Следующий шаг — добавление компонента "Обновление типа видеоданных" для указания значения Rec.709 для цветового пространства. Добавьте компонент "Конвертер форматов видео", в параметрах "Макет данных" и "Тип макета" которого указано "Configurable Planar" (Настраиваемый плоский). После этого видеопоток будет преобразован в формат, который можно использовать в качестве источника компонента наложения.

![Компоненты "Обновление типа видеоданных" и "Конвертер форматов видео"](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Компоненты "Обновление типа видеоданных" и "Конвертер форматов видео"*

![Тип макета — Configurable Planar (Настраиваемый переключатель)](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Тип макета — "Configurable Planar"*

Затем добавьте компонент наложения видео и присоедините видео (без сжатия) к видео (без сжатия) входного файла мультимедиа.

Добавьте еще один компонент ввода файлов мультимедиа (чтобы загрузить файл логотипа), щелкните его и переименуйте в "Логотип для входных файлов мультимедиа", а затем выберите изображение (например, PNG-файл) в свойстве файла. Соедините несжатое изображение и несжатое изображение наложения.

![Компонент наложения и источник файла изображения](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Компонент наложения и источник файла изображения*

Если вы хотите изменить положение логотипа на видео (например, сместить его на 10 % от верхнего левого угла видео), снимите флажок "Ручной ввод". Это можно сделать, так как для передачи файла логотипа в компонент наложения используется компонент ввода файлов мультимедиа.

![Положение наложения](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Положение наложения*

Для кодирования видеопотока в стандарте H.264 добавьте в рабочую область конструктора компонент видеокодировщика AVC и кодировщика AAC. Присоедините их друг к другу.
Настройте кодировщик AAC и выберите "Audio Format Conversion/Preset : 2.0 (L, R)" (Преобразование или предустановка формата аудио: 2.0 (левый, правый)).

![Кодировщики аудио и видео](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Кодировщики аудио и видео*

Теперь добавьте компонент **мультиплексора ISO MPEG-4** и компонент **вывода файла** и соедините их, как показано на рисунке.

![Мультиплексор MP4 и вывод файла](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*Мультиплексор MP4 и вывод файла*

Необходимо задать имя для выходного файла. Щелкните компонент **вывода файла** и измените выражение для файла.

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Имя вывода файла](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Имя вывода файла*

Рабочий процесс можно запустить локально, чтобы проверить, правильно ли он выполняется.

После завершения его можно запустить в службах мультимедиа Azure.

Сначала в службах мультимедиа Azure подготовьте ресурс-контейнер, содержащий два файла: видеофайл и файл логотипа. Это можно сделать с помощью .NET или REST API. Это можно также сделать на портале Azure или с помощью [обозревателя служб мультимедиа Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

В данном учебнике показано, как управлять ресурсами-контейнерами с помощью AMSE. Добавлять файлы в ресурс-контейнер можно двумя способами.

* Создайте локальную папку, скопируйте в нее два файла и перетащите папку на вкладку **Ресурс-контейнер** .
* Передайте видеофайл в качестве ресурса-контейнера, отобразите сведения о нем, перейдите на вкладку файлов и передайте дополнительный файл (логотип).

> [!NOTE]
> Настройте основной файл в ресурсе (основной видеофайл).

![Файлы ресурсов в AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Файлы ресурсов в AMSE*

Выберите ресурс и способ кодирования с помощью кодировщика Premium. Загрузите рабочий процесс и выберите его.

Нажмите кнопку, чтобы передать данные в обработчик, и добавьте следующий XML-файл, чтобы задать свойства среды выполнения.

![Кодировщик Premium в AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Кодировщик Premium в AMSE*

Вставьте следующие XML-данные. Необходимо указать имя видеофайла для компонента "Входные файлы мультимедиа" и свойства primarySourceFile. Укажите также имя файла для эмблемы.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*

При использовании пакета SDK для .NET для создания и запуска задачи эти XML-данные необходимо передать в качестве строки конфигурации.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

После выполнения задания в MP4-файле выходного ресурса-контейнера отобразится наложение.

![Наложение на видео](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Наложение на видео*

Вы можете скачать пример рабочего процесса с сайта [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Пример 2. Кодирование нескольких аудиодорожек на разных языках

Пример рабочего процесса кодирования нескольких аудиодорожек на разных языках доступен в [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Эта папка содержит пример рабочего процесса, который можно использовать для кодирования MXF-файла в ресурс с несколькими MP4-файлами, содержащий несколько звуковых дорожек.

Этот рабочий процесс предполагает, что MXF-файл содержит одну звуковую дорожку. Дополнительные звуковые дорожки должны передаваться как отдельные аудиофайлы (в формате WAV или MP4).

Ниже приведена процедура кодирования.

* Создайте ресурс служб мультимедиа с MXF-файлом и аудиофайлами (их должно быть от 0 до 18).
* Убедитесь, что MXF-файл задан в качестве первичного файла.
* Создайте задание и задачу с помощью обработчика Premium Workflow Encoder. Используйте предоставленный рабочий процесс (MultiMP4-1080p-19audio-v1.workflow).
* Передайте данные setruntime.xml в задачу (если используется обозреватель служб мультимедиа Azure, воспользуйтесь кнопкой "pass xml data to the workflow" (Передать данные XML в рабочий процесс)).
  * Обновите данные XML, чтобы указать правильные имена файлов и теги языков.
  * Рабочий процесс содержит компоненты аудио от Audio 1 до Audio 18.
  * RFC5646 поддерживается для тега языка.

```xml
<?xml version="1.0" encoding="utf-16"?>
<transcodeRequest>
  <setRuntimeProperties>
    <property propertyPath="Media File Input Video/filename" value="MainVideo.mxf" />
    <property propertyPath="Language/language_code" value="en" />
    <property propertyPath="/primarySourceFile" value="MainVideo.mxf" />
    <property propertyPath="Audio 1/Media File Input/filename" value="french-audio.wav" />
    <property propertyPath="Audio 1/Language/language_code" value="fr" />
    <property propertyPath="Audio 2/Media File Input/filename" value="german-audio.wav" />
    <property propertyPath="Audio 2/Language/language_code" value="de" />
    <property propertyPath="Audio 3/Media File Input/filename" value="japanese-audio.wav" />
    <property propertyPath="Audio 3/Language/language_code" value="ja" />
  </setRuntimeProperties>
</transcodeRequest>
```

* Закодированный ресурс будет содержать звуковые дорожки на нескольких языках, которые будут доступны для выбора в Проигрывателе мультимедиа Azure.

## <a name="see-also"></a>См. также
* [Знакомство со Службой кодирования категории "Премиум" в службах мультимедиа Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Использование Службы кодирования категории "Премиум" в службах мультимедиа Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Обзор и сравнение кодировщиков мультимедиа Azure по запросу](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Форматы и кодеки рабочего процесса Premium Media Encoder](media-services-premium-workflow-encoder-formats.md)
* [Примеры файлов рабочего процесса](https://github.com/Azure/azure-media-services-samples)
* [Средство Explorer для служб мультимедиа Azure](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
