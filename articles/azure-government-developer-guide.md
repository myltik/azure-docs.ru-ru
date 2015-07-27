<properties 
	pageTitle="Руководство для разработчиков Azure Government" 
	description="Данное руководство включает сравнительный анализ характеристик и рекомендации по разработке приложений для разработчиков Azure." 
	services="" 
	documentationCenter="" 
	authors="Joharve2" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="azure-government" 
	ms.date="01/21/2014" 
	ms.author="jharve"/>


#  Руководство для разработчиков Microsoft Azure Government 

<p> Microsoft Azure Government  — это физически изолированный от сети экземпляр Microsoft Azure. В данном руководстве для разработчиков представлены подробные сведения о различиях, с которыми столкнутся разработчики приложений и администраторы при работе с этими отдельными регионами Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## Содержание раздела


+ [Обзор](#Overview)
+ [Рекомендации для разработчиков](#Guidance)
+ [Функции Microsoft Azure Government, доступные в настоящее время](#Features)
+ [Сопоставление конечных точек](#Endpoint)
+ [Дальнейшие действия](#next)


## <a name="Overview"></a>Обзор

Microsoft Azure Government – это отдельный экземпляр службы Microsoft Azure, соответствующий требованиям безопасности и законодательным нормам федеральных органов США, местных властей и властей штатов, а также поставщиков решений для указанных организаций. Microsoft Azure Government предлагает физическую и сетевую изоляцию от развертываний, не относящихся к государственным органам, и обеспечивает защищенную работу их сотрудников.

Корпорация Майкрософт предлагает ряд средств для создания и развертывания облачных приложений в глобальных службах Microsoft Azure («глобальная служба») и Microsoft Azure Government.

При создании и развертывании приложений для Azure Government разработчики должны понимать основные различия между этой службой и глобальным развертыванием. В частности, различия по подготовке и настройке среды программирования, настройке конечных точек, разработке приложений и развертыванию их в качестве служб в Azure Government.

Данный документ описывает эти различия и дополняет информацию, приведенную на сайте [Azure Government](http://www.azure.com/gov "Azure Government") и в [технической библиотеке Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") в MSDN. Официальные сведения доступны также и в других местах, например в [центре управления безопасностью Microsoft Azure](http://azure.microsoft.com/support/trust-center/ "Центр управления безопасностью Microsoft Azure"), [центре документации Azure](http://azure.microsoft.com/documentation/) и [блогах Azure](http://azure.microsoft.com/blog/ "Блоги Azure").

Эти сведения предназначены для партнеров и разработчиков, развертывающих приложения в Microsoft Azure Government.



## <a name="Guidance"></a>Рекомендации для разработчиков
Поскольку большая часть доступных в настоящее время технических материалов предполагает, что приложения разрабатываются для глобальной службы, а не для Microsoft Azure Government, важно познакомить разработчиков с основными отличиями приложений, размещаемых в Azure Government.

- Во-первых, есть различия в работе служб и функций, то есть определенные функции в некоторых регионах глобальной службы могут оказаться недоступными в Azure Government.

- Во-вторых, для функций, доступных в Azure Government, есть изменения в процессе конфигурации по отношению к глобальной службе. Таким образом, необходимо изучить примеры кода, конфигурации и действия, необходимые для построения и выполнения приложений в среде облачных служб Azure Government.


## <a name="Features"></a> Функции Microsoft Azure Government, доступные в настоящее время
Azure Goverment в настоящее время имеет следующие службы, доступные в регионах штата US GOV IOWA и US GOV VIRGINIA:

- Виртуальные машины
- Облачные службы
- Хранилище
- Active Directory
- Планировщик
- Виртуальная сеть
- База данных SQL

Доступны и другие службы, и их список будет расширяться. Актуальный список доступных служб см. на [странице регионов](http://azure.microsoft.com/regions/#services). Там будут отмечены все доступные регионы и службы в них.

В данный момент Azure Government поддерживают центры обработки данных US GOV Iowa и US GOV Virginia. На указанной выше странице регионов можно посмотреть доступные в данный момент центры обработки данных и службы.

## <a name="Endpoint"></a>Сопоставление конечных точек

Используйте следующую таблицу для сопоставления общедоступных конечных точек Microsoft Azure и баз данных SQL Azure с конечными точками Azure Government.

<table>
<tr style='font-weight:bold'><td>
Тип службы</td><td>	Azure Public</td><td>	Azure Government
</td></tr><tr><td>
Azure Government Home</td><td>	windowsazure.com	</td><td>microsoftazure.us
</td></tr><tr><td>
Портал управления</td><td>	manage.windowsazure.com</td><td>	manage.windowsazure.us
</td></tr><tr><td>
Общие сведения</td><td>	*.windows.net	</td><td>*.usgovcloudapi.net
</td></tr><tr><td>
Core	</td><td>*.core.windows.net	</td><td>*.core.usgovcloudapi.net
</td></tr><tr><td>
Среда выполнения приложений	</td><td>*.cloudapp.net	</td><td>*.usgovcloudapp.net
</td></tr><tr><td>
Хранилище BLOB-объектов</td><td>	*.blob.core.windows.net</td><td>	*.blob.core.usgovcloudapi.net
</td></tr><tr><td>
Хранилище очередей	</td><td>*.queue.core.windows.net</td><td>	*.queue.core.usgovcloudapi.net
</td></tr><tr><td>
Хранилище таблиц</td><td>	*.table.core.windows.net	</td><td>*.table.core.usgovcloudapi.net
</td></tr><tr><td>
Service Management</td><td>	management.core.windows.net</td><td>	management.core.usgovcloudapi.net

</td></tr>
<tr><td>База данных SQL</td><td>	*.database.windows.net	</td><td>*.database.usgovcloudapi.net</td></tr>
</table>

## <a name="next"></a>Дальнейшие действия
Дополнительные сведения о Azure Government и о том, как ваша организация может получить доступ к этой службе, см. на странице <A href="http://azure.com/gov">http://www.azure.com/gov</a>.

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=July15_HO3-->