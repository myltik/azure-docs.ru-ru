<properties 
	pageTitle="Как добавить единицы кодирования" 
	description="Информация о добавлении единиц кодирования с помощью .NET"  
	services="media-services" 
	documentationCenter="" 
	authors="juliako,milangada,gtrifonov" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2016"
	ms.author="juliako"/>


#Масштабирование кодирования с помощью пакета SDK для .NET


> [AZURE.SELECTOR]
- [Portal](media-services-portal-encoding-units.md)
- [.NET](media-services-dotnet-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##Обзор

Учетная запись служб мультимедиа связана с типом зарезервированных единиц, который определяет скорость обработки заданий кодирования. Вы можете выбрать один из следующих типов зарезервированных единиц: S1, S2 или S3. Например, задание по кодированию выполняется быстрее при выборе типа зарезервированной единицы Standard по сравнению с типом Basic. Дополнительную информацию см. в блоге "Типы зарезервированных единиц кодирования" [Милана Гады (Milan Gada)](https://azure.microsoft.com/blog/author/milanga/).

Помимо указания типа зарезервированных единиц, можно также указать параметр, позволяющий использовать зарезервированные единицы кодирования при подготовке учетной записи. Количеством подготовленных зарезервированных элементов кодирования определяется количество задач мультимедиа, которые могут одновременно обрабатываться в данной учетной записи. Например, если в учетной записи имеется 5 зарезервированных единиц, то будет запущено 5 одновременно выполняемых задач мультимедиа, если имеются задачи, которые требуется обработать. Остальные задачи будут ожидать в очереди, они будут последовательно отбираться для обработки сразу же после завершения выполнения текущей задачи. Если учетная запись не имеет подготовленных зарезервированных единиц, задачи будут отбираться последовательно. В этом случае время ожидания между завершением одной задачи и началом выполнения следующей будет зависеть от доступности ресурсов в системе.

Чтобы изменить тип зарезервированных единиц и число зарезервированных единиц кодирования с помощью пакета SDK для .NET, сделайте следующее:

	IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
	encodingS1ReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);
	
	encodingS1ReservedUnit.CurrentReservedUnits = 2;
	encodingS1ReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

##Открытие запроса в службу поддержки

По умолчанию каждая учетная запись служб мультимедиа может масштабироваться до 25 зарезервированных единиц кодирования и 5 зарезервированных единиц потоковой передачи по требованию. Вы можете запросить более высокий предел, открыв запрос в службу поддержки.

###Открытие запроса в службу поддержки

Чтобы открыть запрос в службу поддержки, выполните следующее:

1. Щелкните [Получить поддержку](https://manage.windowsazure.com/?getsupport=true). Если вы не выполнили вход, будет предложено ввести учетные данные.

1. Выберите свою подписку.
 
1. В группе типа поддержки выберите "Техническая".
 
1. Щелкните "Создать запрос в службу поддержки".
 
1. Выберите "Службы мультимедиа Azure" в списке продуктов, представленных на следующей странице.
 
1. Выберите соответствующее значение в поле "Тип проблемы".
 
1. Нажмите кнопку "Продолжить".
 
1. Следуйте указаниям на следующей странице, а затем введите сведения о проблеме.
 
1. Нажмите кнопку "Отправить" для создания запроса в службу поддержки.
 


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0204_2016-->