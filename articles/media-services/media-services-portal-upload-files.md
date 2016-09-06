.<properties
	pageTitle=" Отправка файлов в учетную запись служб мультимедиа с помощью портала Azure | Microsoft Azure"
	description="В этом руководстве описаны этапы отправки файлов в учетную запись служб мультимедиа Azure с помощью портала Azure."
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/29/2016"
	ms.author="juliako"/>


# Отправка файлов в учетную запись служб мультимедиа с помощью портала Azure 

> [AZURE.SELECTOR]
- [Портал](media-services-portal-upload-files.md)
- [.NET](media-services-dotnet-upload-files.md)
- [REST](media-services-rest-upload-files.md)

> [AZURE.NOTE] Для работы с этим учебником требуется учетная запись Azure. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).

В службах мультимедиа цифровые файлы отправляются в актив. Ресурс может содержать видео, аудио, изображения, коллекции эскизов, текстовые каналы и файлы скрытых субтитров (и метаданные этих файлов). После отправки этих файлов содержимое сохраняется в безопасном расположении в облаке для дальнейшей обработки и потоковой передачи.
 
1. В окне **Параметры** щелкните **Ресурсы-контейнеры**.

	![Отправка файлов](./media/media-services-portal-vod-get-started/media-services-upload.png)

3. Нажмите кнопку **Отправить**.

	Появится окно **загрузки видеоресурса**.

	>[AZURE.NOTE] Размер файлов неограничен.
	
4. Найдите нужное видео на компьютере, выберите его и нажмите кнопку "ОК".

	Начнется передача. За ходом загрузки можно наблюдать под именем файла.

По завершении загрузки в окне **Ресурсы** появится новый ресурс.


## Дальнейшие действия

Теперь можно закодировать отправленные ресурсы. Дополнительную информацию см. в статье, посвященной [кодированию ресурсов](media-services-portal-encode.md).

## Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0831_2016-->