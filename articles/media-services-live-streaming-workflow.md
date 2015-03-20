<properties 
	pageTitle="Рабочий процесс для потоковой трансляции в службах мультимедиа" 
	description="В этом разделе описан типичный рабочий процесс для потоковой трансляции в службах мультимедиа." 
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
	ms.date="02/18/2015" 
	ms.author="juliako"/>


#Рабочий процесс для потоковой трансляции в службах мультимедиа

##Обзор

В этом разделе описан типичный рабочий процесс для потоковой трансляции в службах мультимедиа Azure (AMS). На каждом шаге перечисляются ссылки на соответствующие разделы. Для задач, которые можно выполнить с использованием различных технологий, предусмотрены кнопки, позволяющие выбрать соответствующую технологию (например, .NET или REST).   

Обратите внимание, что службы мультимедиа можно интегрировать с существующими средствами и процессами. Например, можно кодировать контент на сайте, а затем загружать его в службы мультимедиа для преобразования в различные форматы и доставки с помощью сети CDN Azure или сторонней сети CDN. 

На следующей диаграмме показаны основные части платформы служб мультимедиа, участвующих в рабочем процессе для видео по запросу.
![Live workflow][live-overview]


Дополнительные сведения см. в разделе [Обзор служб мультимедиа](../media-services-overview).

##Создание учетной записи служб мультимедиа

Воспользуйтесь **порталом управления Azure** для [создания учетной записи служб мультимедиа Azure](../media-services-create-account/).

##Настройка конечных точек потоковой передачи

[работа выполняется]

##Настройка среды разработки  

Выберите **.NET** или **REST API** для среды разработки.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

##Программное подключение  

Выберите **.NET** или **REST API** для программного подключения к службам мультимедиа Azure.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

##Работа с динамическими перекодировщиками

Дополнительную информацию см. в статье [Использование сторонних динамических кодировщиков со службами мультимедиа Azure](https://msdn.microsoft.com/library/azure/dn783464.aspx).

##Управление каналами, программами и ресурсами

Дополнительную информацию см. в разделе [Потоковая трансляция](https://msdn.microsoft.com/library/azure/dn783466.aspx).

##Настройка защиты содержимого и политики авторизации ключа содержимого 

Настройте политику авторизации ключей с помощью **.NET** или **REST API**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

##Настройка политики доставки ресурсов

Настройте политики доставки ресурсов с помощью **.NET** или **REST API**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

##Публикация ресурсов

Опубликуйте ресурсы (создав указатели) с помощью **портала управления Azure** или **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


##Масштабирование учетной записи служб мультимедиа

**Службы мультимедиа** можно масштабировать, указав число **зарезервированных единиц потоковой передачи**, которые необходимо использовать при подготовке учетной записи. 

Информацию о масштабировании единиц потоковой передачи см. в статье [Как масштабировать единицы потоковой передачи](../media-services-manage-origins#scale_streaming_endpoints).



[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png
<!--HONumber=47-->
