<properties 
	pageTitle="Использование нескольких входных файлов и свойств компонентов в кодировщике Premium | Microsoft Azure" 
	description="В этой статье описано, как с помощью свойства setRuntimeProperties можно использовать несколько входных файлов в обработчике мультимедиа ";Расширенный рабочий процесс обработчика мультимедиа"; и передавать в него пользовательские данные." 
	services="media-services" 
	documentationCenter="" 
	authors="xpouyat" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2016"  
	ms.author="xpouyat;anilmur;juliako"/>

#Использование нескольких входных файлов и свойств компонентов в кодировщике Premium

##Обзор 

Есть сценарии, в которых при отправке задачи с помощью обработчика мультимедиа **Расширенный рабочий процесс кодировщика мультимедиа** может потребоваться настроить свойства компонентов, указать содержимое XML-файла со списком клипов или отправить несколько входных файлов. Ниже приведены некоторые примеры.

- наложение текста на видео и присвоение текстового значения (например, текущая дата) во время выполнения для каждого входного видео;
- настройка XML-файла со списком клипов (для указания одного или нескольких исходных файлов с обрезкой или без нее и т. д.);
- наложение изображения эмблемы на входное видео во время кодирования.

Чтобы обработчик мультимедиа **Расширенный рабочий процесс кодировщика мультимедиа** знал, что в рабочем процессе при создании задания или отправке нескольких входных файлов некоторые свойства будут изменены, необходимо использовать строку конфигурации, содержащую элементы **setRuntimeProperties** и/или **transcodeSource**. В этом разделе объясняется, как их использовать.


##Синтаксис строки конфигурации

В строке конфигурации, которую необходимо задать в задаче кодирования, используется XML-документ, выглядящий следующим образом.
  
    <?xml version="1.0" encoding="utf-8"?>
    <transcodeRequest>
      <transcodeSource>
      </transcodeSource>
      <setRuntimeProperties>
        <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      </setRuntimeProperties>
    </transcodeRequest>


Ниже приведен код на языке C# для считывания конфигурации XML из файла и ее передачи в задачу задания.

    XDocument configurationXml = XDocument.Load(xmlFileName);
    IJob job = _context.Jobs.CreateWithSingleTask(
                                                  "Media Encoder Premium Workflow",
                                                  configurationXml.ToString(),
                                                  myAsset,
                                                  "Output asset",
                                                  AssetCreationOptions.None);


##Настройка свойств компонентов  

###Свойство с простым значением
В некоторых случаях рекомендуется настраивать свойство компонента вместе с файлом рабочего процесса, который будет выполняться обработчиком "Расширенный рабочий процесс кодировщика мультимедиа". Предположим, что разработан рабочий процесс, который накладывает текст на видео, и этот текст (например, текущая дата) необходимо задать во время выполнения. Это можно сделать, отправив текст в качестве нового значения для свойства текста компонента наложения из задачи кодирования. Этот механизм можно использовать для изменения других свойств компонента в рабочем процессе (например, положения или цвета наложения, скорости кодировщика AVC и т. д.). Свойство **setRuntimeProperties** используется для переопределения свойства в компонентах рабочего процесса.

Пример:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Optional Overlay/Overlay/filename" value="MyLogo.png"/>
          <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
      </setRuntimeProperties>
    </transcodeRequest>


###Свойство со значением XML

Чтобы задать свойство, которое ожидает значение XML, необходимо инкапсулировать с использованием синтаксиса <![CDATA[ и ]]>.

Пример:

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

