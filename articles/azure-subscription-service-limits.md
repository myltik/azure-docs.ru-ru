<properties urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Подписка Microsoft Azure, ограничения служб, квоты и ограничения" metaKeywords="Cloud Services, Virtual Machines, Web Sites, Virtual Network, SQL Database, Subscription, Storage" description="В данном разделе приводится перечень наиболее распространенных ограничений, относящихся к подписке Azure и различным службам, наряду с максимальными значениями соответствующих показателей." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth" />

# Подписка Azure, границы, квоты и ограничения службы

Следующий документ описывает некоторые из наиболее известных ограничений Microsoft Azure. Учтите, что этот документ на текущий момент охватывает не все службы Azure. Со временам эти ограничения будут расширены.

-   [Ограничения подписки][Ограничения подписки]
-   [Ограничения облачной службы][Ограничения облачной службы]
-   [Ограничения виртуальной машины][Ограничения виртуальной машины]
-   [Ограничения веб-сайтов][Ограничения веб-сайтов]
-   [Ограничения сети][Ограничения сети]
-   [Ограничения хранилища][Ограничения хранилища]
-   [Ограничения DocumentDB (предварительная версия)][Ограничения DocumentDB (предварительная версия)]
-   [Ограничения базы данных SQL][Ограничения базы данных SQL]
-   [Ограничения служб мультимедиа][Ограничения служб мультимедиа]
-   [Ограничения служебной шины][Ограничения служебной шины]

> [WACOM.NOTE] Если необходимо увеличить предельные значения, определенные **Ограничениями по умолчанию**, следует [направить соответствующий запрос в онлайн-службу поддержки клиентов, при этом оплата за такой запрос не взимается][направить соответствующий запрос в онлайн-службу поддержки клиентов, при этом оплата за такой запрос не взимается]. Ограничения не могут быть увеличены выше значения **Максимальное ограничение** в таблицах ниже. Если столбца **Максимальное ограничение** нет, то указанный ресурс не имеет настраиваемого ограничения.

## <a name="subscription"></a>Ограничения подписки

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ресурс</th>
<th align="left">Ограничение по умолчанию</th>
<th align="left">Максимальное ограничение</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Ядер на <a href="http://msdn.microsoft.com/ru-ru/library/azure/hh531793.aspx">подписку</a><sup>1</sup></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>10,000</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/ru-ru/library/azure/gg456328.aspx">Дополнительные администраторы</a> на подписку</p></td>
<td align="left"><p>200</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/ru-ru/documentation/articles/storage-whatis-account/">Учетные записи хранилища</a> на подписку</p></td>
<td align="left"><p>100</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://azure.microsoft.com/ru-ru/documentation/articles/cloud-services-what-is/">Облачные службы</a> на подписку</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Виртуальные сети</a> на подписку<sup>2</sup></p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/ru-ru/library/jj157100.aspx">Локальные сети</a> на подписку</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Баз данных SQL на подписку</p></td>
<td align="left"><p>150</p></td>
<td align="left"><p>500</p></td>
</tr>
<tr class="even">
<td align="left"><p>Логических серверов баз данных SQL на подписку</p></td>
<td align="left"><p>6</p></td>
<td align="left"><p>150</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Серверы DNS на подписку</p></td>
<td align="left"><p>9</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p>Зарезервированные IP на подписку</p></td>
<td align="left"><p>5</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Сертификаты размещенной службы на подписку</p></td>
<td align="left"><p>400</p></td>
<td align="left"><p>400</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/ru-ru/library/azure/jj156085.aspx">Территориальные группы</a> на подписку</p></td>
<td align="left"><p>256</p></td>
<td align="left"><p>256</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Сверхмалые экземпляры считаются как одно ядро в отношении ограничения ядер, несмотря на частичную загрузку ядра.

<sup>2</sup>Каждая виртуальная сеть поддерживает единственный виртуальный сетевой шлюз.

## <a name="webworkerlimits"></a>Ограничения облачной службы

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ресурс</th>
<th align="left">Ограничение по умолчанию</th>
<th align="left">Максимальное ограничение</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/ru-ru/documentation/articles/cloud-services-what-is/">Веб- и рабочие роли на развертывание<sup>1</sup></a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/ru-ru/library/gg557552.aspx#InstanceInputEndpoint">Входные конечные точки экземпляра</a> на развертывание</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/ru-ru/library/gg557552.aspx#InputEndpoint">Входные конечные точки</a> на развертывание</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/ru-ru/library/gg557552.aspx#InternalEndpoint">Внутренние конечные точки</a> на развертывание</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Каждая облачная служба с ролями рабочего или веб-процесса может иметь два развертывания: одно для рабочего, и другое для промежуточного развертывания. Необходимо также иметь в виду, что данное ограничение относится к количеству отдельных ролей (настройка), а не к количеству экземпляров для роли (масштабирование).

