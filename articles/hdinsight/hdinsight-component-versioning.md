---
title: "Компоненты и версии Hadoop в Azure HDInsight | Документация Майкрософт"
description: "Сведения о компонентах и версиях Hadoop в HDInsight, а также об уровнях обслуживания, доступных в этой облачной реализации платформы данных HortonWorks Data Platform."
keywords: "версии hadoop, компоненты экосистемы hadoop, компоненты hadoop, проверка версии hadoop"
services: hdinsight
editor: cgronlun
manager: asadk
author: bprakash
tags: azure-portal
documentationcenter: 
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: bprakash
ms.openlocfilehash: 45cccb09753c85ae4a6d077d49cbd58630a9788a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>Что представляют собой компоненты и версии Hadoop, доступные в HDInsight?

Дополнительные сведения о компоненты экосистемы Apache Hadoop и версиях в Microsoft Azure HDInsight, а также пакет безопасности предприятия. Кроме того, узнайте, как проверить версии компонентов Hadoop в HDInsight. 

Каждая версия HDInsight является облачной реализацией версии платформы данных Hortonworks Data Platform (HDP).

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Компоненты Hadoop, доступные в разных версиях HDInsight
Azure HDInsight поддерживает несколько версий кластера Hadoop, которые могут быть развернуты в любое время. Каждая из версий создает конкретную версию платформы HDP и набор компонентов, содержащихся в этой версии. Начиная с 17 февраля 2017 г. Azure HDInsight по умолчанию использует версию кластера 3.5 на основе HDP 2.5.

В следующей таблице перечислены версии компонентов, связанных с версиями кластеров HDInsight. 

> [!NOTE]
> Версия по умолчанию службы HDInsight может измениться без предварительного уведомления. Если используется зависимость версии, укажите версию HDInsight при создании кластеров с использованием пакета SDK для .NET, Azure PowerShell и Azure CLI.

| Компонент | HDInsight 3.6 (по умолчанию) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | HDInsight 3.2 | HDInsight 3.1 | HDInsight 3.0 |
| --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2,0 |
| Apache Hadoop и YARN |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive и HCatalog |1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive2 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez Hive2 | 0.8.4 |-|-|-|-|-|-|
| Apache Ranger | 0.7.0 |0.6.0 |-|-|-|-|-|
| Apache HBase |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.1.0 (только для Linux) |1.6.2 + 2.0 (только для Linux) |1.6.0 (только для Linux) |1.5.2 (только для экспериментальной сборки Linux) |1.3.1 (только для Windows) |-|-|
| Apache Kafka | 0.10.0 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.5.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |3.2.8 |-|-|-|

## <a name="check-for-current-hadoop-component-version-information"></a>Проверка сведений о текущей версии компонентов Hadoop

Версии компонентов экосистемы Hadoop, связанных с версиями кластера HDInsight, могут быть изменены в обновлениях для HDInsight. Чтобы проверить компоненты Hadoop и версии, которые должны использоваться для кластера, используйте Ambari REST API. Для извлечения сведений о компонентах службы можно воспользоваться командой **GetComponentInformation**. Дополнительные сведения см. в [документации по Ambari][ambari-docs].

Для кластеров Windows. Еще один способ проверки сведений о версиях компонента — вход в кластер с помощью удаленного рабочего стола и изучение содержимого каталога C:\apps\dist.

