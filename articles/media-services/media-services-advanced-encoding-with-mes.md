<properties 
	pageTitle="Дополнительное кодирование с помощью стандартного кодировщика мультимедиа" 
	description="В этом разделе показано, как выполнять расширенные задачи кодирования, настраивая предустановки задач Media Encoder Standard. В этом разделе показано, как использовать пакет SDK служб мультимедиа для .NET для создания задания и задачи кодирования. В нем также показано, как предоставить пользовательские предустановки для задания кодирования." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2016"   
	ms.author="juliako"/>


#Дополнительное кодирование с помощью стандартного кодировщика мультимедиа

##Обзор

В этом разделе показано, как выполнять расширенные задачи кодирования с помощью Media Encoder Standard. В этом разделе показано, [как с помощью .NET создать задачу кодирования и задание, которое выполняет эту задачу](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet). В нем также показано, как предоставить пользовательские предустановки для задачи кодирования. Описания элементов, использующихся в данных предустановках, содержатся в [этом документе](https://msdn.microsoft.com/library/mt269962.aspx).

В статье демонстрируются пользовательские предустановки, которые выполняют следующие задачи кодирования:

- [Создание эскизов](media-services-custom-mes-presets-with-dotnet.md#thumbnails)
- [Монтаж видео (обрезка)](media-services-custom-mes-presets-with-dotnet.md#trim_video)
- [Создание наложения](media-services-custom-mes-presets-with-dotnet.md#overlay)
- [Вставка звуковой дорожки с тишиной, если входные данные не содержат звука](media-services-custom-mes-presets-with-dotnet.md#silent_audio)
- [Отключение автоматического устранения чересстрочной развертки](media-services-custom-mes-presets-with-dotnet.md#deinterlacing)
- [Предустановки только для звука](media-services-custom-mes-presets-with-dotnet.md#audio_only)
- [Объединение двух или нескольких видеофайлов](media-services-custom-mes-presets-with-dotnet.md#concatenate)
- [Обрезка видео с помощью стандартного кодировщика мультимедиа](media-services-custom-mes-presets-with-dotnet.md#crop)
- [Вставка видеодорожки, если во входных данных нет видео](media-services-custom-mes-presets-with-dotnet.md#no_video).

##<a id="encoding_with_dotnet"></a>Кодирование с помощью пакета SDK служб мультимедиа для .NET

В следующем примере кода пакет SDK служб мультимедиа используется для выполнения следующих задач.

- Создание задания кодирования.
- Получение ссылки на стандартный кодировщик мультимедиа.
- Загрузка пользовательских предустановок в формате XML или JSON. Вы можете сохранить код [XML](media-services-custom-mes-presets-with-dotnet.md#xml) или [JSON](media-services-custom-mes-presets-with-dotnet.md#json) в файл и использовать приведенный ниже код для загрузки файла.

		// Load the XML (or JSON) from the local file.
	    string configuration = File.ReadAllText(fileName);  
- Добавление задачи кодирования в задание.
- Указание входного ресурса-контейнера для кодирования.
- Создание выходного ресурса-контейнера с закодированным ресурсом.
- Добавление обработчика событий для проверки хода выполнения задания.
- Отправка задания.
	
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Net;
		using System.Security.Cryptography;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Newtonsoft.Json.Linq;
		using System.Threading;
		using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
		using System.Web;
		using System.Globalization;
		
		namespace CustomizeMESPresests
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        private static readonly string _mediaFiles =
		            Path.GetFullPath(@"../..\Media");
		
		        private static readonly string _singleMP4File =
		            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
		
		        static void Main(string[] args)
		        {
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the chached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            // Get an uploaded asset.
		            var asset = _context.Assets.FirstOrDefault();
		
		            // Encode and generate the output using custom presets.
		            EncodeToAdaptiveBitrateMP4Set(asset);
		
		            Console.ReadLine();
		        }
		
		        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
				{
				    // Declare a new job.
				    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
				    // Get a media processor reference, and pass to it the name of the 
				    // processor to use for the specific task.
				    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
				
		
				    // Load the XML (or JSON) from the local file.
				    string configuration = File.ReadAllText("CustomPreset_JSON.json");
				
				    // Create a task
		            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
		                processor,
		                configuration,
		                TaskOptions.None);
				
				    // Specify the input asset to be encoded.
				    task.InputAssets.Add(asset);
				    // Add an output asset to contain the results of the job. 
				    // This output is specified as AssetCreationOptions.None, which 
				    // means the output asset is not encrypted. 
				    task.OutputAssets.AddNew("Output asset",
				        AssetCreationOptions.None);
				
				    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
				    job.Submit();
				    job.GetExecutionProgressTask(CancellationToken.None).Wait();
				
				    return job.OutputMediaAssets[0];
				}
		
		        static public IAsset UploadMediaFilesFromFolder(string folderPath)
		        {
		            IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
		
		            foreach (var af in asset.AssetFiles)
		            {
		                // The following code assumes 
		                // you have an input folder with one MP4 and one overlay image file.
		                if (af.Name.Contains(".mp4"))
		                    af.IsPrimary = true;
		                else
		                    af.IsPrimary = false;
		
		                af.Update();
		            }
		
		            return asset;
		        }
		
		
		        static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
		        {
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
		            // Get a media processor reference, and pass to it the name of the 
		            // processor to use for the specific task.
		            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		
		            // Load the XML (or JSON) from the local file.
		            string configuration = File.ReadAllText(customPresetFileName);
		
		            // Create a task
		            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input assets to be encoded.
		            // This asset contains a source file and an overlay file.
		            task.InputAssets.Add(assetSource);
		
		            // Add an output asset to contain the results of the job. 
		            task.OutputAssets.AddNew("Output asset",
		                AssetCreationOptions.None);
		
		            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
		            job.Submit();
		            job.GetExecutionProgressTask(CancellationToken.None).Wait();
		
		            return job.OutputMediaAssets[0];
		        }
		

		        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine("Job state changed event:");
		            Console.WriteLine("  Previous state: " + e.PreviousState);
		            Console.WriteLine("  Current state: " + e.CurrentState);
		            switch (e.CurrentState)
		            {
		                case JobState.Finished:
		                    Console.WriteLine();
		                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
		                    break;
		                case JobState.Canceling:
		                case JobState.Queued:
		                case JobState.Scheduled:
		                case JobState.Processing:
		                    Console.WriteLine("Please wait...\n");
		                    break;
		                case JobState.Canceled:
		                case JobState.Error:
		
		                    // Cast sender as a job.
		                    IJob job = (IJob)sender;
		
		                    // Display or log error details as needed.
		                    break;
		                default:
		                    break;
		            }
		        }
		
		
		        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		        {
		            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		            if (processor == null)
		                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		            return processor;
		        }
		
		    }
		}


##Поддержка относительных размеров

При создании эскизов вам не нужно всегда указывать выходную ширину и высоту в пикселях. Эти значения можно указать в процентах, используя диапазон [1%, ..., 100%].

###Предустановка JSON 
	
	"Width": "100%",
	"Height": "100%"

###Предустановка XML
	
	<Width>100%</Width>
	<Height>100%</Height>
	
##<a id="thumbnails"></a>Создание эскизов

В этом разделе показано, как настроить предустановку, которая создает эскизы. Предустановка, определенная ниже, содержит сведения о том, как должен кодироваться файл, а также сведения, необходимые для создания эскизов. Вы можете использовать любую из предустановок Media Encoder Standard (MES), которые приведены [здесь](https://msdn.microsoft.com/library/mt269960.aspx), и добавить в нее код для создания эскизов.

>[AZURE.NOTE]Если видео преобразуется в формат с одной скоростью, то параметр **SceneChangeDetection** в следующей конфигурации может иметь только значение True. Если видео преобразуется в формат с несколькими скоростями, а параметр **SceneChangeDetection** имеет значение True, то кодировщик выдает ошибку.


Сведения о схеме см. в [этом](https://msdn.microsoft.com/library/mt269962.aspx) разделе.

Обязательно изучите раздел [Рекомендации](media-services-custom-mes-presets-with-dotnet.md#considerations).

###<a id="json"></a>Предустановка JSON


	{
	  "Version": 1.0,
	  "Codecs": [
	    {
	      "KeyFrameInterval": "00:00:02",
	      "SceneChangeDetection": "true",
	      "H264Layers": [
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 4500,
	          "MaxBitrate": 4500,
	          "BufferWindow": "00:00:05",
	          "Width": 1280,
	          "Height": 720,
	          "ReferenceFrames": 3,
	          "EntropyMode": "Cabac",
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	   
	        }
	      ],
	      "Type": "H264Video"
	    },
	    {
	      "JpgLayers": [
	        {
	          "Quality": 90,
	          "Type": "JpgLayer",
	          "Width": 640,
	          "Height": 360
	        }
	      ],
	      "Start": "{Best}",
	      "Type": "JpgImage"
	    },
	    {
	      "PngLayers": [
	        {
	          "Type": "PngLayer",
	          "Width": 640,
	          "Height": 360,
	        }
	      ],
	      "Start": "00:00:01",
		  "Step": "00:00:10",
	      "Range": "00:00:58",
	      "Type": "PngImage"
	    },
	    {
	      "BmpLayers": [
	        {
	          "Type": "BmpLayer",
	          "Width": 640,
	          "Height": 360
	        }
	      ],
	      "Start": "10%",
		  "Step": "10%",
	      "Range": "90%",
	      "Type": "BmpImage"
	    },
	    {
	      "Channels": 2,
	      "SamplingRate": 48000,
	      "Bitrate": 128,
	      "Type": "AACAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}_{Index}{Extension}",
	      "Format": {
	        "Type": "JpgFormat"
	      }
	    },
	    {
	      "FileName": "{Basename}_{Index}{Extension}",
	      "Format": {
	        "Type": "PngFormat"
	      }
	    },
	    {
	      "FileName": "{Basename}_{Index}{Extension}",
	      "Format": {
	        "Type": "BmpFormat"
	      }
	    },
	    {
	      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	}


###<a id="xml"></a>Предустановка XML


	<?xml version="1.0" encoding="utf-16"?>
	<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
	  <Encoding>
	    <H264Video>
	      <KeyFrameInterval>00:00:02</KeyFrameInterval>
	      <SceneChangeDetection>true</SceneChangeDetection>
	      <H264Layers>
	        <H264Layer>
	          <Bitrate>4500</Bitrate>
	          <Width>1280</Width>
	          <Height>720</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>4500</MaxBitrate>
	        </H264Layer>
	      </H264Layers>
	    </H264Video>
	    <AACAudio>
	      <Profile>AACLC</Profile>
	      <Channels>2</Channels>
	      <SamplingRate>48000</SamplingRate>
	      <Bitrate>128</Bitrate>
	    </AACAudio>
	    <JpgImage Start="{Best}">
	      <JpgLayers>
	        <JpgLayer>
	          <Width>640</Width>
	          <Height>360</Height>
	          <Quality>90</Quality>
	        </JpgLayer>
	      </JpgLayers>
	    </JpgImage>
	    <BmpImage Start="10%" Step="10%" Range="90%">
	      <BmpLayers>
	        <BmpLayer>
	          <Width>640</Width>
	          <Height>360</Height>
	        </BmpLayer>
	      </BmpLayers>
	    </BmpImage>
	    <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
	      <PngLayers>
	        <PngLayer>
	          <Width>640</Width>
	          <Height>360</Height>
	        </PngLayer>
	      </PngLayers>
	    </PngImage>
	  </Encoding>
	  <Outputs>
	    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
	      <MP4Format />
	    </Output>
	    <Output FileName="{Basename}_{Index}{Extension}">
	      <JpgFormat />
	    </Output>
	    <Output FileName="{Basename}_{Index}{Extension}">
	      <BmpFormat />
	    </Output>
	    <Output FileName="{Basename}_{Index}{Extension}">
	      <PngFormat />
	    </Output>
	  </Outputs>
	</Preset>

###Рекомендации

Действительны следующие условия.

- Использование явных меток времени для элементов Start, Step или Range предполагает, что входные данные составляют не менее одной минуты.
- Элементы Jpg, Png и BmpImage обладают строковыми атрибутами Start, Step и Range, которые можно интерпретировать следующим образом:

	- номер кадра, если эти атрибуты выражены неотрицательными целыми числами, например "Start": "120";
	- значение относительно длительности источника, если атрибуты указаны с суффиксом "%", например "Start": "15%";
	- метка времени, если атрибуты имеют формат ЧЧ:ММ:СС...: "Start" : "00:01:00".

	При желании условные обозначения можно комбинировать.
	
	Кроме того, атрибут Start поддерживает также специальный макрос {Best}, который пытается определить первый "интересный" кадр содержимого NOTE: (если атрибут Start имеет значение {Best}, атрибуты Step и Range игнорируются)
	
	- По умолчанию Start:{Best}
- Для атрибута Image должен быть указан формат выходных данных: Jpg/Png/BmpFormat. Если он присутствует, Media Encoder Standard сопоставляет JpgVideo с JpgFormat и т. д. OutputFormat представляет новый макрос, связанный с кодеком изображений: {Index}, который необходимо указывать для форматов вывода изображений (один и только один раз).

##<a id="trim_video"></a>Монтаж видео (обрезка)

В этом разделе рассказывается об изменении предустановок кодировщика для обрезки входного видеоролика, при котором входной видеоролик представляет собой так называемый мезонинный файл или файл по требованию. С помощью кодировщика можно обрезать ресурс-контейнер, который записывается или архивируется из потока, передающегося в реальном времени. Подробные сведения об этом см. в [этом блоге](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Для обрезки видео можно взять любую из предустановок Media Encoder Standard, которые описаны [здесь](https://msdn.microsoft.com/library/mt269960.aspx), и изменить элемент **Sources** (как показано ниже). Значение StartTime должно соответствовать абсолютной метке времени входящего видео. Например, если первый кадр входящего видео имеет отметку времени "12:00:10.000", значение StartTime должно равняться или быть больше 12:00:10.000. В приведенном ниже примере мы предполагаем, что входящее видео имеет отметку времени начала, равную нулю. Элемент **Sources** должен размещаться в начале предустановки.
 
###<a id="json"></a>Предустановка JSON
	
	{
	  "Version": 1.0,
	  "Sources": [
	    {
	      "StartTime": "00:00:04",
	      "Duration": "00:00:16"
	    }
	  ],
	  "Codecs": [
	    {
	      "KeyFrameInterval": "00:00:02",
	      "StretchMode": "AutoSize",
	      "H264Layers": [
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 3400,
	          "MaxBitrate": 3400,
	          "BufferWindow": "00:00:05",
	          "Width": 1280,
	          "Height": 720,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 2250,
	          "MaxBitrate": 2250,
	          "BufferWindow": "00:00:05",
	          "Width": 960,
	          "Height": 540,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 1500,
	          "MaxBitrate": 1500,
	          "BufferWindow": "00:00:05",
	          "Width": 960,
	          "Height": 540,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 1000,
	          "MaxBitrate": 1000,
	          "BufferWindow": "00:00:05",
	          "Width": 640,
	          "Height": 360,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 650,
	          "MaxBitrate": 650,
	          "BufferWindow": "00:00:05",
	          "Width": 640,
	          "Height": 360,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 400,
	          "MaxBitrate": 400,
	          "BufferWindow": "00:00:05",
	          "Width": 320,
	          "Height": 180,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        }
	      ],
	      "Type": "H264Video"
	    },
	    {
	      "Profile": "AACLC",
	      "Channels": 2,
	      "SamplingRate": 48000,
	      "Bitrate": 128,
	      "Type": "AACAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	} 

###Предустановка XML
	
Для обрезки видео можно взять любую из предустановок Media Encoder Standard, которые описаны [здесь](https://msdn.microsoft.com/library/mt269960.aspx), и изменить элемент **Sources** (как показано ниже).

	<?xml version="1.0" encoding="utf-16"?>
	<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
	  <Sources>
	    <Source StartTime="PT4S" Duration="PT14S"/>
	  </Sources>
	  <Encoding>
	    <H264Video>
	      <KeyFrameInterval>00:00:02</KeyFrameInterval>
	      <H264Layers>
	        <H264Layer>
	          <Bitrate>3400</Bitrate>
	          <Width>1280</Width>
	          <Height>720</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>3400</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>2250</Bitrate>
	          <Width>960</Width>
	          <Height>540</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>2250</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>1500</Bitrate>
	          <Width>960</Width>
	          <Height>540</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>1500</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>1000</Bitrate>
	          <Width>640</Width>
	          <Height>360</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>1000</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>650</Bitrate>
	          <Width>640</Width>
	          <Height>360</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>650</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>400</Bitrate>
	          <Width>320</Width>
	          <Height>180</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>400</MaxBitrate>
	        </H264Layer>
	      </H264Layers>
	    </H264Video>
	    <AACAudio>
	      <Profile>AACLC</Profile>
	      <Channels>2</Channels>
	      <SamplingRate>48000</SamplingRate>
	      <Bitrate>128</Bitrate>
	    </AACAudio>
	  </Encoding>
	  <Outputs>
	    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
	      <MP4Format />
	    </Output>
	  </Outputs>
	</Preset>

##<a id="overlay"></a>Создание наложения

Стандартный кодировщик служб мультимедиа позволяет наложить изображение на существующее видео. В настоящее время поддерживаются следующие форматы: png, jpg, gif и bmp. Предустановка, определенная ниже, представляет собой базовый пример наложения видео.

Наряду с определением файла предустановки также необходимо сообщить службам мультимедиа, какой файл в ресурсе-контейнере содержит изображение для наложения, а какой — исходное видео, на которое вы хотите наложить изображение. Видеофайл должен быть **первичным** файлом.

В приведенном выше примере .NET определены две функции: **UploadMediaFilesFromFolder** и **EncodeWithOverlay**. Функция UploadMediaFilesFromFolder передает файлы из папки (например, BigBuckBunny.mp4 и Image001.png) и задает MP4-файл в качестве первичного файла в ресурсе-контейнере. Функция **EncodeWithOverlay** использует настраиваемый файл предустановки, который был передан в нее (например, приведенную ниже предустановку), для создания задачи кодирования.

>[AZURE.NOTE]Текущие ограничения:
>
>Настройка прозрачности наложения не поддерживается.
>
>Исходный видеофайл и файл образа наложения должны находиться в одном ресурсе, причем видеофайл должен быть задан как основной файл в этом ресурсе.

###Предустановка JSON
	
	{
	  "Version": 1.0,
	  "Sources": [
	    {
	      "Streams": [],
	      "Filters": {
	        "VideoOverlay": {
	          "Position": {
	            "X": 100,
	            "Y": 100,
	            "Width": 100,
	            "Height": 50
	          },
	          "AudioGainLevel": 0.0,
	          "MediaParams": [
	            {
	              "OverlayLoopCount": 1
	            },
	            {
	              "IsOverlay": true,
	              "OverlayLoopCount": 1,
	              "InputLoop": true
	            }
	          ],
	          "Source": "Image001.png",
	          "Clip": {
	            "Duration": "00:00:05"
	          },
	          "FadeInDuration": {
	            "Duration": "00:00:01"
	          },
	          "FadeOutDuration": {
	            "StartTime": "00:00:03",
	            "Duration": "00:00:04"
	          }
	        }
	      },
	      "Pad": true
	    }
	  ],
	  "Codecs": [
	    {
	      "KeyFrameInterval": "00:00:02",
	      "H264Layers": [
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 1045,
	          "MaxBitrate": 1045,
	          "BufferWindow": "00:00:05",
	          "ReferenceFrames": 3,
	          "EntropyMode": "Cavlc",
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "Width": "640",
	          "Height": "360",
	          "FrameRate": "0/1"
	        }
	      ],
	      "Type": "H264Video"
	    },
	    {
	      "Type": "CopyAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}{Extension}",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	}


###Предустановка XML
	
	<?xml version="1.0" encoding="utf-16"?>
	<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
	  <Sources>
	    <Source>
	      <Streams />
	      <Filters>
	        <VideoOverlay>
	          <Source>Image001.png</Source>
	          <Clip Duration="PT5S" />
	          <FadeInDuration Duration="PT1S" />
	          <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
	          <Position X="100" Y="100" Width="100" Height="50" />
	          <Opacity>0</Opacity>
	          <AudioGainLevel>0</AudioGainLevel>
	          <MediaParams>
	            <MediaParam>
	              <IsOverlay>false</IsOverlay>
	              <OverlayLoopCount>1</OverlayLoopCount>
	              <InputLoop>false</InputLoop>
	            </MediaParam>
	            <MediaParam>
	              <IsOverlay>true</IsOverlay>
	              <OverlayLoopCount>1</OverlayLoopCount>
	              <InputLoop>true</InputLoop>
	            </MediaParam>
	          </MediaParams>
	        </VideoOverlay>
	      </Filters>
	      <Pad>true</Pad>
	    </Source>
	  </Sources>
	  <Encoding>
	    <H264Video>
	      <KeyFrameInterval>00:00:02</KeyFrameInterval>
	      <H264Layers>
	        <H264Layer>
	          <Bitrate>1045</Bitrate>
	          <Width>640</Width>
	          <Height>360</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>0</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cavlc</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>1045</MaxBitrate>
	        </H264Layer>
	      </H264Layers>
	    </H264Video>
	    <CopyAudio />
	  </Encoding>
	  <Outputs>
	    <Output FileName="{Basename}{Extension}">
	      <MP4Format />
	    </Output>
	  </Outputs>
	</Preset>


##<a id="silent_audio"></a>Вставка звуковой дорожки с тишиной, если входные данные не содержат звука

По умолчанию при отправке кодировщику входных данных, которые содержат только видео и не содержат звука, выходной ресурс-контейнер будет содержать файлы только с видеоданными. Некоторые проигрыватели не смогут обработать такие выходные потоки. Этот параметр можно использовать для принудительного добавления кодировщиком звуковой дорожки с тишиной к выходным данным в этом сценарии.

Чтобы заставить кодировщик создать ресурс, содержащий звуковую дорожку с тишиной, когда входные данные не содержат звука, укажите значение "InsertSilenceIfNoAudio".

Вы можете использовать любую из предустановок Media Encoder Standard, которые определены [здесь](https://msdn.microsoft.com/library/mt269960.aspx), и внести в нее следующие изменения.

###Предустановка JSON

    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

###Предустановка XML

    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

##<a id="deinterlacing"></a>Отключение автоматического устранения чересстрочной развертки

Клиентам не нужно ничего делать, если они хотят, чтобы содержимое в чересстрочной развертке автоматически устранялось. Если автоматическое устранение чересстрочной развертки включено (по умолчанию), стандартный кодировщик мультимедиа не выполняет автоматическое обнаружение чересстрочных кадров и устраняет чересстрочную развертку кадров, которые помечены как чересстрочные.

Автоматическое удаление чересстрочной развертки можно отключить. Однако это делать не рекомендуется.

###Предустановка JSON
	
	"Sources": [
	{
	 "Filters": {
	    "Deinterlace": {
	      "Mode": "Off"
	    }
	  },
	}
	]

###Предустановка XML
	
	<Sources>
	<Source>
	  <Filters>
	    <Deinterlace>
	      <Mode>Off</Mode>
	    </Deinterlace>
	  </Filters>
	</Source>
	</Sources>


##<a id="audio_only"></a>Предустановки только для звука

В этом разделе демонстрируются две предустановки стандартного кодировщика мультимедиа только для звука: "Звук в формате AAC" и "Звук в формате AAC хорошего качества".

###Звук в формате AAC 

	{
	  "Version": 1.0,
	  "Codecs": [
	    {
	      "Profile": "AACLC",
	      "Channels": 2,
	      "SamplingRate": 48000,
	      "Bitrate": 128,
	      "Type": "AACAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	}

###Звук в формате AAC хорошего качества

	{
	  "Version": 1.0,
	  "Codecs": [
	    {
	      "Profile": "AACLC",
	      "Channels": 2,
	      "SamplingRate": 48000,
	      "Bitrate": 192,
	      "Type": "AACAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	}

##<a id="concatenate"></a>Сцепка нескольких видеофайлов

В следующем примере показано, как создать предустановку для сцепления двух или более видеофайлов. Наиболее распространенный сценарий — это добавление названия или трейлера к основному видео. Используется, когда совместно редактируемые видеофайлы имеют одинаковые свойства (разрешение видео, частоту кадров, количество аудиодорожек и т. д.). Не следует комбинировать видео с разной частотой кадров или разным количеством аудиодорожек.

###Требования и рекомендации

- В исходных видеофайлах должна быть только одна аудиодорожка.
- У всех исходных видеофайлов должна быть одинаковая частота кадров.
- Необходимо загрузить видеофайлы в отдельные ресурсы и задать видео как основной файл в каждом ресурсе.
- Необходимо знать длительность видеофайлов.
- Приведенные ниже примеры предустановок предполагают, что все исходные видеофайлы начинаются с нулевой отметки. Если видеофайлы начинаются с разных меток времени, необходимо изменить значения StartTime (как при работе с архивами прямых трансляций).
- Предустановка JSON включает прямые ссылки на значения AssetID исходных ресурсов.
- Образец кода предполагает, что предустановка JSON сохранена как локальный файл, например C:\\supportFiles\\preset.json. Кроме того, предполагается, что ресурсы созданы путем загрузки двух видеофайлов и что вам известны соответствующие значения AssetID.
- Фрагмент кода и предустановка JSON демонстрирует пример сцепления двух видеофайлов. Количество видеофайлов можно увеличить следующим образом.

	1. Вызвать метод task.InputAssets.Add() несколько раз, чтобы добавить несколько видео по порядку.
	2. Внести соответствующие права в элемент Sources (Источники) файла JSON, добавив новые записи в том же порядке.


###Код .NET

	
	IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
	IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();
	
	// Declare a new job.
	IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
	// Get a media processor reference, and pass to it the name of the 
	// processor to use for the specific task.
	IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
	
	// Load the XML (or JSON) from the local file.
	string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");
	
	// Create a task
	ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
	    processor,
	    configuration,
	    TaskOptions.None);
	
	// Specify the input videos to be concatenated (in order).
	task.InputAssets.Add(asset1);
	task.InputAssets.Add(asset2);
	// Add an output asset to contain the results of the job. 
	// This output is specified as AssetCreationOptions.None, which 
	// means the output asset is not encrypted. 
	task.OutputAssets.AddNew("Output asset",
	    AssetCreationOptions.None);
	
	job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
	job.Submit();
	job.GetExecutionProgressTask(CancellationToken.None).Wait();

###Предустановка JSON

Обновите настроенную вами предустановку, указав идентификаторы ресурсов, которые нужно сцепить, и соответствующий промежуток времени для каждого видеофайла.

	{
	  "Version": 1.0,
	  "Sources": [
	    {
	      "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
	      "StartTime": "00:00:01",
	      "Duration": "00:00:15"
	    },
	    {
	      "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
	      "StartTime": "00:00:02",
	      "Duration": "00:00:05"
	    }
	  ],
	  "Codecs": [
	    {
	      "KeyFrameInterval": "00:00:02",
	      "SceneChangeDetection": true,
	      "H264Layers": [
	        {
	          "Level": "auto",
	          "Bitrate": 1800,
	          "MaxBitrate": 1800,
	          "BufferWindow": "00:00:05",
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "Width": "640",
	          "Height": "360",
	          "FrameRate": "0/1"
	        }
	      ],
	      "Type": "H264Video"
	    },
	    {
	      "Channels": 2,
	      "SamplingRate": 48000,
	      "Bitrate": 128,
	      "Type": "AACAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	}

##<a id="crop"></a>Обрезка видео с помощью Media Encoder Standard

Ознакомьтесь с разделом [Обрезка видео с помощью стандартного кодировщика мультимедиа](media-services-crop-video.md).

##<a id="no_video"></a>Вставка видеодорожки, если во входных данных нет видео

По умолчанию при отправке кодировщику входных данных, которые содержат только звук и не содержат аудио, выходной ресурс-контейнер будет содержать файлы только с аудиоданными. Некоторые проигрыватели, в том числе Azure Media Player (см. [этот раздел](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)), могут не поддерживать обработку таких потоков. Этот параметр можно использовать для принудительного добавления кодировщиком монохромной видеодорожки в выходные данные в этом сценарии.

>[AZURE.NOTE]Это увеличивает размер выходного ресурса-контейнера, а, следовательно, и затраты на такую задачу кодирования. Следует выполнить тесты, чтобы убедиться, что результирующее увеличение незначительно влияет на ежемесячные расходы.

### Вставка видео только с минимальной скоростью

Предположим, что вы используете предустановку кодирования с несколькими скоростями, например [H264 Multiple Bitrate 720p](https://msdn.microsoft.com/library/mt269960.aspx), чтобы закодировать для потоковой передачи весь входной каталог, содержащий смесь видео- и аудиофайлов. В этом случае, если входные данные не содержат видео, может потребоваться указать кодировщику принудительно вставлять монохромную видеодорожку только с самой низкой скоростью, а не с каждой выходной скоростью. Для этого необходимо указать флаг InsertBlackIfNoVideoBottomLayerOnly.

Вы можете использовать любую из предустановок Media Encoder Standard, которые определены [здесь](https://msdn.microsoft.com/library/mt269960.aspx), и внести в нее следующие изменения.

#### Предустановка JSON

	{
	      "KeyFrameInterval": "00:00:02",
	      "StretchMode": "AutoSize",
	      "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
	      "H264Layers": [
	      …
	      ]
	}

#### Предустановка XML

	<KeyFrameInterval>00:00:02</KeyFrameInterval>
	<StretchMode>AutoSize</StretchMode>
	<Condition>InsertBlackIfNoVideoBottomLayerOnly</Condition>

### Вставка видео со всеми выходными скоростями

Предположим, что вы используете предустановку кодирования с несколькими скоростями, например [H264 Multiple Bitrate 720p](https://msdn.microsoft.com/library/mt269960.aspx), чтобы закодировать для потоковой передачи весь входной каталог, содержащий смесь видео- и аудиофайлов. В этом случае, если входные данные не содержат видео, может потребоваться указать кодировщику принудительно вставлять монохромную видеодорожку с каждой из выходных скоростей. Это гарантирует, что выходные ресурсы-контейнеры будут однородны по количеству видео- и аудиодорожек. Чтобы получить такой результат, необходимо указать флаг InsertBlackIfNoVideo.

Вы можете использовать любую из предустановок Media Encoder Standard, которые определены [здесь](https://msdn.microsoft.com/library/mt269960.aspx), и внести в нее следующие изменения.

#### Предустановка JSON

	{
	      "KeyFrameInterval": "00:00:02",
	      "StretchMode": "AutoSize",
	      "Condition": "InsertBlackIfNoVideo",
	      "H264Layers": [
	      …
	      ]
	}

#### Предустановка XML
	
	<KeyFrameInterval>00:00:02</KeyFrameInterval>
	<StretchMode>AutoSize</StretchMode>
	<Condition>InsertBlackIfNoVideo</Condition>

##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##См. также 

[Обзор кодирования с помощью служб мультимедиа](media-services-encode-asset.md)

<!---HONumber=AcomDC_0831_2016-->