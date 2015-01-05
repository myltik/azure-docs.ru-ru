<properties urlDisplayName="Smooth Streaming Plugin" pageTitle="Подключаемый модуль Smooth Streaming для платформы Open Source Media Framework" metaKeywords="" description="Learn how to use the Azure Media Services Smooth Streaming plugin for the Adobe Open Source Media Framework." metaCanonical="" services="media-services" documentationCenter="" title="How to Use the Microsoft Smooth Streaming Plugin for the Adobe Open Source Media Framework" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />



# Использование подключаемого модуля Smooth Streaming Майкрософт для платформы Adobe Open Source Media Framework #

##Обзор ##
Подключаемый модуль Smooth Streaming для платформы Open Source Media Framework 2.0 (SS для OSMF) расширяет возможности OSMF по умолчанию и добавляет возможности воспроизведения контента Smooth Streaming для новых и существующих проигрывателей OSMF. Подключаемый модуль также добавляет возможность воспроизведения Smooth Streaming в SMP.

SS для OSMF включает в себя две версии подключаемого модуля:

- статический подключаемый модуль Smooth Streaming для OSMF (.swc);

- динамический подключаемый модуль Smooth Streaming для OSMF (.swf).

В настоящем документе предполагается, что читатель имеет общие знания о работе с OSMF и подключаемыми модулями OSMF. Дополнительную информацию об OSMF см. в документации на [официальном сайте OSMF](http://osmf.org/).

###Подключаемый модуль Smooth Streaming для OSMF 2.0

Подключаемый модуль поддерживает загрузку и воспроизведение контента Smooth Streaming по запросу со следующими возможностями:

- воспроизведение Smooth Streaming по запросу (воспроизведение, пауза, поиск, остановка);
- воспроизведение Smooth Streaming в реальном времени (воспроизведение);
- функции DVR в реальном времени (пауза, поиск, воспроизведение DVR, Go-to-Live);
- поддержка видеокодеков H.264;
- поддержка аудиокодеков AAC;
- поддержка нескольких языков аудио с помощью встроенных API-интерфейсов OSMF;
- выбор качества воспроизведения с помощью встроенных API-интерфейсов OSMF;
- боковые субтитры с использованием подключаемого модуля субтитров OSMF;
- Проигрыватель Adobe&reg; Flash&reg; 10.2 или более поздней версии.
- Эта версия поддерживает только OSMF 2.0.

Ниже приведены неподдерживаемые функции:

- кодеки WMA и VC-1;
- защита контента (PlayReady);
- текст и разреженные дорожки;
- Trickplay (замедленное воспроизведение, перемотки вперед и назад).

Ниже приведен список известных проблем.

- При воспроизведении контента Smooth Streaming со звуковыми дорожками с частотой 48 кГц возникают проблемы. В среде выполнения Flash возникают проблемы с воспроизведением аудио с частотой 48 кГц. По этой причине контент Smooth Streaming с частотой 48 кГц может не работать должным образом. Дополнительные сведения см. в статьях [Using Flash Player](http://forums.adobe.com/message/4483498#4483498) (Использование проигрывателя Flash Player) и [Adobe Flash Player 11.3 - Bug 3210964](https://bugbase.adobe.com/index.cfm?event=bug&id=3210964) (Adobe Flash Player 11.3: ошибка 3210964).
- Одновременное воспроизведение контента Smooth Streaming на одной странице может вызвать проблемы. Это известная проблема с OSMF.
- Воспроизведение видео Stage может вызвать проблемы, на некоторых компьютерах видео может не отображаться. Для решения проблемы можно отключить аппаратное ускорение или видео Stage.

## Загрузка подключаемого модуля
Подключаемые модули OSMF можно загружать статически (во время компиляции) или динамически (во время выполнения). Подключаемый модуль Smooth Streaming для OSMF содержит динамическую и статическую версию.

- Статическая загрузка: для статической загрузки требуется файл статической библиотеки (SWC). Статические подключаемые модули добавляются как ссылки на проекты и объединяются с окончательным выходным файлом во время компиляции.

- Динамическая загрузка: для динамической загрузки требуется предварительно скомпилированный файл (SWF). Динамические подключаемые модули загружаются во время выполнения и не включаются в выходные данные проекта. (Выходные данные компиляции) Динамические подключаемые модули можно загрузить с использованием протоколов HTTP и FILE.

Дополнительную информацию о статической и динамической загрузке см. на официальной [странице подключаемого модуля OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

###Динамическая загрузка SS для OSMF
В следующем фрагменте кода показано, как загрузить подключаемый модуль SS для OSMF статически и воспроизвести простое видео с помощью класса MediaFactory OSMF. Перед добавлением кода SS для OSMF убедитесь, что ссылка на проект включает статический подключаемый модуль "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc".

<pre><code>
package 
{
	
	import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;
	
	import flash.display.*;
	import org.osmf.media.*;
	import org.osmf.containers.MediaContainer;
	import org.osmf.events.MediaErrorEvent;
	import org.osmf.events.MediaFactoryEvent;
	import org.osmf.events.MediaPlayerStateChangeEvent;
	import org.osmf.layout.*;
	
	
	
	[SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
	public class TestPlayer extends Sprite
	{        
		public var _container:MediaContainer;
		public var _mediaFactory:DefaultMediaFactory;
		private var _mediaPlayerSprite:MediaPlayerSprite;
		

		public function TestPlayer( )
		{
			stage.quality = StageQuality.HIGH;

			initMediaPlayer();

		}
	
		private function initMediaPlayer():void
		{
		
			// Create the container (sprite) for managing display and layout
			_mediaPlayerSprite = new MediaPlayerSprite();    
			_mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
			_mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
			_mediaPlayerSprite.scaleMode = ScaleMode.NONE;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			//Adds the container to the stage
			addChild(_mediaPlayerSprite);
			
			// Create a mediafactory instance
			_mediaFactory = new DefaultMediaFactory();
			
			// Add the listeners for PLUGIN_LOADING
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
			
			// Load the plugin class 
			loadAdaptiveStreamingPlugin( );  
			
		}
		
		private function loadAdaptiveStreamingPlugin( ):void
		{
			var pluginResource:MediaResourceBase;
			
			pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
			_mediaFactory.loadPlugin( pluginResource ); 
		}
		
		private function onPluginLoaded( event:MediaFactoryEvent ):void
		{
			// The plugin is loaded successfully.
			// Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
		loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
		
		}
		
		private function onPluginLoadFailed( event:MediaFactoryEvent ):void
		{
			// The plugin is failed to load ...
		}
		
		
		private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
		{
			var state:String;
			
			state =  event.state;
			
			switch (state)
			{
				case MediaPlayerState.LOADING: 
					
					// A new source is started to load.
					
					break;
				
				case  MediaPlayerState.READY :   
					// Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
					
					break;
				
				case MediaPlayerState.BUFFERING :
					
					break;
				
				case  MediaPlayerState.PAUSED :
					break;      
				// other states ...          
			}
		}
		
		private function onPlayerFailed(event:MediaErrorEvent) : void
		{
			// Media Player is failed .           
		}
		
		private function loadMediaSource(sourceURL : String):void 
		{
			// Take an URL of SmoothStreamingSource's manifest and add it to the page.
			
			var resource:URLResource= new URLResource( sourceURL );
			
			var element:MediaElement = _mediaFactory.createMediaElement( resource );
			_mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			
			// Add the media element
			_mediaPlayerSprite.media = element;
		}     
		
	}
}
</code></pre>


###Динамическая загрузка SS для OSMF

В следующем фрагменте кода показано, как загрузить подключаемый модуль SS для OSMF динамически и воспроизвести простое видео с помощью класса MediaFactory OSMF. Перед включением кода SS для OSMF скопируйте динамический модуль "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" в папку проекта, если загрузка будет выполняться с помощью протокола FILE, или скопируйте его на веб-сервер для загрузки с помощью протокола HTTP. Нет необходимости включать "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" в ссылки проекта.

<pre><code>
package 
{
	
	import flash.display.*;
	import org.osmf.media.*;
	import org.osmf.containers.MediaContainer;
	import org.osmf.events.MediaErrorEvent;
	import org.osmf.events.MediaFactoryEvent;
	import org.osmf.events.MediaPlayerStateChangeEvent;
	import org.osmf.layout.*;
	import flash.events.Event;
	import flash.system.Capabilities;

	
	//Sets the size of the SWF
	
	[SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
	public class TestPlayer extends Sprite
	{        
		public var _container:MediaContainer;
		public var _mediaFactory:DefaultMediaFactory;
		private var _mediaPlayerSprite:MediaPlayerSprite;
		
		
		public function TestPlayer( )
		{
			stage.quality = StageQuality.HIGH;
			initMediaPlayer();
		}
		
		private function initMediaPlayer():void
		{
			
			// Create the container (sprite) for managing display and layout
			_mediaPlayerSprite = new MediaPlayerSprite();    
			_mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
			_mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

			//Adds the container to the stage
			addChild(_mediaPlayerSprite);
			
			// Create a mediafactory instance
			_mediaFactory = new DefaultMediaFactory();
			
			// Add the listeners for PLUGIN_LOADING
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
			
			// Load the plugin class 
			loadAdaptiveStreamingPlugin( );  
			
		}
		
		private function loadAdaptiveStreamingPlugin( ):void
		{
			var pluginResource:MediaResourceBase;
			var adaptiveStreamingPluginUrl:String;

			// Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

			adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
			pluginResource = new URLResource(adaptiveStreamingPluginUrl);
			_mediaFactory.loadPlugin( pluginResource ); 

		}
		
		private function onPluginLoaded( event:MediaFactoryEvent ):void
		{
			// The plugin is loaded successfully.

			// Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

	loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
		}
		
		private function onPluginLoadFailed( event:MediaFactoryEvent ):void
		{
			// The plugin is failed to load ...
		}
		
		
		private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
		{
			var state:String;
			
			state =  event.state;
			
			switch (state)
			{
				case MediaPlayerState.LOADING: 
					
					// A new source is started to load.
					
					break;
				
				case  MediaPlayerState.READY :   
					// Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
					
					break;
				
				case MediaPlayerState.BUFFERING :
					
					break;
				
				case  MediaPlayerState.PAUSED :
					break;      
				// other states ...          
			}
		}
		
		private function onPlayerFailed(event:MediaErrorEvent) : void
		{
			// Media Player is failed .           
		}
		
		private function loadMediaSource(sourceURL : String):void 
		{
			// Take an URL of SmoothStreamingSource's manifest and add it to the page.
			
			var resource:URLResource= new URLResource( sourceURL );
			
			var element:MediaElement = _mediaFactory.createMediaElement( resource );
			_mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			// Add the media element
			_mediaPlayerSprite.media = element;
		}     
		
	}
}
</code></pre>

##Воспроизведение Strobe Media с помощью динамического подключаемого модуля SS ODMF
Динамический подключаемый модуль Smooth Streaming для OSMF совместим с [проигрывателем Strobe Media Playback (SMP)](http://osmf.org/strobe_mediaplayback.html). Подключаемый модуль SS для OSMF можно использовать для поддержки воспроизведения контента в SMP. Для этого скопируйте "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" на веб-сервер, чтобы выполнять загрузку по протоколу HTTP, и выполните следующие действия:

1.	Откройте [страницу настройки Strobe Media Playback](http://osmf.org/dev/2.0gm/setup.html). 
2.	Задайте src в качестве значения для источника Smooth Streaming, (например, http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3.	Внесите требуемые изменения и нажмите кнопку "Предварительный просмотр и обновление".
 
	**Примечание.** На веб-сервере должен быть действительный файл crossdomain.xml. 
4.	Скопируйте и вставьте код на простую HTML-страницу, используя предпочитаемый текстовый редактор, как в следующем примере:



		<html>
		<body>
		<object width="920" height="640"> 
		<param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
		<param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
		<param name="allowFullScreen" value="true"></param>
		<param name="allowscriptaccess" value="always"></param>
		<param name="wmode" value="direct"></param>
		<embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
    		type="application/x-shockwave-flash" 
    		allowscriptaccess="always" 
    		allowfullscreen="true" 
    		wmode="direct" 
    		width="920" 
    		height="640" 
    		flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
		</embed>
		</object>
		</body>
		</html>



5. Добавьте подключаемый модуль Smooth Streaming OSMF в код внедрения и сохраните файл.

		<html>
		<object width="920" height="640"> 
		<param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
		<param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
		<param name="allowFullScreen" value="true"></param>
		<param name="allowscriptaccess" value="always"></param>
		<param name="wmode" value="direct"></param>
		<embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
    		type="application/x-shockwave-flash" 
    		allowscriptaccess="always" 
    		allowfullscreen="true" 
    		wmode="direct" 
    		width="920" 
    		height="640" 
    		flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
		</embed>
		</object>
		</html>


6. 	Сохраните HTML-страницу и опубликуйте ее на веб-сервере. Перейдите к опубликованной веб-странице в предпочитаемом браузере с поддержкой проигрывателя Flash&reg; (Internet Explorer, Chrome, Firefox и т. д.).
7. 	Наслаждайтесь просмотром контента Smooth Streaming в проигрывателе Adobe&reg; Flash&reg;.

Дополнительную информацию об общей процедуре разработки для OSMF см. на официальной [странице разработки OSMF](http://osmf.org/resources.html).

<!--HONumber=35.1-->
