<properties 
	pageTitle="Рабочий процесс видео по запросу служб мультимедиа" 
	description="В этом разделе описан типичный рабочий процесс для видео по запросу в службах мультимедиа." 
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
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#Рабочий процесс видео по запросу служб мультимедиа

##Обзор

В этом разделе описан типичный рабочий процесс для видео по запросу в службах мультимедиа Azure (AMS) На каждом шаге перечисляются ссылки на соответствующие разделы. Для задач, которые можно выполнить с использованием различных технологий, предусмотрены кнопки, позволяющие выбрать соответствующую технологию (например, .NET или REST).   

Обратите внимание, что службы мультимедиа можно интегрировать с существующими средствами и процессами. Например, можно кодировать контент на сайте, а затем загружать его в службы мультимедиа для преобразования в различные форматы и доставки с помощью сети CDN Azure или сторонней сети CDN. 

На следующей диаграмме показаны основные части платформы служб мультимедиа, участвующих в рабочем процессе для видео по запросу.
![VoD workflow][vod-overview]

Дополнительные сведения см. в разделе [Обзор служб мультимедиа](media-services-overview.md).

##Создание учетной записи служб мультимедиа

Воспользуйтесь **порталом управления Azure** для [создания учетной записи служб мультимедиа Azure](media-services-create-account.md). 

##Настройка конечных точек потоковой передачи

[работа выполняется]

##Настройка среды разработки  

Выберите **.NET** или **REST API** для среды разработки.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

##Программное подключение  

Выберите **.NET** или **REST API** для программного подключения к службам мультимедиа Azure.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

##Отправка мультимедиа 

Отправьте файлы с помощью **портала управления Azure**, **.NET** или **API REST**.

[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

##Обработка мультимедиа: кодирование, индексирование, отслеживание заданий

###Получение обработчика мультимедиа

Получите обработчик мультимедиа с помощью **.NET** или **API REST**.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../includes/media-services-selector-get-media-processor.md)]

###Создание заданий 

Задание - это сущность, которая содержит метаданные о наборе задач (например, кодирования или индексирования). Каждая задача выполняет атомарную операцию на входном ресурсе или ресурсах. Пример создания заданий кодирования см. в разделе:

[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

###Наблюдение за ходом выполнения задания

Мониторинг хода выполнения задания с помощью **портала управления Azure**, **.NET** или **API REST**.

[AZURE.INCLUDE [media-services-selector-job-progress](../includes/media-services-selector-job-progress.md)]

###Индексация

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

###Кодирование 

См. раздел [Кодирование с помощью служб мультимедиа Azure](media-services-encode-asset.md).

##Настройка политики авторизации ключей содержимого 

Настройка защиты содержимого и политики авторизации ключа с помощью  **.NET** или **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

##Настройка политики доставки ресурсов

Настройте политики доставки ресурсов с помощью **.NET** или **REST API**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

##Публикация ресурсов

Опубликуйте ресурсы (создав указатели) с помощью **портала управления Azure** или **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]

##Масштабирование учетной записи служб мультимедиа

Можно масштабировать **службы мультимедиа**, указав число **зарезервированных единиц потоковой передачи** и **зарезервированных единиц кодирования**, которые необходимо использовать при подготовке учетной записи. 

Можно также масштабировать учетную запись служб мультимедиа, добавляя к ней учетные записи хранения. Каждая учетная запись хранения ограничена 500 ТБ. Чтобы увеличить емкость хранилища за пределы ограничений по умолчанию, можно присоединить несколько учетных записей хранения для одной учетной записи служб мультимедиа.

[Этот](media-services-how-to-scale.md) раздел содержит ссылки на соответствующие разделы.


##Воспроизведение содержимого

Дополнительные сведения см. в разделе [Воспроизведение содержимого с помощью существующих проигрывателей](media-services-playback-content.md).

[vod-overview]: ./media/media-services-overview/media-services-video-on-demand.png
<!--HONumber=47-->
