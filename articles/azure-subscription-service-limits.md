<properties linkid="azure-subscription-service-limits" urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Microsoft Azure Subscription and Service Limits, Quotas, and Constraints" metaKeywords="Cloud Services, Virtual Machines, Web Sites, Virtual Network, SQL Database, Subscription, Storage" description="Provides a list of common Azure subscription and service limits along with maximum values." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"/>

# Подписка Azure, границы, квоты и ограничения службы

Следующий документ описывает некоторые из наиболее известных ограничений Microsoft Azure. Учтите, что этот документ на текущий момент охватывает не все службы Azure. Со временам эти ограничения будут расширены.

-   [Ограничения подписки][]
-   [Ограничения рабочей или веб-роли][]
-   [Ограничения виртуальной машины][]
-   [Ограничения сети][]
-   [Ограничения хранилища][]
-   [Ограничения базы данных SQL][]

> [WACOM.NOTE] Если вы хотите увеличить ограничение выше значения **Стандартное ограничение**, создайте запрос в [службу поддержки][]. Ограничения не могут быть увеличены выше значения **Максимальное ограничение** в таблицах ниже. Если столбца **Максимальное ограничение** нет, то указанный ресурс не имеет настраиваемого ограничения.

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
<td align="left"><p>Ядер на <a href="http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx">подписку</a><sup>1</sup></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>10,000</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx">Дополнительные администраторы</a> на подписку</p></td>
<td align="left"><p>200</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/articles/storage-whatis-account/">Учетные записи хранилища</a> на подписку</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/">Облачные службы</a> на подписку</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Виртуальные сети</a> на подписку<sup>2</sup></p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/jj157100.aspx">Локальные сети</a> на подписку</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
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
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx">Территориальные группы</a> на подписку</p></td>
<td align="left"><p>256</p></td>
<td align="left"><p>256</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Сверхмалые экземпляры считаются как одно ядро в отношении ограничения ядер, несмотря на частичную загрузку ядра.

<sup>2</sup>Каждая виртуальная сеть поддерживает единственный виртуальный сетевой шлюз.

## <a name="webworkerlimits"></a>Ограничения веб- или рабочей роли

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
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/">Веб- и рабочие роли на развертывание<sup>1</sup></a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint">Входные конечные точки экземпляра</a> на развертывание</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint">Входные конечные точки</a> на развертывание</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint">Внутренние конечные точки</a> на развертывание</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Каждая облачная служба с ролями рабочего или веб-процесса может иметь два развертывания: одно для рабочего, и другое для промежуточного развертывания.

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
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/services/virtual-machines/">Виртуальные машины</a> на облачную службу<sup>1</sup></p></td>
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

<sup>2</sup>Каждая виртуальная сеть поддерживает единственный [виртуальный сетевой шлюз][].

<sup>3</sup>ACL поддерживаются на конечных точках входа для виртуальных машин. Для ролей рабочего или веб-процесса поддерживаются для входных конечных точек и входных конечных точек экземпляра.

## <a name="storagelimits"></a>Ограничения хранилища<sup>1</sup>

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ресурс</th>
<th align="left">Ограничение по умолчанию</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>ТБ на учетную запись хранилища<sup>2</sup></p></td>
<td align="left"><p>500</p></td>
</tr>
<tr class="even">
<td align="left"><p>Максимум операций ввода-вывода на постоянный диск</p></td>
<td align="left"><p>500<sup>3</sup></p></td>
</tr>
<tr class="odd">
<td align="left"><p>Максимум операций ввода-вывода на учетную запись хранилища</p></td>
<td align="left"><p>20,000</p></td>
</tr>
<tr class="even">
<td align="left"><p>Максимум входов на учетную запись хранилища (регион — США)</p></td>
<td align="left"><p>10 гбит/c, если включен GRS<sup>4</sup>, 20 гбит/c, если выключен</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Максимум входов на учетную запись хранилища (регионы — Европа и Азия)</p></td>
<td align="left"><p>5 Гбит/c, если включен GRS<sup>4</sup>, 10 Гбит/c, если выключен</p></td>
</tr>
<tr class="even">
<td align="left"><p>Максимум выходов на учетную запись хранилища (регион — США)</p></td>
<td align="left"><p>20 Гбит/c, если включен GRS<sup>4</sup>, 30 Гбит/c, если выключен</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Максимум выходов на учетную запись хранилища (регионы — Европа и Азия)</p></td>
<td align="left"><p>10 Гбит/c, если включен GRS<sup>4</sup>, 15 Гбит/c, если выключен</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Для получения дополнительной информации см. раздел [Цели масштабирования и производительности хранилища Azure][].