## <a name="vmlimits"></a>Ограничения виртуальной машины

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ресурс</th>
<th align="left">Ограничение по умолчанию</th>
<th align="left">Максимальное ограничение</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/ru-ru/documentation/services/virtual-machines/">Виртуальные машины</a> на облачную службу<sup>1</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>Входные конечные точки на облачную службу<sup>2</sup></p></td>
<td align="left"><p>150</p></td>
<td align="left"><p>150</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>При создании виртуальной машины автоматически создается содержащая ее облачная служба. Можно далее добавить несколько виртуальных машин в ту же самую облачную службу.

<sup>2</sup>Входные конечные точки используются для обеспечения подсоединения к виртуальным машинам, которые являются внешними по отношению к облачной службе. Виртуальным машинам в одной облачной службе автоматически разрешено подсоединение по всем портам UDP и TCP для внутренних коммуникаций.

## <a name="websiteslimits"></a>Ограничения веб-сайтов

[WACOM.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

## <a name="networkinglimits"></a>Ограничения сети

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ресурс</th>
<th align="left">Ограничение по умолчанию</th>
<th align="left">Максимальное ограничение</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Всего машин <sup>1</sup> на <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">виртуальную сеть</a><sup>2</sup></p></td>
<td align="left"><p>2048</p></td>
<td align="left"><p>2048</p></td>
</tr>
<tr class="even">
<td align="left"><p>Параллельных соединений TCP для виртуальной машины или экземпляра роли</p></td>
<td align="left"><p>500K</p></td>
<td align="left"><p>500K</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Списки управления доступом (ACL) на конечную точку<sup>3</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>Сайтов локальной сети на виртуальную сеть</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>10</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Общее число машин включает виртуальные машины и экземпляры ролей рабочего и веб-процессов.

<sup>2</sup>Каждая виртуальная сеть поддерживает единственный [виртуальный сетевой шлюз][виртуальный сетевой шлюз].

<sup>3</sup>ACL поддерживаются на конечных точках входа для виртуальных машин. Для ролей рабочего или веб-процесса поддерживаются для входных конечных точек и входных конечных точек экземпляра.

## <a name="storagelimits"></a>Ограничения хранилища

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ресурс<sup>1</sup></th>
<th align="left">Ограничение по умолчанию</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>ТБ на учетную запись хранилища</p></td>
<td align="left"><p>500 TБ</p></td>
</tr>
<tr class="even">
<td align="left"><p>Максимальный размер одного контейнера BLOB-объектов, таблицы или очереди</p></td>
<td align="left"><p>500 TБ</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Максимальное количество контейнеров BLOB-объектов, BLOB-объектов, общих папок, таблиц, очередей, сущностей или сообщений для учетной записи хранения</p></td>
<td align="left"><p>Ограничение касается только размера учетной записи хранения, который не может превышать 500 TБ</p></td>
</tr>
<tr class="even">
<td align="left"><p>Максимальный размер общей папки</p></td>
<td align="left"><p>5 TБ</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Максимальное количество файлов в общей папке</p></td>
<td align="left"><p>Ограничение касается только размера общей папки, который не может превышать 5 TБ</p></td>
</tr>
<tr class="even">
<td align="left"><p>Максимум 8 КБ на операции ввода вывода в секунду для постоянного диска (уровень Basic)</p></td>
<td align="left"><p>300<sup>2</sup></p></td>
</tr>
<tr class="odd">
<td align="left"><p>Максимум 8 КБ на операции ввода вывода в секунду для постоянного диска (уровень Standard)</p></td>
<td align="left"><p>500<sup>2</sup></p></td>
</tr>
<tr class="even">
<td align="left"><p>Частота запросов (предполагается размер объекта в 1 КБ) для учетной записи хранилища</p></td>
<td align="left"><p>До 20 000 сущностей или сообщений в секунду</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Предельная пропускная способность для отдельного BLOB-объекта</p></td>
<td align="left"><p>До 60 МБ в секунду или до 500 запросов в секунду</p></td>
</tr>
<tr class="even">
<td align="left"><p>Предельная пропускная способность для отдельной очереди (размер сообщения 1 КБ)</p></td>
<td align="left"><p>До 2 000 сообщений в секунду</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Предельная пропускная способность для отдельного табличного раздела (размер сущностей 1 КБ)</p></td>
<td align="left"><p>До 2 000 сущностей в секунду</p></td>
</tr>
<tr class="even">
<td align="left"><p>Максимум входов на учетную запись хранилища (регион — США)</p></td>
<td align="left"><p>10 Гбит/с, если включена служба GRS<sup>3</sup>, 20 Гбит/с для LRS</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Максимум выходов на учетную запись хранилища (регион — США)</p></td>
<td align="left"><p>20 Гбит/c, если включен GRS<sup>3</sup>, 30 Гбит/c для LRS</p></td>
</tr>
<tr class="even">
<td align="left"><p>Максимум входов на учетную запись хранилища (регионы — Европа и Азия)</p></td>
<td align="left"><p>5 Гбит/c, если включен GRS<sup>3</sup>, 10 Гбит/c для LRS</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Максимум выходов на учетную запись хранилища (регионы — Европа и Азия)</p></td>
<td align="left"><p>10 Гбит/c, если включена службаGRS<sup>3</sup>, 15 Гбит/c для LRS</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Для получения дополнительной информации см. раздел [Цели масштабирования и производительности хранилища Azure][Цели масштабирования и производительности хранилища Azure].

<sup>2</sup>Для виртуальных машин на уровне Basic Tier не размещайте более 66 часто используемых виртуальных жестких дисков в учетной записи хранилища, чтобы не превышать ограничения на общую частоту запросов 20 000 (20 000/300). Для виртуальных машин на уровне Standard не рекомендуется размещать в учетной записи хранилища более 40 виртуальных жестких дисков (20 000/500). Дополнительные сведения см. в разделе [Размеры виртуальных машин и облачных служб в Azure][Размеры виртуальных машин и облачных служб в Azure].

<sup>3</sup>GRS означает [геоизбыточное хранилище][геоизбыточное хранилище] (Geo Redundant Storage, GRS). LRS означает [Locally Redundant Storage (Локально избыточное хранилище)][Locally Redundant Storage (Локально избыточное хранилище)]. Следует иметь в виду, что GRS обладает также и локальной избыточностью.

## <a name="documentdblimits"></a>Ограничения DocumentDB (предварительная версия)

[WACOM.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

## <a name="sqldblimits"></a>Ограничения базы данных SQL

Ограничения на базу данных SQL см. в следующих разделах:

-   [Уровни службы базы данных SQL Azure (выпуски)][Уровни службы базы данных SQL Azure (выпуски)]
-   [Уровни и уровни производительности службы базы данных SQL Azure][Уровни и уровни производительности службы базы данных SQL Azure]
-   [Квоты на единицу пропускной способности для баз данных (Database Throughput Unit, DTU)][Квоты на единицу пропускной способности для баз данных (Database Throughput Unit, DTU)]
-   [Ограничения ресурсов базы данных SQL][Ограничения ресурсов базы данных SQL]

## <a name="mediaserviceslimits"></a>Ограничения служб мультимедиа

[WACOM.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

## <a name="servicebuslimits"></a>Ограничения служебной шины

[WACOM.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]

## <a name="seealso"></a> См. также

[Основные сведения об ограничениях в Azure и об их увеличении][направить соответствующий запрос в онлайн-службу поддержки клиентов, при этом оплата за такой запрос не взимается]

[Размеры виртуальных машин и облачных служб Azure][Размеры виртуальных машин и облачных служб в Azure]

  [Ограничения подписки]: #subscription
  [Ограничения облачной службы]: #webworkerlimits
  [Ограничения виртуальной машины]: #vmlimits
  [Ограничения веб-сайтов]: #websiteslimits
  [Ограничения сети]: #networkinglimits
  [Ограничения хранилища]: #storagelimits
  [Ограничения DocumentDB (предварительная версия)]: #documentdblimits
  [Ограничения базы данных SQL]: #sqldblimits
  [Ограничения служб мультимедиа]: #mediaserviceslimits
  [Ограничения служебной шины]: #servicebuslimits
  [направить соответствующий запрос в онлайн-службу поддержки клиентов, при этом оплата за такой запрос не взимается]: http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
  [виртуальный сетевой шлюз]: http://msdn.microsoft.com/ru-ru/library/azure/jj156210.aspx
  [Цели масштабирования и производительности хранилища Azure]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [Размеры виртуальных машин и облачных служб в Azure]: http://msdn.microsoft.com/ru-ru/library/azure/dn197896.aspx
  [геоизбыточное хранилище]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [Locally Redundant Storage (Локально избыточное хранилище)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx
  [Уровни службы базы данных SQL Azure (выпуски)]: http://msdn.microsoft.com/ru-ru/library/azure/dn741340.aspx
  [Уровни и уровни производительности службы базы данных SQL Azure]: http://msdn.microsoft.com/ru-ru/library/azure/dn741336.aspx
  [Квоты на единицу пропускной способности для баз данных (Database Throughput Unit, DTU)]: http://msdn.microsoft.com/ru-ru/library/azure/ee336245.aspx#DTUs
  [Ограничения ресурсов базы данных SQL]: http://msdn.microsoft.com/ru-ru/library/azure/dn338081.aspx
