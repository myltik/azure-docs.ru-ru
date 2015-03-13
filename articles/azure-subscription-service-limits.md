<properties 
	pageTitle="Подписка Microsoft Azure, ограничения служб, квоты и ограничения" 
	description="В данном разделе приводится перечень наиболее распространенных ограничений, относящихся к подписке Azure и различным службам, наряду с максимальными значениями соответствующих показателей." 
	services="" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="jroth"/>

# Подписка Azure, границы, квоты и ограничения службы

Следующий документ описывает некоторые из наиболее известных ограничений Microsoft Azure. Учтите, что этот документ на текущий момент охватывает не все службы Azure. Со временам эти ограничения будут расширены.

- [Лимиты подписки](#subscription)
- [Лимиты облачной службы](#webworkerlimits)
- [Лимиты виртуальных машин](#vmlimits)
- [Лимиты веб-сайтов](#websiteslimits)
- [Сетевые лимиты](#networkinglimits)
- [Лимиты хранилища](#storagelimits)
- [Лимиты предварительной версии пакета](#batchlimits)
- [Лимиты предварительной версии DocumentDB](#documentdblimits)
- [Лимиты базы данных SQL](#sqldblimits)
- [Лимиты служб мультимедиа](#mediaserviceslimits)
- [Лимиты служебной шины](#servicebuslimits)
- [Лимиты Active Directory](#adlimits)

> [WACOM.NOTE] Если требуется сделать лимит выше значения **Лимит по умолчанию**, то можно [бесплатно отправить запрос в службу поддержки][azurelimitsblogpost]. Лимиты нельзя поднять выше **максимального** значения, приведенного в таблицах ниже. Если столбца **Максимальный лимит** нет, то для указанного ресурса лимиты не регулируются.

##<a name="subscription"></a>Ограничения подписки

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ресурс</th>
   <th align="left" valign="middle">Ограничение по умолчанию</th>
   <th align="left" valign="middle">Максимальное ограничение</th>
</tr>
<tr>
   <td valign="middle"><p>Ядер на <a href="http://msdn.microsoft.com/library/azure/hh531793.aspx">подписку</a><sup>1</sup></p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>10 000</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/gg456328.aspx">Соадминистраторов</a> на подписку</p></td>
   <td valign="middle"><p>200</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/storage-whatis-account/">учетные записи хранения;</a> на подписку</p></td>
   <td valign="middle"><p>100</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/cloud-services-what-is/">Облачных служб</a> на подписку</p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Виртуальных сетей</a> на подписку<sup>2</sup></p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/jj157100.aspx">Локальных сетей</a> на подписку</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Серверов баз данных SQL на подписку</p></td>
   <td valign="middle"><p>6</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
<tr>
   <td valign="middle"><p>Баз данных SQL на сервер</p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>500</p></td>
</tr>
<tr>
   <td valign="middle"><p>Серверы DNS на подписку</p></td>
   <td valign="middle"><p>9</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Зарезервированные IP на подписку</p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Сертификаты размещенной службы на подписку</p></td>
   <td valign="middle"><p>400</p></td>
   <td valign="middle"><p>400</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156085.aspx">Территориальных групп</a> на подписку</p></td>
   <td valign="middle"><p>256</p></td>
   <td valign="middle"><p>256</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups/">Групп ресурсов</a> на подписку</p></td>
   <td valign="middle"><p>300</p></td>
   <td valign="middle"><p>300</p></td>
</tr>

<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/services/batch/">Учетных записей предварительной версии пакета</a> в регионе на подписку</p></td>
   <td valign="middle"><p>1</p></td>
   <td valign="middle"><p>50</p></td>   
</tr>
</table>

<sup>1</sup>Каждый экземпляр размера Extra Small считается одним ядром ЦП при подсчете лимита по ядрам, хотя используется лишь часть ядра.

<sup>2</sup>Каждая виртуальная сеть поддерживает один шлюз виртуальной сети.

##<a name="webworkerlimits"></a>Ограничения облачной службы

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ресурс</th>
   <th align="left" valign="middle">Ограничение по умолчанию</th>
   <th align="left" valign="middle">Максимальное ограничение</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/cloud-services-what-is/">Рабочих и веб-ролей в развертывании<sup>1</sup></a></p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint">Входных конечных точек экземпляра</a> на развертывание</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint">Конечных точек ввода</a> на развертывание</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint">Внутренних конечных точек</a> на развертывание</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
</table>

<sup>1</sup>Каждая облачная служба с рабочими и веб-ролями может иметь два развертывания: одно для производственной среды и одно для промежуточной. Необходимо также иметь в виду, что данное ограничение относится к количеству отдельных ролей (настройка), а не к количеству экземпляров для роли (масштабирование). 

##<a name="vmlimits"></a>Ограничения виртуальной машины

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ресурс</th>
   <th align="left" valign="middle">Ограничение по умолчанию</th>
   <th align="left" valign="middle">Максимальное ограничение</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/services/virtual-machines/">Виртуальные машины</a> на облачную службу<sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Конечных точек ввода на облачную службу<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>При создании виртуальной машины автоматически создается облачная служба. Можно далее добавить несколько виртуальных машин в ту же самую облачную службу.

<sup>2</sup>Конечные точки ввода используются для подключения к виртуальным машинам за пределами облачной службы, где размещается приложение. Виртуальным машинам в одной облачной службе автоматически разрешено подсоединение по всем портам UDP и TCP для внутренних коммуникаций.

##<a name="websiteslimits"></a>Ограничения веб-сайтов

[WACOM.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

##<a name="networkinglimits"></a>Ограничения сети

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ресурс</th>
   <th align="left" valign="middle">Ограничение по умолчанию</th>
   <th align="left" valign="middle">Максимальное ограничение</th>
</tr>
<tr>
   <td valign="middle"><p>Всего машин<sup>1</sup> на <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Виртуальная сеть</a><sup>2</sup></p></td>
   <td valign="middle"><p>2048</p></td>
   <td valign="middle"><p>2048</p></td>
</tr>
<tr>
   <td valign="middle"><p>Параллельных соединений TCP для виртуальной машины или экземпляра роли</p></td>
   <td valign="middle"><p>500 тыс.</p></td>
   <td valign="middle"><p>500 тыс.</p></td>
</tr>
<tr>
   <td valign="middle"><p>Списков управления доступом (ACL) на конечную точку<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Сайтов локальной сети на виртуальную сеть</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>Общее число машин включает виртуальные машины, а также экземпляры рабочих и веб-ролей.

<sup>2</sup>Каждая виртуальная сеть поддерживает один [шлюз виртуальной сети][gateway].

<sup>3</sup>Список управления доступом поддерживается для конечных точек ввода виртуальных машин. Для ролей рабочего или веб-процесса поддерживаются для входных конечных точек и входных конечных точек экземпляра.

##<a name="storagelimits"></a>Ограничения хранилища

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ресурс<sup>1</sup></th>
   <th align="left" valign="middle">Ограничение по умолчанию</th>
</tr>
<tr>
   <td valign="middle"><p>ТБ на учетную запись хранения</p></td>
   <td valign="middle"><p>500 ТБ</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимальный размер одного контейнера BLOB-объектов, таблицы или очереди</p></td>
   <td valign="middle"><p>500 ТБ</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимальное количество контейнеров BLOB-объектов, BLOB-объектов, общих папок, таблиц, очередей, сущностей или сообщений для учетной записи хранения</p></td>
   <td valign="middle"><p>Ограничение касается только размера учетной записи хранения, который не может превышать 500 TБ</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимальный размер общей папки</p></td>
   <td valign="middle"><p>5 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимальное количество файлов в общей папке</p></td>
   <td valign="middle"><p>Ограничение касается только размера общей папки, который не может превышать 5 TБ</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимум 8 КБ на операции ввода вывода в секунду для постоянного диска (уровень Basic)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимум 8 КБ на операции ввода вывода в секунду для постоянного диска (уровень Standard)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Частота запросов (предполагается размер объекта в 1 КБ) для учетной записи хранения</p></td>
   <td valign="middle"><p>До 20 000 сущностей или сообщений в секунду</p></td>
</tr>
<tr>
   <td valign="middle"><p>Предельная пропускная способность для отдельного BLOB-объекта</p></td>
   <td valign="middle"><p>До 60 МБ в секунду или до 500 запросов в секунду</p></td>
</tr>
<tr>
   <td valign="middle"><p>Предельная пропускная способность для отдельной очереди (размер сообщения 1 КБ)</p></td>
   <td valign="middle"><p>До 2 000 сообщений в секунду</p></td>
</tr>
<tr>
   <td valign="middle"><p>Предельная пропускная способность для отдельного табличного раздела (размер сущностей 1 КБ)</p></td>
   <td valign="middle"><p>До 2 000 сущностей в секунду</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимальный поток входящих данных на учетную запись хранения (регионы США)</p></td>
   <td valign="middle"><p>10 Гбит/c, если включена функция GRS<sup>3</sup> , 20 Гбит/c для LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимальный поток исходящих данных на учетную запись хранения (регионы США)</p></td>
   <td valign="middle"><p>20 Гбит/c, если включена функция GRS<sup>3</sup> , 30 Гбит/c для LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимальный поток входящих данных на учетную запись хранения (регионы Европы и Азии)</p></td>
   <td valign="middle"><p>5 Гбит/c, если включена функция GRS<sup>3</sup> , 10 Гбит/c для LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимальный поток исходящих данных на учетную запись хранения (регионы Европы и Азии)</p></td>
   <td valign="middle"><p>10 Гбит/c, если включена функция GRS<sup>3</sup> , 15 Гбит/c для LRS</p></td>
</tr>
</table>

<sup>1</sup>Дополнительные сведения об этих лимитах см. в разделе [Масштабируемость хранилища Azure и цели анализа производительности][storagelimits]. 

<sup>2</sup>Для виртуальных машин базового уровня не размещайте более 66 активно используемых VHD-образов на учетной записи хранилища, чтобы избежать превышения лимита на 20 000 запросов (20 000/300). Для виртуальных машин на уровне Standard не рекомендуется размещать в учетной записи хранилища более 40 виртуальных жестких дисков (20 000/500). Дополнительные сведения см. в разделе [Размеры виртуальных машин и облачных служб для Azure][vmsizes]. 

<sup>3</sup>GRS - [географически избыточное хранилище][georedundantstorage]. LRS - [локально избыточное хранилище][locallyredundantstorage]. Следует иметь в виду, что GRS обладает также и локальной избыточностью.

##<a name="batchlimits"></a>Ограничения предварительной версии пакета

[WACOM.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

##<a name="documentdblimits"></a>Ограничения DocumentDB (предварительная версия)

[WACOM.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

##<a name="sqldblimits"></a>Ограничения базы данных SQL

Ограничения на базу данных SQL см. в следующих разделах:

 - [Уровни служб баз данных SQL Azure (выпуски)][sqltiers]
 - [Уровни служб баз данных SQL Azure и уровни производительности][sqltiersperflevels]
 - [Квоты пропускной способности базы данных (DTU)][sqlDTU]
 - [Лимиты ресурсов базы данных SQL][sqldblimits]

##<a name="mediaserviceslimits"></a>Ограничения служб мультимедиа

[WACOM.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

##<a name="servicebuslimits"></a>Ограничения служебной шины

[WACOM.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]

##<a name="adlimits"></a>Лимиты Active Directory

По Azure Active Directory (AD) см. следующий раздел:

 - [Лимиты и ограничения службы Azure Active Directory][adlimitsandrestrictions]

##<a name="seealso"></a>См. также

[Основные сведения о лимитах Azure и их увеличении][azurelimitsblogpost]

[Размеры виртуальных машин и облачных служб в Azure][vmsizes]

  [customersupportfaq]: http://azure.microsoft.com/support/faq/
  [azurelimitsblogpost]: http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
  [gateway]: http://msdn.microsoft.com/library/azure/jj156210.aspx 
  [storagelimits]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [sqldblimits]: http://msdn.microsoft.com/library/azure/dn338081.aspx
  [sqltiers]: http://msdn.microsoft.com/library/azure/dn741340.aspx
  [sqltiersperflevels]: http://msdn.microsoft.com/library/azure/dn741336.aspx
  [sqlDTU]: http://msdn.microsoft.com/library/azure/ee336245.aspx#DTUs
  [vmsizes]: http://msdn.microsoft.com/library/azure/dn197896.aspx
  [georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [locallyredundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx
  [adlimitsandrestrictions]: http://msdn.microsoft.com/library/azure/dn764971.aspx

<!--HONumber=35.2-->

<!--HONumber=46--> 