> [!IMPORTANT]
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](#hdinsight-windows-retirement).

### <a name="release-notes"></a>Заметки о выпуске

Дополнительные заметки о выпусках последних версий HDInsight см. [здесь](hdinsight-release-notes.md).

## <a name="supported-hdinsight-versions"></a>Поддерживаемые версии HDInsight
В следующей таблице перечислены версии HDInsight, которые в настоящее время доступны на портале Azure. Версии HDP, соответствующие каждой версии HDInsight, отображаются вместе с датой выпуска продукта. Даты прекращения поддержки и прекращения использования также предоставляются, когда они известны.

> [!NOTE]
> После реализации поддержки версии истек, он не становятся доступны на портале Microsoft Azure. Однако доступ к версиям кластера по-прежнему можно будет получить с помощью параметра `Version` в командлете Windows PowerShell [New-AzureHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) и пакета SDK для .NET до даты вывода.
> 
> Высокодоступные кластеры с двумя головными узлами развертываются по умолчанию для HDInsight версии 2.1 и более поздних. Они недоступны для кластеров HDInsight версии 1.6.

| Версия HDInsight | Версия HDP | ОС виртуальной машины | высокую доступность; | Дата выпуска | Доступность на портале Azure | Дата прекращения поддержки | Дата вывода |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16 |Yes |4 апреля 2017 г. |Yes | | |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16 |Yes |30 сентября 2016 г. |Yes |5 сентября 2017 г. |31 мая 2018 г. |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |Yes |29 марта 2016 г. |Yes |29 декабря 2016 г. |9 января 2018 г. |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |Yes |2 декабря 2015 г. |Yes |27 июня 2016 г. |31 июля 2018 г. |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |Yes |2 декабря 2015 г. |Yes |27 июня 2016 г. |31 июля 2017 г. |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS или Windows Server 2012 R2 |Yes |18 февраля 2015 г. |Нет  |1 марта 2016 г. |1 апреля 2017 г. |
| HDInsight 3.1 |HDP 2,1 |Windows Server 2012 R2 |Yes |24 июня 2014 г. |Нет  |18 мая 2015 г. |30 июня 2016 г. |
| HDInsight 3.0 |HDP 2,0 |Windows Server 2012 R2 |Yes |11 февраля 2014 г. |Нет  |17 сентября 2014 г. |30 июня 2015 г. |
| HDInsight 2.1 |HDP 1,3 |Windows Server 2012 R2 |Yes |28 октября 2013 г. |Нет  |12 мая 2014 г. |31 мая 2015 г. |
| HDInsight 1.6 |HDP 1.1 | |Нет  |28 октября 2013 г. |Нет  |26 апреля 2014 г. |31 мая 2015 г. |


## <a name="enterprise-security-package-for-hdinsight"></a>Пакет безопасности предприятия для HDInsight

Azure HDInsight — это дополнительный пакет, который можно добавить в кластере HDInsight как часть рабочего процесса создания кластера. Пакет безопасности предприятия поддерживает:

- Интеграция с Active Directory для проверки подлинности.

    В прошлом можно создать кластеры HDInsight только с именем пользователя локального администратора и локального пользователя SSH. Локальный администратор может получить доступ к все файлы, папки, таблиц и столбцов.  Пакет безопасности предприятия, можно включить управление доступом на основе ролей путем интеграции кластеров HDInsight с собственного Active Directory, среди которых в локальной среде Active Directory, доменных служб Active Directory Azure или Active Directory в IaaS Виртуальная машина. Администратор домена в кластере могут предоставлять пользователям использовать свои собственные корпоративные (домен) имя пользователя и пароль для доступа к кластеру. 

    Дополнительные сведения можно найти в разделе 

    - [Общие сведения о безопасности Hadoop с кластерами HDInsight на присоединенных к домену](./domain-joined/apache-domain-joined-introduction.md)
    - [Планирование Azure домену кластеров Hadoop в HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Настройка среды с присоединенных к домену "песочницы"](./domain-joined/apache-domain-joined-configure.md)
    - [Настройка кластеров HDInsight, присоединенных к домену, с помощью доменных служб Active Directory Azure](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Авторизация для данных

    - Интеграция с Apache круг для авторизации для Hive, Spark SQL и Yarn очереди.
    - Можно задать управление доступом к файлам и папкам.

    Дополнительные сведения можно найти в разделе 

    - [Настройка политик Hive в присоединенном к домену кластере HDInsight (предварительная версия)](./domain-joined/apache-domain-joined-run-hive.md)

- Просмотрите журналы аудита доступа монитора и настроенные политики. 

### <a name="supported-cluster-types"></a>Типы поддерживаемых кластера

В настоящее время только следующие типы кластера поддерживают пакет безопасности предприятия.

- Hadoop (только в HDInsight 3.6)
- Spark
- Interactive Query

### <a name="support-for-azure-data-lake-store"></a>Поддержка хранилища озера данных Azure

Поддерживает пакет безопасности предприятия, использование хранилища Озера данных Azure как основное хранилище и дополнительное хранилище.

### <a name="pricing-and-sla"></a>Цены и соглашение об уровне обслуживания
Сведения о ценах и соглашения об уровне ОБСЛУЖИВАНИЯ для пакета безопасности предприятия см. в разделе [цены на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="hdinsight-windows-retirement"></a>Дата вывода HDInsight под управлением Windows
Microsoft Azure HDInsight версии 3.3 был последней версией HDInsight под управлением Windows. Дата вывода HDInsight под управлением Windows — 31 июля 2018 г. Если у вас есть какие-либо кластеры HDInsight под управлением Windows версии 3.3 или более ранней, их необходимо перенести в HDInsight в Linux (HDInsight версии 3.5 или более поздней) до 31 июля 2018 г. Переход на ОС Linux позволяет сохранить возможность создавать или изменять размер кластеров HDInsight. Срок действия поддержки для HDInsight версии 3.3 в Windows истек 27 июня 2016 г.

Начиная с HDInsight версии 3.4 корпорация Майкрософт выпускает HDInsight только для ОС Linux. В результате некоторые компоненты в HDInsight доступны только для Linux. Сюда входят Apache Ranger, Kafka, Interactive Query, Spark, приложения HDInsight и Azure Data Lake Store в качестве первичной файловой системы. Будущие выпуски HDInsight доступны только в ОС Linux. Будущих выпусков HDInsight под управлением Windows не будет. 

## <a name="faqs"></a>Часто задаваемые вопросы

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>Что такое временная шкала прекращения использования HDInsight в Windows?
Дата вывода HDInsight под управлением Windows — 31 июля 2018 г. Если планируемая дата вывода отличается для вашего региона, вы получите отдельное уведомление. 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>Как повлияет вывод HDInsight под управлением Windows на имеющихся клиентов?
После вывода HDInsight под управлением Windows вы больше не сможете создать кластер HDInsight под управлением Windows или изменить размер имеющегося. Срок действия поддержки HDInsight версии 3.3 истек 27 июня 2016 г. Таким образом поддержки или исправления ошибок для HDInsight 3.3 или более ранних версий не будет. Будущие выпуски HDInsight доступны только в ОС Linux. Будущих выпусков HDInsight под управлением Windows не будет.
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>Какие версии HDInsight под управлением Windows будут затронуты?
Azure HDInsight версии 3.3 является последней версией HDInsight для Windows. Перед прекращением использования HDInsight под управлением Windows все кластеры HDInsight под управлением Windows версии 3.3 или более ранней должны быть перенесены в HDInsight в Linux версии 3.5 или более поздних. Перенос кластеров в HDInsight в Linux позволяет сохранить возможность создавать кластеры или изменять имеющиеся кластеры. 

### <a name="what-do-i-need-to-do"></a>Что нужно сделать?
Переносите кластеры HDInsight под управлением Windows в поддерживаемые кластеры HDInsight под управлением Linux до 31 июля 2018 г. Дополнительные сведения о версиях Azure HDInsight см. в статье [Миграция из кластера HDInsight под управлением Windows на кластер под управлением Linux](hdinsight-migrate-from-windows-to-linux.md). Дополнительные сведения о версиях Azure HDInsight см. в списке [поддерживаемых версий](hdinsight-component-versioning.md#supported-hdinsight-versions). 

### <a name="where-do-i-find-the-cluster-os-type"></a>Где найти тип ОС кластера?
На портале Azure перейдите на страницу обзора кластера HDInsight и в разделе **Основные компоненты** найдите **Тип кластера**. На этой странице перечислены типы ОС кластера. 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>Я не могу перенести кластер HDInsight в Linux до 31 июля 2018 г. Как это повлияет на кластер HDInsight под управлением Windows?
Кластер HDInsight под управлением Windows выполняется как есть, но вы не можете создать кластер HDInsight под управлением Windows или изменить имеющийся кластер HDInsight под управлением Windows. 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>Кластер имеет зависимость .NET. Как разрешить эту зависимость в Linux?
Зависимость кластера Linux можно разрешить с помощью [проекта Mono](http://www.mono-project.com/). Эта реализация с открытым исходным кодом .NET доступна для кластеров HDInsight под управлением Linux. Дополнительные сведения о версиях Azure HDInsight см. в статье [Миграция из кластера HDInsight под управлением Windows на кластер под управлением Linux](hdinsight-migrate-from-windows-to-linux.md). 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>Я новый клиент HDInsight под управлением Windows. Как создать кластер HDInsight под управлением Windows?
Начиная с 3 июля 2017 г. только имеющиеся клиенты HDInsight под управлением Windows могут создавать кластеры HDInsight. Новые клиенты не могут создавать кластер HDInsight под управлением Windows на портале Azure с помощью PowerShell или пакета SDK. Рекомендуется, чтобы новые клиенты создавали кластер HDInsight под управлением Linux. Имеющиеся клиенты могут создавать кластеры HDInsight под управлением Windows до наступления его даты вывода. 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>Влияет ли на цену переход с HDInsight под управлением Windows на HDInsight в Linux?
Нет, цены одинаковы для HDInsight в любой операционной системе. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>Каковы преимущества для клиента в связи с использованием только HDInsight под управлением Linux?
* Более быстрый выход на рынок для технологий обработки больших данных с открытым кодом через службу HDInsight.
* Большое сообщество и экосистема, где можно получить поддержку.
* Возможность выполнять активную разработку с помощью сообщества разработчиков ПО с открытым кодом для Hadoop и других технологий обработки больших данных.

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>Предоставляет ли HDInsight на Linux дополнительные функции, недоступные в HDInsight под управлением Windows?
Начиная с HDInsight версии 3.4 корпорация Майкрософт выпускает HDInsight только для ОС Linux. В результате некоторые компоненты в HDInsight доступны только для Linux. Сюда входят Apache Ranger, Kafka, Interactive Query, Spark, приложения HDInsight и Azure Data Lake Store в качестве первичной файловой системы. 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Соглашение об уровне обслуживания (SLA) для версий кластера HDInsight
Соглашение об уровне обслуживания определяется в терминах _окна поддержки_. Под окном поддержки понимается период времени, в течение которого определенная версия кластера HDInsight поддерживается службой поддержки клиентов корпорации Майкрософт. Если версия достигла _даты прекращения поддержки_, кластер HDInsight находится за пределами окна поддержки. Дополнительные сведения о поддерживаемых версиях см. в [этом списке](hdinsight-migrate-from-windows-to-linux.md). Дата истечения срока действия технической поддержки для конкретной версии HDInsight (обозначается как версия X) рассчитывается как наиболее поздняя из следующих дат (после доступности более новой версии Х+1):  

* Формула 1. Добавьте 180 дней к дате выхода версии X кластера HDInsight.
* Формула 2. Добавьте 90 дней к дате, когда версия кластера HDInsight X + 1 стала доступной на портале Azure.

_Дата вывода_ — это дата, после которой версия кластера не может быть создана в HDInsight. Начиная с 31 июля 2017 г. нельзя изменить размер кластера HDInsight после даты вывода. 

> [!NOTE]
> Кластеры HDInsight под управлением Windows (включая версии 2.1, 3.0, 3.1, 3.2 и 3.3) выполняются на гостевых ОС Azure версии 4, которая использует 64-разрядную версию Windows Server 2012 R2. Гостевая ОС Azure версии 4 поддерживает платформы .NET версии 4.0, 4.5, 4.5.1 и 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Заметки о выпуске Hortonworks, связанные с версиями HDInsight

Раздел содержит ссылки на заметки о выпуске для дистрибутивов Hortonworks Data Platform и компонентов Apache, которые используются с HDInsight.
* Кластер HDInsight версии 3.6 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* Кластер HDInsight версии 3.5 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Кластер HDInsight версии 3.5 является кластером Hadoop _по умолчанию_, который создается на портале Azure.
* Кластер HDInsight версии 3.4 использует пакет Hadoop, построенный на платформе [Нortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* Кластер HDInsight версии 3.3 использует пакет Hadoop, построенный на платформе [Нortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Заметки о выпуске Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) доступны на веб-сайте Apache.
  * [Заметки о выпуске Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) доступны на веб-сайте Apache.
* Кластер HDInsight версии 3.2 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 2.2][hdp-2-2].

  * Заметки о выпуске для отдельных компонентов Apache: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112) и [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* Кластер HDInsight версии 3.1 использует дистрибутив Hadoop, основанный на платформе [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Кластеры HDInsight версии 3.1, созданные до 7 ноября 2014 г., основаны на платформе [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* Кластер HDInsight версии 3.0 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 2.0][hdp-2-0-8].
* Кластер HDInsight версии 2.1 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 1.3][hdp-1-3-0].
* Кластер HDInsight версии 1.6 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 1.1][hdp-1-1-0].






## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Конфигурация узлов и размеры виртуальных машин по умолчанию для кластеров
В следующих таблицах перечислены размеры виртуальных машин по умолчанию для кластеров HDInsight.

> [!IMPORTANT]
> Если в кластере требуется более 32 рабочих узлов, для головного узла следует выбрать размер с по крайней мере 8 ядрами и 14 ГБ ОЗУ.
> 
> 

* Все поддерживаемые регионы, кроме южной Бразилии и западной Японии:

  | Тип кластера | Hadoop | hbase | Interactive Query | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- | --- |
  | Головной узел: размер виртуальной машины по умолчанию |D3 v2 |D3 v2 | D13, D14 |A3 |D12 v2 |D12 v2 |
  | Головной узел: рекомендуемые размеры виртуальных машин |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |A3, A4, A5 |D12 v2, D13 v2, D14 v2 |D12 v2, D13 v2, D14 v2 |
  | Рабочая роль: размер виртуальной машины по умолчанию |D3 v2 |D3 v2  | D13, D14 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | Рабочая роль: рекомендуемые размеры виртуальных машин |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |D3 v2, D4 v2, D12 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
  | ZooKeeper: размер виртуальной машины по умолчанию | |A3 | |A2 | | |
  | ZooKeeper: рекомендуемые размеры виртуальных машин | |A3, A4, A5 | | A2, A3, A4 | | |
  | Граничный узел: размер виртуальной машины по умолчанию | | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Граничный узел: рекомендуемый размер виртуальной машины | | | | | |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
* Только южная Бразилия и западная Япония (нет размеров v2):

  | Тип кластера | Hadoop | hbase | Interactive Query |Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- | --- |
  | Головной узел: размер виртуальной машины по умолчанию |D3 |D3  | D13, D14 |A3 |D12 |D12 |
  | Головной узел: рекомендуемые размеры виртуальных машин |D3, D4, D12 |D3, D4, D12  | D13, D14 |A3, A4, A5 |D12, D13, D14 |D12, D13, D14 |
  | Рабочая роль: размер виртуальной машины по умолчанию |D3 |D3  | D13, D14 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | Рабочая роль: рекомендуемые размеры виртуальных машин |D3, D4, D12 |D3, D4, D12  | D13, D14 |D3, D4, D12 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |
  | ZooKeeper: размер виртуальной машины по умолчанию | |A2 | | A2 | | |
  | ZooKeeper: рекомендуемые размеры виртуальных машин | |A2, A3, A4 | |A2, A3, A4 | | |
  | Граничный узел: размеры виртуальных машин по умолчанию | | | | | |Windows: D12; Linux: D4 |
  | Граничный узел: рекомендуемые размеры виртуальных машин | | | | | |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |

> [!NOTE]
> - Головной узел для типа кластера Storm называется *Nimbus*.
> - Рабочая роль в типах кластера Storm называется *супервизором*.
> - Рабочая роль в типах кластера Storm называется *регионом*.

## <a name="next-steps"></a>Дальнейшие действия
- [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](hdinsight-hadoop-provision-linux-clusters.md)
- [Работа в экосистеме Hadoop в HDInsight на компьютере с Windows](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/
