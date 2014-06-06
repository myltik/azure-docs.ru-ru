<properties linkid="manage-linux-other-resources-endorsed-distributions" urlDisplayName="Рекомендованные дистрибутивы" pageTitle="Рекомендованные дистрибутивы Linux в Azure" metaKeywords="" description="Ознакомьтесь с рекомендованными дистрибутивами Linux в Azure, в том числе с рекомендациями для Ubuntu, OpenLogic и SUSE." metaCanonical="" services="virtual-machines" documentationCenter="" title="Рекомендованные дистрибутивы Linux в Azure" authors=""  solutions="" writer="kathydav" manager="jeffreyg" editor="tysonn"  />





#Linux в Azure — рекомендованные дистрибутивы

Образы дистрибутивов в коллекции предоставлены перечисленными ниже партнерами, и мы также работаем с сообществом, чтобы предоставить дополнительные рекомендованные дистрибутивы. Между тем вы всегда можете предоставить собственный дистрибутив Linux, следуя инструкциям на этой странице.

## Canonical ##
 
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Инженерно-техническое открытое сообщество Canonical способствует успешному распространению Ubuntu в клиентских, серверных и облачных средах, включая персональные облачные службы для потребителей. Canonical представляет Ubuntu как единую бесплатную платформу, от телефона до облака, с семейством согласованных интерфейсов для телефона, планшета, ТВ и рабочего стола, что делает Ubuntu лучшим вариантом для различных учреждений, от поставщиков общедоступных облаков до создателей бытовой электроники, и фаворитом среди частных специалистов.

Благодаря центрам разработки и проектирования по всему миру Canonical имеет уникальные возможности для партнерства с производителями оборудования, поставщиками контента и разработчиками программного обеспечения для вывода на рынок решений Ubuntu для разных сред, от компьютеров до серверов и портативных устройств.

## OpenLogic ##
 
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic — ведущий поставщик корпоративных решений с открытым исходным кодом для облака и центра обработки данных. OpenLogic помогает сотням ведущих предприятий в различных отраслях безопасно получать, поддерживать и контролировать программное обеспечение с открытым исходным кодом. Опираясь на экспертное сообщество, OpenLogic предлагает техническую поддержку коммерческого уровня и гарантии возмещения для 600 пакетов с открытым исходным кодом, в том числе поддержку корпоративного уровня для CentOS, а также является партнером по запуску для предоставления образов Centos в Azure.

## Oracle##
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle предлагает широкий набор решений для общедоступных и частных облаков, предоставляя при этом клиентам свободу выбора и гибкость при развертывании программного обеспечения Oracle в облаках Oracle, а также в других облаках.  Партнерство Oracle с корпорацией Майкрософт позволяет клиентам развертывать программное обеспечение Oracle в общедоступных и частных облаках Майкрософт при обеспечении сертификации и поддержки от Oracle.  Заинтересованность компании Oracle и ее инвестиции в решения для общедоступных и частных облаков Oracle остаются неизменными.

##SUSE##
 
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server на Azure — проверенная платформа, предоставляющая высокую степень надежности и безопасности для работы в облаке. Универсальная платформа SUSE Linux легко интегрируется с облачными службами Azure, создавая облачную среду с простым управлением. Более 9200 сертифицированных приложений для SUSE Linux Enterprise Server от свыше 1800 независимых поставщиков программного обеспечения гарантируют, что рабочие нагрузки, поддерживаемые в центре обработки данных, могут быть беспрепятственно развернуты в Azure.

## Поддерживаемые версии ##

В приведенной ниже таблице показаны версии дистрибутивов, драйверы служб Linux Integration Services (LIS) и версии агента Azure Linux, которые были протестированы для работы с Azure. Драйверы LIS доступны на веб-странице [http://www.microsoft.com/ru-ru/download/details.aspx?id=34603](http://www.microsoft.com/ru-ru/download/details.aspx?id=34603). Версии агента Linux доступны на веб-странице [https://github.com/windowsazure/walinuxagent](https://github.com/windowsazure/walinuxagent).

Таблица также содержит ссылку на исправление для совместимости ядра, необходимое для оптимальной работы некоторых версий дистрибутивов в Azure.

<table border="1" width="600">
  <tr bgcolor="#E9E7E7">
		<th>Дистрибутив</th>		
	    <th>Версия</th>
	    <th>Драйверы</th>
		<th>Исправление для совместимости ядра</th>
		<th>Агент</th>
			</tr>
	<tr>
		<th>  Canonical UBUNTU </th>
		<td> Ubuntu 12.04.1, 12.10 и 13.04</td>
		<td>В ядре</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275152&amp;clcid=0x409">Требуется только для версии 12.04 или 12.04.01</a></td>
		<td>Пакет: в репозитории пакетов под именем walinuxagent <br />
			Источник: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
			</tr>
	<tr>
		<th> CENTOS от Open Logic </th>
		<td> CentOS 6.3+</td>
	    <td>CentOS 6.3: <a href="http://go.microsoft.com/fwlink/p/?LinkID=254263">драйверы LIS</a>; драйверы CentOS 6.4+: в ядре</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275153&amp;clcid=0x409">Требуется только для версии 6.3</a></td>
		<td>Пакет: в репозитории пакетов <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">Open Logic </a> под именем walinuxagent<br />
			Источник: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
 		
	</tr>
	<tr>
		<th> Oracle Linux </th>
		<td> 6.4+</td>
        <td>В ядре</td>
		<td>Недоступно</td>
		<td>Пакет: в репозитории, имя: WALinuxAgent
<br />
			Источник: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
		
	</tr><tr>
		<th> SUSE Linux Enterprise </th>
		<td> SLES 11 SP3+</td>
        <td>В ядре</td>
		<td>Недоступно</td>
		<td>Пакет: в репозитории <A HREF="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</A>, имя: WALinuxAgent<br />
			Исходный код: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
		
	</tr>
	<tr>
		<th> openSUSE </th>
		<td> OpenSUSE 13.1+</td>
		<td>В ядре</td>
		<td>Недоступно</td>
		<td>Пакет: в репозитории <A HREF="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</A>, имя: WALinuxAgent<br />
			Исходный код: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
		
	</tr>
</table>

