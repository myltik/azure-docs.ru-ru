.<properties
	pageTitle=" Масштабирование обработки мультимедиа с помощью портала Azure | Microsoft Azure"
	description="В этом учебнике пошагово описано, как масштабировать обработку мультимедиа с помощью портала Azure."
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
	ms.topic="article"
	ms.date="08/29/2016"
	ms.author="juliako"/>

# Изменение типа зарезервированных единиц

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Портал](media-services-portal-scale-media-processing.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

## Обзор

Чтобы получить дополнительные сведения о масштабировании обработки мультимедиа, ознакомьтесь с этим [обзором](media-services-scale-media-processing-overview.md).

## Масштабирование обработки мультимедиа

Чтобы изменить тип зарезервированных единиц и число зарезервированных единиц, выполните следующие действия:

1. Войдите на [портал Azure](https://portal.azure.com/).

2. В окне **Параметры** выберите **Media reserved units** (Зарезервированные единицы мультимедиа).

	Чтобы изменить число зарезервированных единиц для выбранного типа зарезервированных единиц, используйте ползунок **Media Served Units** (Зарезервированные единицы мультимедиа).

	Чтобы изменить **ТИП ЗАРЕЗЕРВИРОВАННЫХ ЕДИНИЦ**, выберите S1, S2 или S3.

	![Страница "Процессоры"](./media/media-services-portal-scale-media-processing/media-services-scale-media-processing.png)

3. Нажмите кнопку СОХРАНИТЬ, чтобы сохранить изменения.

	Новые зарезервированные единицы выделяются сразу после нажатия кнопки "СОХРАНИТЬ".

##Дальнейшие действия

Просмотрите схемы обучения работе со службами мультимедиа.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0831_2016-->