>[AZURE.NOTE]Не ставьте знак возврата каретки сразу после параметра <![CDATA[.


###Значение propertyPath

В предыдущих примерах значение propertyPath было /Media File Input/filename, /inactiveTimeout или clipListXml. Обычно это имя компонента, а не имя свойства. Путь может содержать больше или меньше уровней, например /primarySourceFile (так как свойство находится в корне рабочего процесса) или /Video Processing/Graphic Overlay/Opacity (так как наложение находится в группе).

Чтобы проверить путь и имя свойства, нажмите управляющую кнопку, которая находится непосредственно возле каждого свойства. Вы можете нажать эту управляющую кнопку и выбрать команду "Изменить". Отобразится фактическое имя свойства, а непосредственно над ним — пространство имен.

![Действие/Изменить](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Свойство](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

##Несколько входных файлов

Для каждой задачи, которую нужно передать в обработчик **Расширенный рабочий процесс кодировщика мультимедиа**, требуется два ресурса.

- Первый — "Ресурс рабочего процесса", в котором содержится файл рабочего процесса. Файлы рабочих процессов можно спроектировать с помощью [конструктора рабочих процессов](media-services-workflow-designer.md).
- Второй — "Ресурс мультимедиа", содержащий файлы мультимедиа, которые требуется закодировать.

При отправке нескольких файлов мультимедиа в кодировщик **Расширенный рабочий процесс кодировщика мультимедиа** применяются следующие ограничения:

- Все файлы мультимедиа должны находиться в одном ресурсе мультимедиа. Использование нескольких ресурсов мультимедиа не поддерживается.
- В этом ресурсе мультимедиа необходимо настроить основной файл (в идеале это основной видеофайл, который должен обработать кодировщик).
- Необходимо передать данные конфигурации, в том числе элемент **setRuntimeProperties** и/или **transcodeSource**, в процессор.
  - Элемент **setRuntimeProperties** используется для переопределения имени файла или другого свойства в компонентах рабочего процесса.
  - Элемент **transcodeSource** используется для указания содержимого XML-файла со списком клипов.

Соединения в рабочем процессе
  - Если используется один или несколько компонентов "Входные файлы мультимедиа" и планируется использовать элемент **setRuntimeProperties** для указания имени файла, не присоединяйте к ним компонент основного файла. Убедитесь в отсутствии соединения между объектом основного файла и входными файлами мультимедиа.
  - Если вы предпочитаете использовать компонент "XML-файл со списком клипов" и один компонент "Источник мультимедиа", их можно присоединить друг к другу.

![Компонент "Основной исходный файл" не присоединен к компоненту "Входные файлы мультимедиа"](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*При использовании элемента setRuntimeProperties для задания имени файла нельзя присоединить основной файл к входным файлам мультимедиа*


![Соединение компонентов "XML-файл со списком клипов" и "Источник списка клипов"](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Вы можете соединить XML-файл со списком клипов с источником мультимедиа и использовать элемент transcodeSource*


###Настройка XML-файла со списком клипов
XML-файл со списком клипов можно указать в рабочем процессе во время его выполнения с помощью элемента **sourceTranscode** в XML-файле строки конфигурации. Для этого требуется, чтобы XML-файл со списком клипов был присоединен к компоненту "Источник мультимедиа" в рабочем процессе.

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

Если вы хотите указать /primarySourceFile, чтобы использовать это свойство для именования выходных файлов с помощью параметра Expressions, то рекомендуется передать XML-файл со списком клипов в качестве свойства *после* свойства /primarySourceFile, чтобы избежать переопределения списка клипов параметром этого свойства.

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

С дополнительной точной обрезкой кадров:

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


##Пример

Рассмотрим пример, в котором необходимо наложить изображение эмблемы на входное видео во время кодирования. В этом примере имя файла с входным видео — MyInputVideo.mp4, а имя файла с изображением эмблемы — MyLogo.png. Необходимо сделать следующее:

- Создайте ресурс рабочего процесса с файлом рабочего процесса (пример приведен ниже).
- Создайте ресурс мультимедиа, который содержит два файла: MyInputVideo.mp4 в качестве основного файла и MyLogo.png.
- Отправьте задачу в обработчик мультимедиа "Расширенный рабочий процесс кодировщика мультимедиа" с помощью указанных выше входных ресурсов и укажите следующую строку конфигурации.

Конфигурация:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="MyLogo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


В приведенном выше примере имя видеофайла передается в компонент "Входные файлы мультимедиа" и свойство primarySourceFile, имя файла с эмблемой отправляется в другой компонент "Входные файлы мультимедиа", присоединенный к компоненту графического наложения.

>[AZURE.NOTE]Имя видеофайла отправляется в свойство primarySourceFile, так как это свойство используется в рабочем процессе для создания правильного имени выходного файла, например с помощью параметра Expressions.


###Пошаговая инструкция по созданию рабочего процесса, в котором эмблема накладывается поверх видео     

Ниже приведена процедура создания рабочего процесса, который в качестве входных данных принимает два файла: видео и изображение. В этом процессе изображение будет наложено поверх видео.

Откройте **конструктор рабочих процессов** и последовательно щелкните **Файл**-> **Создать рабочую область**-> **Transcode Blueprint** (Схема перекодирования).

В новом рабочем процессе отобразятся три элемента:

- основной исходный файл;
- XML-файл списка клипов;
- выходной файл и ресурс-контейнер.

![Новый рабочий процесс кодирования](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Новый рабочий процесс кодирования*


Чтобы принять входной файл мультимедиа, в область входного файла мультимедиа нужно добавить компонент "Входные файлы мультимедиа". Чтобы добавить компонент в рабочий процесс, найдите его в репозитории и перетащите нужную запись в соответствующую область конструктора.

Теперь добавьте видеофайл, используемый для создания рабочего процесса. Для этого щелкните фоновую область в конструкторе рабочих процессов и найдите свойство основного исходного файла на панели свойств справа. Щелкните значок папки и выберите соответствующий видеофайл.

![Основной источник файла](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Основной источник файла*


Затем укажите видеофайл в компоненте "Входные файлы мультимедиа".

![Источник входного файла мультимедиа](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Источник входного файла мультимедиа*


После этого компонент входного файла мультимедиа проверит файл и создаст для него выходные точки.

Следующий шаг — добавление компонента "Обновление типа видеоданных" для указания значения Rec.709 для цветового пространства. Добавьте компонент "Конвертер форматов видео", в параметрах "Макет данных" и "Тип макета" которого указано Configurable Planar (Настраиваемый переключатель). После этого видеопоток будет преобразован в формат, который можно использовать в качестве источника компонента наложения.

![Компоненты "Обновление типа видеоданных" и "Конвертер форматов видео"](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Компоненты "Обновление типа видеоданных" и "Конвертер форматов видео"*

![Тип макета — Configurable Planar (Настраиваемый переключатель)](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Тип макета — Configurable Planar (Настраиваемый переключатель)*

Затем добавьте компонент наложения видео и присоедините видео (без сжатия) к видео (без сжатия) входного файла мультимедиа.

Добавьте еще один компонент "Входные файлы мультимедиа" (чтобы загрузить файл эмблемы), щелкните этот компонент и переименуйте его на "Эмблема входных файлов мультимедиа", а затем выберите изображение (например, PNG-файл) в свойстве файла. Соедините несжатое изображение и несжатое изображение наложения.

![Компонент наложения и источник файла изображения](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Компонент наложения и источник файла изображения*


Если вы хотите изменить положение эмблемы на видео (например, можно сместить его на 10 % от верхнего левого угла видео), снимите флажок "Ручной ввод". Так как компонент "Входные файлы мультимедиа" используется для предоставления файла эмблемы в компонент наложения, этот флажок можно снять.

![Положение наложения](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Положение наложения*


Для кодирования видеопотока в стандарте H.264 добавьте в рабочую область конструктора компонент видеокодировщика AVC и кодировщика AAC. Присоедините их друг к другу. Настройте кодировщик AAC и задайте для параметра Audio Format Conversion/Preset (Сжатие аудиоформата/предустановка) значение 2.0 (L, R).

![Кодировщики аудио и видео](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Кодировщики аудио и видео*


Теперь добавьте компоненты **Мультиплексор ISO MPEG-4** и **Вывод файла** и соедините их, как показано.

![Мультиплексор MP4 и вывод файла](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*Мультиплексор MP4 и вывод файла*


Необходимо задать имя для выходного файла. Щелкните компонент "Вывод файла" и измените выражение для файла.

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Имя вывода файла](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Имя вывода файла*


Рабочий процесс можно запустить локально, чтобы проверить, правильно ли он выполняется.

Если проверка прошла успешно, его можно запустить в службах мультимедиа Azure.

Сначала в службах мультимедиа Azure подготовьте ресурс, содержащий два файла: видеофайл и файл с эмблемой. Это можно сделать с помощью .NET или REST API. Это также можно сделать на портале Azure или с помощью [обозревателя служб мультимедиа Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

В этом руководстве показано, как управлять ресурсами с помощью AMSE. Добавлять файлы в ресурс можно двумя способами.

1. Создайте локальную папку, скопируйте в нее два файла и перетащите папку на вкладку "Ресурс".
1. Загрузите видеофайл в качестве ресурса, отобразите сведения о ресурсе, перейдите на вкладку файлов и загрузите дополнительный файл (эмблему).

>[AZURE.NOTE]Настройте основной файл в ресурсе (основной видеофайл).

![Файлы ресурсов в AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Файлы ресурсов в AMSE*


Выберите ресурс и способ кодирования с помощью кодировщика Premium. Загрузите рабочий процесс и выберите его.

Нажмите кнопку, чтобы передать данные в обработчик, и добавьте следующий XML-файл, чтобы задать свойства среды выполнения.

![Кодировщик Premium в AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Кодировщик Premium в AMSE*


Вставьте следующие XML-данные. Необходимо указать имя видеофайла для компонента "Входные файлы мультимедиа" и свойства primarySourceFile. Укажите также имя файла для эмблемы.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*


При использовании пакета SDK для .NET для создания и запуска задачи эти XML-данные необходимо передать в качестве строки конфигурации.

    public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
  
После выполнения задания в MP4-файле выходного ресурса отобразится наложение.

![Наложение на видео](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Наложение на видео*


Вы можете скачать образец рабочего процесса [здесь](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).


##См. также: 

[Знакомство с кодированием Premium в службах мультимедиа Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Использование кодирования Premium в службах мультимедиа Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Обзор и сравнение кодировщиков мультимедиа Azure по запросу](media-services-encode-asset.md#media_encoder_premium_workflow)

[Форматы и кодеки рабочего процесса Premium Media Encoder](media-services-premium-workflow-encoder-formats.md)

[Примеры файлов рабочего процесса](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)

[Средство Explorer для служб мультимедиа Azure](http://aka.ms/amse)

##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0713_2016-->