<sup>2</sup>Для больших двоичных объектов страниц начисление идет только за используемые страницы. Например, за виртуальную машину с VHD-диском в 127 ГБ, у которой только 30 ГБ заняты ОС, плата будет начислена только за использованную долю в 30 ГБ, а не за все 127 ГБ.

<sup>3</sup>Не размещайте более 40 часто используемых VHD-дисков в учетной записи, чтобы не столкнуться с ограничением в 20 000 операций ввода-вывода.

<sup>4</sup>Учетная запись геоизбыточного хранилища

## <a name="sqldblimits"></a>Ограничения базы данных SQL

Ограничения на базу данных SQL см. в следующих разделах:

-   [Уровни службы базы данных SQL Azure (выпуски)][]
-   [Уровни и уровни производительности службы базы данных SQL Azure][]
-   [Ограничения ресурсов базы данных SQL][]

## <a name="seealso"></a> См. также

[Размеры виртуальных машин и облачных служб Azure][]

[Цели масштабируемости хранилища и производительности Azure][Цели масштабирования и производительности хранилища Azure]

[Уровни службы базы данных SQL Azure (выпуски)][]

[Уровни и уровни производительности службы базы данных SQL Azure][]

[Ограничения ресурсов базы данных SQL][]

  [Ограничения подписки]: #subscription
  [Ограничения рабочей или веб-роли]: #webworkerlimits
  [Ограничения виртуальной машины]: #vmlimits
  [Ограничения сети]: #networkinglimits
  [Ограничения хранилища]: #storagelimits
  [Ограничения базы данных SQL]: #sqldblimits
  [службу поддержки]: http://azure.microsoft.com/en-us/support/faq/
  [подписку]: http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx
  [Дополнительные администраторы]: http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx
  [Учетные записи хранилища]: http://azure.microsoft.com/en-us/documentation/articles/storage-whatis-account/
  [Облачные службы]: http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/
  [Виртуальные сети]: http://msdn.microsoft.com/library/azure/jj156007.aspx
  [Локальные сети]: http://msdn.microsoft.com/en-us/library/jj157100.aspx
  [Территориальные группы]: http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx
  [Входные конечные точки экземпляра]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint
  [Входные конечные точки]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint
  [Внутренние конечные точки]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint
  [Виртуальные машины]: http://azure.microsoft.com/en-us/documentation/services/virtual-machines/
  [виртуальный сетевой шлюз]: http://msdn.microsoft.com/en-us/library/azure/jj156210.aspx
  [Цели масштабирования и производительности хранилища Azure]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [Уровни службы базы данных SQL Azure (выпуски)]: http://msdn.microsoft.com/en-us/library/azure/dn741340.aspx
  [Уровни и уровни производительности службы базы данных SQL Azure]: http://msdn.microsoft.com/en-us/library/azure/dn741336.aspx
  [Ограничения ресурсов базы данных SQL]: http://msdn.microsoft.com/en-us/library/azure/dn338081.aspx
  [Размеры виртуальных машин и облачных служб Azure]: http://msdn.microsoft.com/en-us/library/azure/dn197896.aspx
