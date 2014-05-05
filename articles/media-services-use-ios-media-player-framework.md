<properties linkid="develop-media-services-how-to-guides-ios-media-player-framework" urlDisplayName="Платформа iOS Media Player" pageTitle="Использование платформы iOS Media Player со службами носителей Azure" metaKeywords="" description="Узнайте, как использовать библиотеку платформы Media Services iOS служб носителей для создания информативных, динамических приложений." metaCanonical="" services="media-services" documentationCenter="" title="Использование платформы iOS Media Player для служб носителей Azure" authors="" solutions="" manager="" editor="" />



#Использование платформы iOS Media Player для служб носителей Azure

Библиотека платформы iOS Media Player для служб носителей Azure упрощает для разработчиков ПО для iPod, iPhone и iPad создание информативных, динамических клиентских приложений, которые позволяют в режиме реального времени создавать и смешивать потоки видео и аудио. Например, в приложения, отображающие спортивный контент, можно вставлять рекламные объявления в любом месте по своему выбору, а также управлять частотой отображения этих объявлений даже при перемотке контента. В образовательных приложениях те же функциональные возможности, например, могут использоваться для создания контента, в котором основная лекция сопровождается дополнительными пояснениями или вспомогательными данными перед возвратом к основному контенту.

Как правило, разработать приложение, способное создавать потоки контента, основанные на взаимодействии между приложением и его пользователем, относительно сложная задача - обычно требуется создавать весь поток "с нуля" и заблаговременно сохранить его на сервере. Платформа IOS Media Player позволяет создавать клиентские приложения, которые могут делать все это без необходимости управлять основным потоком контента или изменять его. Вы можете:

- Заблаговременное планирование потоков контента на клиентском устройстве.
- Планирование рекламы вставок до показа.
- Планирование рекламы вставок после показа.
- Планирование рекламы или вставок во время показа и создание рекламных блоков.
- Управление воспроизведением рекламы или вставок во время показа — при каждой перемотке шкалы воспроизведения контента или однократное воспроизведение с удалением со шкалы времени.
- Динамическая вставка контента непосредственно в шкалу времени в результате любого события — нажатие пользователем кнопки или получение приложением уведомления от службы - например, программа показа новостного контента может отправлять уведомления с экстренными новостями, в ответ на что приложение будет приостанавливать воспроизведение основного контента для динамической загрузки потока экстренных новостей. 

Сочетание этих функций с возможностями воспроизведения мультимедиа устройств iOS делает возможным создание очень широких возможностей мультимедиа за очень короткое время с привлечением меньшего количества ресурсов.

Пакет SDK содержит приложение SamplePlayer, которое демонстрирует, как создать приложение iOS, использующее большинство из этих функций для создания потока контента в режиме реального времени и позволяющее пользователю динамически инициировать вставку нажатием кнопки. В этом учебном курсе рассматриваются основные компоненты приложения SamplePlayer и его использование в качестве отправной точки для вашего приложения.

## Приступая к работе с приложением с примером проигрывателя
В перечисленных ниже шагах демонстрируется, как получить приложение, а также описываются области приложения, которые используют данную платформу. 

1. Клонирование репозитория git. 

    `git clone https://github.com/WindowsAzure/azure-media-player-framework`

2. Откройте проект, расположенный в файле `azure-media-player-framework/src/iOS/HLSClient/`: **SamplePlayer.xcodeproj**

 
3. Ниже приведена структура образца проигрывателя:

![Структура кода примера HLS](http://mingfeiy.com/wp-content/uploads/2013/01/HLS-Structure.png)

4. В папке iPad есть два файла .xib: **SeekbarViewController** и **SamplePlayerViewController**. Они создают макет пользовательского интерфейса приложения iPad. Аналогичным образом, в папке iPhone есть два XLB-файла, которые определяют поле поиска и контроллер. 

6. Логика основного приложения находится в файле **SamplePlayerViewController.m** в папке `Общие`. В этом файле находится большинство фрагментов кода, описанных ниже. 

## Общее представление о макете пользовательского интерфейса
Существуют два XIB-файла, которые определяют интерфейс нашего проигрывателя. (Ниже в качестве примера рассматривается макет iPad, однако макет iPhone очень похож и действуют те же принципы).
### SamplePlayerViewController_iPad.xib
![Адресная строка примера проигрывателя](http://mingfeiy.com/wp-content/uploads/2013/01/addressbar.png)

* Для загрузки потока мультимедиа используется URL-адрес, указанный в поле **URL-адрес мультимедиа**. Приложение содержит готовый список URL-адресов мультимедиа, которые можно использовать с помощью кнопок выбора URL-адреса. Кроме того, можно ввести собственный URL-адрес контента HTTP Live (HLS). Этот мультимедийный контент будет использоваться в качестве первого основного контента. 
**Примечание. В поле URL-адреса следует указать значение.**

* Кнопки **Выбор URL-адреса** позволяют выбрать альтернативные URL-адреса из списка URL-адресов мультимедиа.

### SeekbarViewController_iPad.xib
![Контроллер строки поиска](http://mingfeiy.com/wp-content/uploads/2013/01/controller.png)
* Кнопка **Воспроизведение** используется для воспроизведения и приостановки воспроизведения мультимедиа.

* Поле **Строка поиска** охватывает всю шкалу воспроизведения. Для выполнения поиска нажмите и удерживайте элемент, перетащите его в нужное место, а затем отпустите кнопку поиска на панели поиска. 

**Примечание**. Если средство просмотра ведет поиск в рекламном объявлении, отображается новая строка поиска с длительностью этого объявления. Основная строка поиска отображает только длительность основного контента (другими словами, в основной панели поиска указывается, что реклама имеет длительность 0).

* Элемент управления **Время проигрывателя** отображает два времени (`Label:playerTime`), например 00:23/02:10. В этом случае 00:23 — это текущее время воспроизведения, а 02:10 — общая продолжительность мультимедиа. 

* Действие кнопок **SkipFroward и SkipBackward**в настоящее время отличается от ожидаемого; в ближайшее время будет выпущена обновленная версия.

* При нажатии кнопки **Запланировать сейчас** во время воспроизведения основного контента будет вставлено рекламное объявление (URL-адрес объявления определяется в базовом коде). Примечание. В текущей версии невозможно запланировать рекламное объявление во время воспроизведения другого объявления. 

### Планирование основного контента
Запланирован контент — видеоролик длительностью от 0 до 80 секунд:

    //Schedule the main content
    MediaTime *mediaTime = [[[MediaTime alloc] init] autorelease];
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
        
    int clipId = 0;
    if (![framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

В предыдущем примере кода:

* Объект * **MediaTime** управляет видеоклипом, который требуется запланировать в качестве основного контента. В предыдущем примере для видеоролика будет запланирована длительность 80 секунд (от 0 секунд до 80 секунд);
* Свойство * **clipBeginMediaTime** представляет время начала воспроизведения видео. Например, если свойство **clipBeginMediaTime** = 5, воспроизведение видеоролика начнется с 5 секунд.
* Свойство * **clipEndMediaTime** представляет время окончания воспроизведения видео. Если свойство **clipEndMediaTime**= 100, воспроизведение видео будет завершено на 100-й секунде видеоклипа.
*Затем планируется объект **MediaTime** путем запроса **appendContentClip** к платформе. В предыдущем примере URL-адрес задается в параметре `[NSURL URLWithString:url]`, а планирование для этого мультимедиа задается с помощью **withMedia**:
 `[framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])`.

**Примечание**. Основной контент всегда планируется до планирования рекламных объявлений (включая рекламу до показа). 

### Вариант. При воспроизведении двух основных роликов контента можно также запланировать воспроизведение второго ролика после первого с помощью следующего кода:

    //Schedule second content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 30;
    mediaTime.clipEndMediaTime = 80;
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

Если сделать это после предшествующего кода, будет запланировано два потока контента на основной временной шкале контента. Первый контент планируется на основе значения `URLWithString:url`, а второй — на основе `URLWithString:secondContent`. Второй контент начинается с 30 секунд потока мультимедиа и заканчивается на 80 секунде после начала. 

## Планирование рекламных объявлений 
В текущем выпуске поддерживаются только рекламные объявления **pauseTimeline = false**. Это означает, что после окончания рекламы проигрыватель продолжит воспроизведение с момента приостановки основного контента. 

Ниже приведены некоторые ключевые моменты:
* При планировании рекламного объявления все параметры **LinearTime.duration** должны иметь значение 0.
* Если значение **clipEndMediaTime** превышает длительность рекламного объявления, это объявление завершается по достижении конца и исключение не создается. Рекомендуется проверить, попадает ли исходная длительность рекламы в рамки времени воспроизведения (**clipEndMediaTime**), чтобы не потерять возможность рекламы. 
* Поддерживаются рекламные объявления до показа, во время показа или после показа. Объявления до показа можно планировать только на самое начало воспроизведения всего контента. Например, нельзя запланировать объявление до показа для второго контента в сценарии с предварительным монтажом (RCE). 
* Поддерживаются непрерывные и разовые рекламные объявления. Их можно сочетать с рекламой до показа, во время показа или после показа.
* Допускается формат рекламного объявления MP4 или HLS.

### Планирование рекламных объявлений до показа, во время показа и после показа, а также рекламных блоков

####Планирование рекламных объявлений до показа

    LinearTime *adLinearTime = [[[LinearTime alloc] init] autorelease];
    NSString *adURLString = @"http://smoothstreamingdemo.blob.core.windows.net/videoasset/WA-BumpShort_120530-1.mp4";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.clipBeginMediaTime = 0;
    adInfo.mediaTime.clipEndMediaTime = 5;
    adInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

Объект **AdInfo** представляет все сведения о вашем рекламном ролике:
* Поле **ClipURL** указывает URL-адрес исходного клипа.
* Свойство **mediaTime** указывает, как долго будет воспроизводиться реклама. (**clipBeginMediaTime** указывает момент начала рекламного объявления, а значение **clipEndMediaTime** определяет конец рекламного объявления). В предыдущем примере кода запланирована реклама в течение 5 секунд, от 0 до 5-й секунды длительности объявления.
* Объект **Политика** в настоящее время не используется в платформе.
* Необходимо установить для параметра **appendTo** значение -1, если это не рекламный блок. 
* Параметр **Тип** может указывать объявление до показа, во время показа, после показа или рекламный блок. Тип требуется указать для объявлений до или после показа, так как у них отсутствуют свойства времени. 

####Планирование рекламных объявлений во время показа

Если добавить в предыдущий пример кода строку `adLinearTime.startTime = 23;`, объявление начнет воспроизводиться с 23 секунды по временной шкале основного контента.

####Планирование рекламных объявлений после показа

    //Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    postAdInfo.mediaTime.clipEndMediaTime = 5;
    postAdInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

Единственное отличие от планирования объявлений до показа состоит в строке `postAdInfo.type = AdType_Postroll;`. В приведенном выше коде воспроизведение 5-секундного рекламного объявления запланировано после показа контента. 

#### Планирование рекламных блоков
Рекламный блок представляет собой рекламную паузу с несколькими рекламными объявлениями, которые воспроизводятся последовательно. Ниже приведен код для планирования двух рекламных объявлений в одном рекламном блоке. 

    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    adpodInfo1.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.mediaTime = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    int32_t adIndex = 0;
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
        
    NSString *adpodSt2=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-532531b8-fca4-4c15-86f6-45f9f45ec980/Windows%208_%20Sign%20in%20with%20a%20Smile.mp4?st=2012-11-28T16%3A35%3A26Z&se=2014-11-28T16%3A35%3A26Z&sr=c&si=c6ede35c-f212-4ccd-84da-805c4ebf64be&sig=zcWsj1JOHJB6TsiQL5ZbRmCSsEIsOJOcPDRvFVI0zwA%3D";
    AdInfo *adpodInfo2 = [[[AdInfo alloc] init] autorelease];
    adpodInfo2.clipURL = [NSURL URLWithString:adpodSt2];
    adpodInfo2.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo2.mediaTime.clipBeginMediaTime = 0;
    adpodInfo2.mediaTime.clipEndMediaTime = 17;
    adpodInfo2.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo2.type = AdType_Pod;
    adpodInfo2.appendTo = adIndex;
    if (![framework scheduleClip:adpodInfo2 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

Следует отметить несколько моментов:
* Для первого ролика свойство **appendTo** имеет значение -1. При вызове метода `[framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex]` объект `adIndex` получает уникальное значение, указывающее конец первого ролика в рекламном блоке. Затем для второго ролика в рекламном блоке следует сопоставить начало второго рекламного объявления с концом первого, установив для свойства **appendTo** значение `adpodInfo2.appendTo = adIndex;`, что указывает конечную позицию первого ролика как момент начала воспроизведения второго ролика. 
* Затем необходимо задать тип `AdType_Pod`, чтобы указать, что это рекламный блок. 

### Планирование разовых и непрерывных рекламных объявлений
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    oneTimeInfo.deleteAfterPlay = YES;

Как показано в предыдущем примере, если установить для свойства **deleteAfterPlay** значение **YES**, объявление воспроизводится только один раз. Если же установить для свойства **deleteAfterPlay** значение **NO**, объявление будет воспроизводиться непрерывно (из чего и следует название таких объявлений).
### Дополнительные сведения см. в [вики по платформе Azure Media Player](https://github.com/WindowsAzure/azure-media-player-framework/wiki).

