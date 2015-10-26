<properties 
	pageTitle="Кодирование мультимедиа с помощью Dolby Digital Plus" 
	description="В этом разделе описывается кодирование мультимедиа с помощью Dolby Digital Plus." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015"   
	ms.author="juliako"/>

#Кодирование мультимедиа с помощью Dolby Digital Plus

Кодировщик служб мультимедиа Azure поддерживает кодирование **Dolby® Digital Plus**. Dolby® Digital Plus, или Enhanced AC-3 (E-AC-3), является передовым аудиокодеком объемного звука, разработанным специально для развивающейся мультимедийной среды. От домашних кинотеатров и ПК до мобильных телефонов и интерактивной потоковой передачи, Dolby Digital Plus задает стандарт высококачественного звука. Вы получите звук кинематографического качества известной компании Dolby для всех своих развлечений. Dolby Digital Plus построен на базовых технологиях Dolby, являющихся установленным стандартом для пространственного звучания кинофильмов, вещания и домашних кинотеатров. По мере распространения мобильных устройств Dolby Digital Plus также развивается как стандарт для мобильных развлечений. Его новые передовые технологии для улучшения воспроизведения звука обеспечивают еще лучшее качество звука и дополнительное сокращение полосы пропускания. Вы получаете отличный контент с минимальными перебоями, даже если пропускная способность ограничена.


##Настройка кодировщика служб мультимедиа Azure для кодирования мультимедиа с помощью Dolby Digital Plus

###Получение кодировщика служб мультимедиа Azure 

Dolby Digital Plus поддерживается кодировщиком служб мультимедиа Azure. Для получения ссылки на **кодировщик служб мультимедиа Azure** перейдите в раздел [Получение обработчиков мультимедиа](media-services-get-media-processor.md).

###<a id="configure_preset"></a>Настройка параметров кодировщика служб мультимедиа Azure

Для настройка параметров кодирования кодировщика служб мультимедиа Azure имеется некоторое количество предварительно определенных предустановленных наборов, представленных легко запоминающимися строками. Кодировщик Dolby Digital Plus предоставляет богатый набор элементов управления; дополнительную информацию см. в разделе [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx). Таким образом, этот кодек не использует предустановленные наборы строк. Необходимо указать желаемые параметры кодировщика в XML-файле и отправить эти данные с задачей, как показано в следующем примере кода:
	
	string configuration = File.ReadAllText(pathToXMLConfigFile));

    ITask task = job.Tasks.AddNew("My Dolby Digital Plus Encode Task",
        processor,
        configuration,
        _clearConfig);

В этой статье приводится несколько примеров предустановленных наборов XML для настройки параметров кодировщика. Кодирование Dolby Digital Plus настраивается с помощью элемента [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx), который отображается как дочерний узел элемента <AudioProfile> в предустановленном наборе XML кодировщика служб мультимедиа Azure. Этот XML-элемент содержит некоторое количество атрибутов, управляющих разными элементами кодирования.

##Кодирование в многоканальный звук Dolby Digital Plus 5.1

Для кодирования в многоканальный звук Dolby Digital Plus 5.1 задайте для атрибутов Codec и EncoderMode значение "DolbyDigitalPlus". Число кодированных каналов задается с помощью атрибута AudioCodingMode элемента <DolbyDigitalPlusAudioProfile>. Для многоканального кодирования 5.1 задайте для AudioCodingMode значение "Mode32".

В данной статье содержится полная XML-предустановка для кодировщика служб мультимедиа Azure, которая создает файл формата MP4 с широкополосным H.264-видео с разрешением 1080р и многоканальным звуком Dolby Digital Plus 5.1. В этой предустановке также кодируется канал Low Frequency Effects (LFE), который задается путем установки значения true для атрибута LFEOn. Все остальные атрибуты, для которых не задаются значения, будут использовать значения по умолчанию.

