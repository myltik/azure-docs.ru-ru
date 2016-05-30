<properties 
   pageTitle="Создание зон и наборов записей DNS для Azure DNS с помощью пакета SDK для .NET | Microsoft Azure" 
   description="Описывается, как создать зоны и наборы записей DNS для Azure DNS с помощью пакета SDK для .NET." 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/10/2016"
   ms.author="cherylmc"/>


# Создание зон и наборов записей DNS с помощью пакета SDK для .NET

Вы можете автоматизировать операции создания, удаления или обновления зон, наборов записей и записей DNS, используя пакет SDK для DNS и библиотеку управления DNS для .NET. Полный проект Visual Studio доступен [здесь.](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

## Пакеты NuGet и объявления пространств имен

Чтобы использовать DNS-клиент, потребуется установить пакет NuGet **библиотеки управления DNS для Azure** и добавить в проект пространства имен управления DNS.
 
1. В **Visual Studio**, откройте существующий или новый проект. 

2. Выберите **Инструменты** **>** **Диспетчер пакетов NuGet** **>** **Консоль диспетчера пакетов**.

3. Скачайте библиотеку управления DNS для Azure.

		using Microsoft.Azure;
		using Microsoft.Azure.Management.Dns;
		using Microsoft.Azure.Management.Dns.Models;

## Инициализация DNS-клиента управления

*DnsManagementClient* содержит методы и свойства, необходимые для управления зонами и наборами записей DNS. Чтобы клиент имел доступ к вашей подписке, необходимо настроить правильные разрешения и создать маркер AWT. Дополнительные сведения см. в разделе [Запросы проверки подлинности диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn790557.aspx).

	// get a token for the AAD application (see the article link above for code)
	string jwt = GetAToken();

	// make the TokenCloudCredentials using subscription ID and token
	TokenCloudCredentials tcCreds = new TokenCloudCredentials(subID, jwt);

	// make the DNS management client
	DnsManagementClient dnsClient = new DnsManagementClient(tcCreds);

## Создание или обновление зоны DNS

Для создания зоны DNS создается объект Zone и передается в *dnsClient.Zones.CreateOrUpdate*. Так как зоны DNS не связаны с конкретным регионом, для расположения устанавливается значение "global".<BR> Служба Azure DNS поддерживает оптимистичный параллелизм с помощью [тегов Etag](dns-getstarted-create-dnszone.md). Etag является свойством зоны. IfNoneMatch является свойством в ZoneCreateOrUpdateParameters.

	// To create a DNS zone
	Zone z = new Zone("global");
	z.Properties = new ZoneProperties();
	z.Tags.Add("dept", "shopping");
	z.Tags.Add("env", "production");
	ZoneCreateOrUpdateParameters zoneParams = new ZoneCreateOrUpdateParameters(z);
	ZoneCreateOrUpdateResponse responseCreateZone = 
	dnsClient.Zones.CreateOrUpdate("myresgroup", "myzone.com", zoneParams);



## Создание или обновление записей и наборов записей DNS

Записи DNS управляются как набор записей. Набор записей — это набор записей с одним и тем же именем и типом в пределах зоны. Для создания или обновления набора записей создается объект RecordSet и передается в *dnsClient.RecordSets.CreateOrUpdate*. Обратите внимание, что имя набора записей указывается относительно имени зоны, в отличие от полного имени DNS. Для расположения задается значение "global".<BR> Azure DNS поддерживают оптимистичный параллелизм [тегов eTag](dns-getstarted-create-dnszone.md). Etag является свойством RecordSet. IfNoneMatch является свойством в RecordSetCreateOrUpdateParameters.
    


	// To create record sets
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
	
    
## Получение зон и наборов записей

Коллекции *Zones* и *RecordSets* позволяют получать зоны и наборы записей, соответственно. Коллекции RecordSets идентифицируются по типу, имени и зоне (и группе ресурсов), в которой они существуют. Зоны идентифицируются имени и группе ресурсов, в которой они существуют.

	ZoneGetResponse getZoneResponse = 
	dnsClient.Zones.Get("myresgroup", "myzone.com");
	RecordGetResponse getRSResp = 
	dnsClient.RecordSets.Get("myresgroup", 
	"myzone.com", "www", RecordType.A);

## Вывод списка зон и наборов записей

Чтобы получить список зон, используйте метод *List* в коллекции Zones. Чтобы получить список наборов записей, используйте метод *List* или *ListAll* в коллекции RecordSets. Метод *List* отличается от метода *ListAll* тем, что он возвращает только наборы записей указанного типа.

В следующем примере показывается, как получить список зон и наборов записей DNS.


	ZoneListResponse zoneListResponse = dnsClient.Zones.List("myresgroup", new ZoneListParameters());
	foreach (Zone zone in zoneListResponse.Zones)
	{
    	RecordListResponse recordSets = 
                 			dnsClient.RecordSets.ListAll("myresgroup", "myzone.com", new RecordSetListParameters());

    // do something like write out each record set
	}


## Дальнейшие действия

[Образец проекта SDK Visual Studio](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

<!---HONumber=AcomDC_0518_2016-->