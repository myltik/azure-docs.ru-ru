<properties 
   pageTitle="Автоматизация операций DNS и наборов записей с использованием пакета SDK .NET | Microsoft Azure" 
   description="Использование пакета SDK .NET для автоматизации всех операций DNS для Azure DNS." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/20/2015"
   ms.author="joaoma"/>
# Создание зон DNS и наборов записей с помощью пакета SDK для .NET
Вы можете автоматизировать операции для создания, удаления или обновления зон DNS, наборов записей и записей с помощью пакета SDK DNS с библиотекой управления DNS .NET. Полный проект Visual Studio доступен [здесь.](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

## Пакеты NuGet и объявление пространств имен
Чтобы использовать DNS-клиент, необходимо установить пакет NuGet «Библиотека управления DNS Azure» и добавить в проект пространства имен управления DNS. Перейдите в Visual Studio, откройте проект или создайте новый проект и перейдите в раздел «Инструменты» -> «Консоль диспетчера пакетов Nuget». Загрузите библиотеку управления DNS Azure.

	using Microsoft.Azure;
	using Microsoft.Azure.Management.Dns;
	using Microsoft.Azure.Management.Dns.Models;

## Инициализация DNS-клиента управления

DnsManagementClient содержит методы и свойства, необходимые для управления зонами DNS и наборами данных. Чтобы клиент имел возможность доступа к подписке, необходимо настроить правильные разрешения и создать токен AWT. Дополнительные сведения см. в разделе «Проверка подлинности запросов диспетчера ресурсов Azure».

	// get a token for the AAD application (see linked article for code)
	string jwt = GetAToken();

	// make the TokenCloudCredentials using subscription ID and token
	TokenCloudCredentials tcCreds = new TokenCloudCredentials(subID, jwt);

	// make the DNS management client
	DnsManagementClient dnsClient = new DnsManagementClient(tcCreds);

## Создание или обновление зоны DNS

Для создания зоны DNS создается объект Zone и передается в dnsClient.Zones.CreateOrUpdate. Поскольку зоны DNS не связаны с конкретным регионом, для расположения устанавливается значение «Глобальное».<BR>

Создание зоны DNS

	// create a DNS zone
	Zone z = new Zone("global");
	z.Properties = new ZoneProperties();
	z.Tags.Add("dept", "shopping");
	z.Tags.Add("env", "production");
	ZoneCreateOrUpdateParameters zoneParams = new ZoneCreateOrUpdateParameters(z);
	ZoneCreateOrUpdateResponse responseCreateZone = 
	dnsClient.Zones.CreateOrUpdate("myresgroup", "myzone.com", zoneParams);


Azure DNS поддерживает оптимистичный параллелизм, который называется [теги eTag](../dns-getstarted-create-dnszone#Etags-and-tags). Etag — это свойство объекта Zone, а IfNoneMatch — это свойство в ZoneCreateOrUpdateParameters.

## Создание или обновление записей DNS
Записи DNS управляются как набор записей. Набор записей — это набор записей с одним и тем же именем и типом в пределах зоны. Для создания или обновления набора записей создается объект RecordSet и передается в dnsClient.RecordSets.CreateOrUpdate. Обратите внимание, что имя набора записей указывается относительно имени зоны, в отличие от полного имени DNS. Снова расположение имеет значение «Глобальное».
    
Сделайте несколько наборов записей.

	// make some records sets
	RecordSet rsWwwA = new RecordSet("global");
	rsWwwA.Properties = new RecordProperties(3600);
	rsWwwA.Properties.ARecords = new List<ARecord>();
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.4"));
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.5"));
	RecordCreateOrUpdateParameters recordParams = 
								new RecordCreateOrUpdateParameters(rsWwwA);
	RecordCreateOrUpdateResponse responseCreateA = 
								dnsClient.RecordSets.CreateOrUpdate("myresgroup", 
	"myzone.com", "www", RecordType.A, recordParams);
	
    
Azure DNS поддерживают оптимистичный параллелизм [тегов eTag](../dns-getstarted-create-dnszone#Etags-and-tags). Etag — это свойство объекта RecordSet, а IfNoneMatch является свойством в RecordSetCreateOrUpdateParameters.

## Получение зон и наборов данных
Коллекции зон и наборов данных позволяют получать зоны и наборы записей соответственно. Наборы записей идентифицируются по типу, имени и зоне (и группе ресурсов), в которой они существуют. Зоны идентифицируются имени и группе ресурсов, в которой они существуют.

	ZoneGetResponse getZoneResponse = 
	dnsClient.Zones.Get("myresgroup", "myzone.com");
	RecordGetResponse getRSResp = 
	dnsClient.RecordSets.Get("myresgroup", 
	"myzone.com", "www", RecordType.A);

##Получение списка зон и наборов записей

Чтобы получить список зон, используйте метод List в коллекции Zones. Чтобы получить список наборов записей, используйте метод List или ListAll в коллекции RecordSets. Метод List отличается от метода ListAll в том, что он возвращает только наборы записей указанного типа.

В следующем примере показывается, как получить список зон DNS и наборов записей.


	ZoneListResponse zoneListResponse = dnsClient.Zones.List("myresgroup", new ZoneListParameters());
	foreach (Zone zone in zoneListResponse.Zones)
	{
    	RecordListResponse recordSets = 
                 			dnsClient.RecordSets.ListAll("myresgroup", "myzone.com", new RecordSetListParameters());

    // do something like write out each record set
	}
## См. также 
[Обзор диспетчера трафика](../traffic-manager-overview)

[Обзор Azure DNS](../dns-overview)

[Образец проекта SDK Visual Studio](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

<!---HONumber=62-->