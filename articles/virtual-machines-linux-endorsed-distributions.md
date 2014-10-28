<properties linkid="manage-linux-other-resources-endorsed-distributions" urlDisplayName="Endorsed distributions" pageTitle="Endorsed distributions of Linux in Azure" metaKeywords="" description="Learn about Linux on Azure-endorsed distributions, including guidelines for Ubuntu, OpenLogic, and SUSE." metaCanonical="" services="virtual-machines" documentationCenter="" title="Linux on Azure-Endorsed Distributions" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Linux в Azure — рекомендованные дистрибутивы

Образы дистрибутивов в коллекции Azure предоставлены перечисленными ниже партнерами, и мы также работаем с разными сообществами Linux, чтобы предоставить дополнительные рекомендованные дистрибутивы. Пока же в случае отсутствия нужных дистрибутивов в коллекции вы всегда можете принести свой дистрибутив Linux, выполнив инструкции на [этой странице][этой странице].

## Canonical

[][0]<http://www.ubuntu.com/cloud/azure></a>

Инженерно-техническое открытое сообщество Canonical способствует успешному распространению Ubuntu в клиентских, серверных и облачных средах, включая персональные облачные службы для потребителей. Canonical представляет Ubuntu как единую бесплатную платформу, от телефона до облака, с семейством согласованных интерфейсов для телефона, планшета, ТВ и рабочего стола, что делает Ubuntu лучшим вариантом для различных учреждений, от поставщиков общедоступных облаков до создателей бытовой электроники, и фаворитом среди частных специалистов.

Благодаря центрам разработки и проектирования по всему миру Canonical имеет уникальные возможности для партнерства с производителями оборудования, поставщиками контента и разработчиками программного обеспечения для вывода на рынок решений Ubuntu для разных сред, от компьютеров до серверов и портативных устройств.

## OpenLogic

[][1]<http://www.openlogic.com/azure></a>

OpenLogic — ведущий поставщик корпоративных решений с открытым исходным кодом для облака и центра обработки данных. OpenLogic помогает сотням ведущих предприятий в различных отраслях безопасно получать, поддерживать и контролировать программное обеспечение с открытым исходным кодом. Опираясь на экспертное сообщество, OpenLogic предлагает техническую поддержку коммерческого уровня и гарантии возмещения для 600 пакетов с открытым исходным кодом, в том числе поддержку корпоративного уровня для CentOS, а также является партнером по запуску для предоставления образов Centos в Azure.

## Oracle

[][2]<http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html></a>

Oracle предлагает широкий набор решений для общедоступных и частных облаков, предоставляя при этом клиентам свободу выбора и гибкость при развертывании программного обеспечения Oracle в облаках Oracle, а также в других облаках. Партнерство Oracle с корпорацией Майкрософт позволяет клиентам развертывать программное обеспечение Oracle в общедоступных и частных облаках Майкрософт при обеспечении сертификации и поддержки от Oracle. Заинтересованность компании Oracle и ее инвестиции в решения для общедоступных и частных облаков Oracle остаются неизменными.

## SUSE

[][3]<http://www.suse.com/suse-linux-enterprise-server-on-azure></a>

SUSE Linux Enterprise Server в Azure — проверенная платформа, предоставляющая высокую степень надежности и безопасности для работы в облаке. Универсальная платформа SUSE Linux легко интегрируется с облачными службами Azure, создавая облачную среду с простым управлением. Более 9200 сертифицированных приложений для SUSE Linux Enterprise Server от свыше 1800 независимых поставщиков программного обеспечения гарантируют, что рабочие нагрузки, поддерживаемые в центре обработки данных, могут быть беспрепятственно развернуты в Azure.

## Поддерживаемые версии

В приведенной ниже таблице показаны версии дистрибутивов, драйверы служб Linux Integration Services (LIS) и версии агента Azure Linux, которые были протестированы для работы с Azure. Драйверы LIS либо встроены в ядро дистрибутива по умолчанию, либо доступны [здесь][здесь]. Версии агента Linux доступны в репозитории пакетов дистрибутивов или на веб-сайте [Github][Github].

Таблица также содержит ссылку на [исправление для совместимости ядра Linux][исправление для совместимости ядра Linux], необходимое для оптимальной работы некоторых версий дистрибутивов и ядер в Azure.

<table border="1" width="600">
  <tr bgcolor="#E9E7E7">
        <th>Дистрибутив</th>       
        <th>Версия</th>
        <th>Драйверы</th>
        <th>Исправление для совместимости ядра</th>
        <th>Агент</th>
            </tr>
    <tr>
        <th>  Каноническая Ubuntu </th>
        <td> Ubuntu 12.04.1+, 13.10 и 14.04</td>
        <td>В ядре</td>
        <td><a href="http://go.microsoft.com/fwlink/?LinkID=275152&amp;clcid=0x409">Требуется только для версии 12.04 или 12.04.01</a></td>
        <td>Пакет: в репозитории пакетов под именем walinuxagent <br />
            Источник: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
            </tr>
    <tr>
        <th> CentOS через OpenLogic </th>
        <td> CentOS 6.3+</td>
        <td>CentOS 6.3: <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">драйверы LIS</a>; CentOS 6.4+ драйверы: в ядре</td>
        <td><a href="http://go.microsoft.com/fwlink/?LinkID=275153&amp;clcid=0x409">Требуется только для версии 6.3</a></td>
        <td>Пакет: в <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">в репозитории пакетов Open Logic</a> под именем walinuxagent<br />
            Источник: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
        
    </tr>
    <tr>
        <th> Oracle Linux </th>
        <td> 6.4+</td>
        <td>В ядре</td>
        <td>Недоступно</td>
        <td>Пакет: в репозитории, имя: WALinuxAgent<br />
            Источник: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
        
    </tr><tr>
        <th> SUSE Linux Enterprise </th>
        <td> SLES 11 SP3+</td>
        <td>В ядре</td>
        <td>Недоступно</td>
        <td>Пакет: в репозитории <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</a>, имя: WALinuxAgent<br />
            Исходный код: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
        
    </tr>
    <tr>
        <th> openSUSE </th>
        <td> openSUSE 13.1+</td>
        <td>В ядре</td>
        <td>Недоступно</td>
        <td>Пакет: в репозитории <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</a>, имя: WALinuxAgent<br />
            Исходный код: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
        
    </tr>
</table>

  [этой странице]: ../virtual-machines-linux-create-upload-vhd/
  [0]: http://www.ubuntu.com/cloud/azure
  [1]: http://www.openlogic.com/azure
  [2]: http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html
  [3]: http://www.suse.com/suse-linux-enterprise-server-on-azure
  [здесь]: http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409
  [Github]: https://github.com/azure/walinuxagent
  [исправление для совместимости ядра Linux]: http://go.microsoft.com/fwlink/?LinkID=403027&clcid=0x409
  [Требуется только для версии 12.04 или 12.04.01]: http://go.microsoft.com/fwlink/?LinkID=275152&clcid=0x409
  [GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=250998
  [Требуется только для версии 6.3]: http://go.microsoft.com/fwlink/?LinkID=275153&clcid=0x409
  [в репозитории пакетов Open Logic]: http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/
  [Cloud:Tools]: https://build.opensuse.org/project/show/Cloud:Tools