Этот XML-код следует передать **кодировщик служб мультимедиа Azure**, чтобы создать задания кодирования, как описано в [этом](media-services-dotnet-encode-asset.md) разделе (только вместо стандартной строки вы передаете весь XML, как описано [здесь](#configure_preset)).


	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode32"
	              LFEOn="True"
	              SamplesPerSecond="48000"
	              BandwidthLimitingLowpassFilter="True"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="512"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="3"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Balanced"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="64"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	                <StreamInfo
	                  Size="1920, 1080">
	                  <Bitrate>
	                    <ConstantBitrate
	                      Bitrate="6000"
	                      IsTwoPass="False"
	                      BufferWindow="00:00:05" />
	                  </Bitrate>
	                </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##Кодирование в стереозвук Dolby Digital Plus

Для кодирования в Dolby Digital Plus задайте для атрибутов Codec и EncoderMode значение "DolbyDigitalPlus". Число кодированных каналов задается с помощью атрибута AudioCodingMode. Для кодирования стерео задайте для AudioCodingMode значение "Mode20". В следующем примере XML-предустановки показан элемент <DolbyDigitalPlusAudioProfile>, используемый для кодирования в звук 5.1. Все остальные атрибуты, для которых не задаются значения, будут использовать значения по умолчанию.

Этот XML-код следует передать **кодировщик служб мультимедиа Azure**, чтобы создать задания кодирования, как описано в [этом](media-services-dotnet-encode-asset.md) разделе (только вместо стандартной строки вы передаете весь XML, как описано [здесь](#configure_preset)).

	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode20"
	              LFEOn="False"
	              SamplesPerSecond="48000"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="128"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="1"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Speed"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="32"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	              <StreamInfo
	                Size="852, 480">
	                <Bitrate>
	                  <ConstantBitrate
	                    Bitrate="2200"
	                    IsTwoPass="False"
	                    BufferWindow="00:00:05" />
	                </Bitrate>
	              </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##Кодирование в несколько MP4-файлов 

Кодирование в несколько MP4-файлов можно выполнить в одном предустановленном наборе XML. Для каждого MP4-файла, который планируется создать, добавьте в конфигурацию элемент <Preset>. Каждый элемент <Preset> может содержать сведения о конфигурации для видео и звука, либо для того и другого.

###Конфигурация

Следующая конфигурация создаст следующие выходные файлы:

- 8 файлов только с видео в формате MP4
	- 1080p-видео @ 6000 кбит/с
	- Видео 1080p, 4700 кбит/с
	- 720p-видео @ 3400 кбит/с
	- Видео 960 x 540 @ 2250 кбит/с
	- Видео 960 x 540, 1500 кбит/с
	- Видео 640 x 380, 1000 кбит/с
	- Видео 640 x 380, 650 кбит/с
	- Видео 320 x 180, 400 кбит/с

- 5 файлов только со звуком в формате MP4
	- Стереозвук AAC @ 128 кбит/с
	- Звук AAC 5.1 @ 512 кбит/с
	- Стерео Dolby Digital Plus @ 128 кбит/с
	- Стерео многоканальное Dolby Digital Plus 5.1 @ 512 кбит/с
	- Стерео AAC @ 56 кбит/с
- Манифест ISM
- XML-файл, содержащий свойства созданных файлов в формате MP4.
		
		<?xml version="1.0" encoding="utf-16"?>
		<!--Created for Azure Media Encoder, May 16 2013 -->
		<Presets>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"   
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <VideoProfile Condition="SourceContainsVideo">
		            <HighH264VideoProfile
		              BFrameCount="3"
		              EntropyMode="Cabac"
		              RDOptimizationMode="Speed"
		              HadamardTransform="False"
		              SubBlockMotionSearchMode="Speed"
		              MultiReferenceMotionSearchMode="Balanced"
		              ReferenceBFrames="False"
		              AdaptiveBFrames="True"
		              SceneChangeDetector="True"
		              FastIntraDecisions="False"
		              FastInterDecisions="False"
		              SubPixelMode="Quarter"
		              SliceCount="0"
		              KeyFrameDistance="00:00:05"
		              InLoopFilter="True"
		              MEPartitionLevel="EightByEight"
		              ReferenceFrames="4"
		              SearchRange="64"
		              AutoFit="True"
		              Force16Pixels="False"
		              FrameRate="0"
		              SeparateFilesPerStream="True"
		              SmoothStreaming="False"
		              NumberOfEncoderThreads="0">
		              <Streams
		                AutoSize="False"
		                FreezeSort="False">
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="6000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="4700"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1280, 720">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="3400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="2250"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1500"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="650"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="320, 180">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		              </Streams>
		            </HighH264VideoProfile>
		          </VideoProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="PropagateSourceTimeStamps">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="6"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="2"
		              AudioCodingMode="Mode20"
		              LFEOn="False"
		              NinetyDegreePhaseShiftSurrounds="False"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="False"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile Condition="SourceContainsAudio">
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="6"
		              AudioCodingMode="Mode32"
		              LFEOn="True"
		              NinetyDegreePhaseShiftSurrounds="True"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="True"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="56"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		</Presets>

##Создание коммерческих служб кодирования

Некоторым клиентам может понадобиться построить коммерческую службу кодирования в дополнение к службам мультимедиа Azure. Если вы создаете такую встроенную службу, важно, чтобы все параметры кодирования Dolby Digital Plus были доступны. Убедитесь, что указаны все параметры в теге <DolbyDigitalPlusAudioProfile> и могут настраиваться конечными пользователями. Обратитесь по адресу prolicensingsupport@dolby.com за инструкциями по обеспечению доступности этих параметров.

##Использование поддержки Dolby Professional Loudness Metering (DPLM)

Кодировщик служб мультимедиа Azure начиная может использовать пакет SDK DPLM для измерения громкости диалога во входной звуковой дорожке и установки правильного значения параметра DialogNormalization. Эта функция включается только в том случае, если звук кодируется в Dolby Digital Plus. DPLM настраивается в файле предустановленной конфигурации с помощью элемента <LoudnessMetering>, который является дочерним элемента <DolbyDigitalPlusAudioProfile>. В следующем примере предустановленного набора демонстрируется настройка DPLM:
	
	<?xml version="1.0" encoding="utf-16"?>
	<Preset
	  Version="5.0">
	  <Job />
	  <MediaFile>
	    <OutputFormat>
	      <MP4OutputFormat
	        StreamCompatibility="Standard">
	    <AudioProfile>
	             <DolbyDigitalPlusAudioProfile
	               Codec="DolbyDigitalPlus"
	               EncoderMode="DolbyDigitalPlus"
	               DolbySurroundMode="NotIndicated"
	               StereoDownmixPreference="NotIndicated"
	               SamplesPerSecond="48000"
	               AudioCodingMode="Mode20"
	               Channels="2"
	               BitsPerSample="24">
	               <LoudnessMetering
	                 Mode= "ITU_R_BS_1770_2_DI"
	                 SpeechThreshold="20"
	                 TruePeakEmphasis="false"
	                 TruePeakDCBlock="false" />
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="320"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	     </DolbyDigitalPlusAudioProfile>
	    </AudioProfile>
	      </MP4OutputFormat>
	    </OutputFormat>
	  </MediaFile>
	</Preset>

Элемент <LoudnessMetering> может быть указан только в элементе <DolbyDigitalPlusAudioProfile>. Если используется элемент <LoudnessMetering>, то атрибут DialogNormalization не должен использоваться. Кодировщик выдает ошибку, если используется и элемент <LoudnessMetering>, и атрибут. Все атрибуты LoudnessMetering являются необязательными, и по умолчанию кодировщик будет использовать значения, рекомендованные корпорацией Dolby Laboratories.

Все атрибуты описываются в следующих разделах.

###Атрибут Mode

Этот атрибут определяет режим измерения громкости. Допустимые значения:

 
**ITU\_R\_BS\_1770\_2\_DI** — ((по умолчанию) указывает ITU-R BS.1770-2, а также Dialogue Intelligence

**ITU\_R\_BS\_1770\_1\_DI** — указывает ITU-R BS.1770-1, а также Dialogue Intelligence

**ITU\_R\_BS\_1770\_2** — указывает ITU-R BS.1770-2

**LEQA\_DI** — указывает Leq(A), а также Dialogue Intelligence

**Примечание**.

Режим **EBU R128** можно включить через **ITU\_R\_BS\_1770\_2\_DI**

**Leq(A)** включен исключительно по причинам обратной совместимости и должен использоваться только в конкретных старых рабочих процессах

**ITU** недавно выпустил обновление BS.1770-3, эквивалентное BS.1770-2 со значениями TruePeakDCBlock и TruePeakEmphasis, установленными в False.

###Атрибут SpeechThreshold

Определяет речевой порог, используемый DPLM для создания интегрированной громкости (например, выбор между стробированием речи, стробированием уровня и вариантом без стробирования). Пороговое значение параметра речи находится в пределах от 0 % до 100 % с шагом в 1 %. Данный параметр действует только тогда, когда DPLM настроен на режим, который использует Dialogue Intelligence, что означает, что он может быть определен, если атрибут Mode имеет значение ITU\_R\_BS\_1770\_2\_DI или ITU\_R\_BS\_1770\_1\_DI. Значение по умолчанию устанавливается, когда атрибут Mode имеет значение ITU\_R\_BS\_1770\_2\_DI или ITU\_R\_BS\_1770\_1\_DI, и оно равно 20%. Значения для этого атрибута должны быть указаны в диапазоне 0,1 – 100.

###Атрибут TruePeakDCBlock

Данный входной параметр определяет, включен (true) или выключен (false) ли блок DC при измерении истинного пикового уровня. Для получения более подробной информации по блоку DC обращайтесь к ITU‐R BS.1770-2. Значение по умолчанию — false.

###Атрибут TruePeakEmphasis

Определяет, включен (true) или выключен (false) ли фильтр компенсации искажений при измерении истинного пикового уровня. Для получения более подробной информации по фильтру компенсации искажений обращайтесь к ITU‐R BS.1770-2. Значение по умолчанию — false.


###Результаты DPLM

Если задачи кодирования определяют использование DPLM, результаты измерения громкости будут включены в метаданные XML в выходном объекте. Ниже приведен пример.
	
	<LoudnessMeteringResultParameters 
	     DPLMVersionInformation="Dolby Professional Loudness Metering Development Kit Version 1.0"
	     DialogNormalization="-15" 
	     IntegratedLoudness="-14.8487606" 
	     IntegratedLoudnessGatingMethod="2" 
	     IntegratedLoudnessSpeechPercentage="11.673481" 
	     SamplePeak="-0.7028221" 
	     TruePeak="0.705999851" />

Все атрибуты описываются ниже.

**DPLMVersionInformation** — строка, представляющая версию используемого пакета SDK DPLM.

**DialogNormalization** — значение DialNorm в децибелах, измеренное из входящего аудиосигнала, которое будет встроено в выходной поток DD+, в диапазоне {-31, -30, …, -1} дБ.

**IntegratedLoudness** — интегрированная громкость, измеренная DPLM, в диапазоне от -70 до +10 LKFS/dBFS (где dBFS используется, только если значение параметра Mode установлено в LEQA\_DI).

**IntegratedLoudnessGatingMethod** — допустимые значения: 0 — нет или без стробирования, 1 — с условным стробированием; 2 — стробирование по уровню.

**IntegratedLoudnessSpeechPercentage** — указывает процент входной временной шкалы мультимедиа, когда обнаруживается речь. Диапазон значений — от 0% до 100%.

**SamplePeak** — указывает наибольшее абсолютное опорное значение в любом канале после сброса измерения и может быть в диапазоне от -70 до +10 dBFS.

**TruePeak** — указывает наибольшее абсолютное опорное значение в любом канале после сброса измерения. Описание понятия "абсолютное опорное значение" см. в ITU‐R BS.1770‐2. Значения могут находиться в диапазоне от -70 до 12,04 dBTP.
 

##Схемы обучения работе со службами мультимедиа

Схемы обучения AMS можно просмотреть здесь:

- [Рабочий процесс для потоковой передачи в реальном времени в службах AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Рабочий процесс для потоковой передачи по запросу в службах AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

<!---HONumber=Oct15_HO3-->