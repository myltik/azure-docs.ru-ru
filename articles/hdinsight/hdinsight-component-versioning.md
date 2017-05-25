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
ms.date: 04/14/2017
ms.author: bprakash
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: f6f50906766408226ad3ab9fdfd67b4707961b9b
ms.contentlocale: ru-ru
ms.lasthandoff: 05/17/2017

 

---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>Что представляют собой компоненты и версии Hadoop, доступные в HDInsight?

Узнайте о компонентах и версиях экосистемы Hadoop в Azure HDInsight, а также об уровнях обслуживания "Стандартный" и "Премиум". Кроме того, узнайте, как проверить версии компонентов Hadoop в HDInsight. 

Каждая версия HDInsight является облачной реализацией версии платформы данных HortonWorks Data Platform (HDP).

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Компоненты Hadoop, доступные в разных версиях HDInsight
Azure HDInsight поддерживает несколько версий кластера Hadoop, которые могут быть развернуты в любое время. Каждая из версий создает конкретную версию платформы HortonWorks Data Platform (HDP) и набор компонентов, содержащихся в этой версии. Сейчас Azure HDInsight по умолчанию использует версию кластера 3.5 на основе HDP 2.5 (по состоянию на 17 февраля 2017 г.).

Версии компонентов, связанные с версиями кластера HDInsight, приведены в указанной ниже таблице. 

> [!NOTE]
> Версия по умолчанию из службы может быть изменена без уведомления. Если используется зависимость версии, мы рекомендуем указать версию при создании кластеров с использованием пакета SDK для .NET, Azure PowerShell и Azure CLI. 
>
>


| Компонент | HDInsight версии 3.6 | HDInsight версии 3.5 (по умолчанию) | HDInsight версия 3.4 | HDInsight версия 3.3 | HDInsight версия 3.2 | HDInsight версия 3.1 | HDInsight версия 3,0 |
| --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2,0 |
| Apache Hadoop & YARN |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive & HCatalog |1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive2 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez-Hive2 | 0.8.4 |-|-|-|-|-|-|
| Apache Ranger | 0.7.0 |0.6.0 |-|-|-|-|-|
| Apache HBase |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.1.0 (только для Linux) |1.6.2 + 2.0 (только для Linux) |1.6.0 (только для Linux) |1.5.2 (только для Linux, экспериментальная сборка) |1.3.1 (только для Windows) |-|-|
| Apache Kafka | 0.10.0 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.5.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |3.2.8 |-|-|-|


## <a name="how-to-check-current-hadoop-component-version-information"></a>Проверка сведений о текущей версии компонентов Hadoop

Версии компонентов экосистемы Hadoop, связанных с версиями кластера HDInsight, могут быть изменены в обновлениях для HDInsight. Чтобы проверить компоненты Hadoop и версии, которые должны использоваться для кластера, используйте Ambari REST API. Для извлечения сведений о компонентах службы можно воспользоваться командой **GetComponentInformation**. Дополнительные сведения см. в [документации по Ambari][ambari-docs].

Только для кластеров под управлением Windows. Еще один способ получения сведений о версиях компонента — вход в кластер с помощью удаленного рабочего стола и непосредственное изучение содержимого каталога C:\apps\dist\".

> [!IMPORTANT]
> Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. Чтобы узнать больше, ознакомьтесь с разделом [Приближается дата прекращения сопровождения HDI версии 3.3](#hdi-version-33-nearing-deprecation-date). 

### <a name="release-notes"></a>Заметки о выпуске

Дополнительные заметки о выпусках последних версий HDInsight см. [здесь](hdinsight-release-notes.md).

## <a name="supported-hdinsight-versions"></a>Поддерживаемые версии HDInsight
В следующей таблице перечислены доступные в настоящее время версии HDInsight, соответствующие версии платформы Hortonworks Data Platform (HDP), которые они используют, и даты их выхода. Также указываются даты прекращения поддержки и даты устаревания, если они известны. Обратите внимание на сведения о следующей версии:

* Высокодоступные кластеры с двумя головными узлами развертываются по умолчанию для HDInsight 2.1 и последующих версий. Они недоступны для кластеров HDInsight 1.6.
* При истечении срока поддержки для определенной версии она может стать недоступной через портал Azure. В таблице ниже указаны версии, доступные на классическом портале Azure. Доступ к версиям кластера по-прежнему можно будет получить с помощью параметра `Version` в командлете Windows PowerShell [New-AzureHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) и пакета SDK .NET до даты его устаревания.

| Версия HDInsight | Версия HDP | ОС виртуальной машины | Высокая доступность | Дата выпуска | Доступно на портале Azure | Дата прекращения поддержки | Дата прекращения сопровождения |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDI 3.6 |HDP 2.6 |Ubuntu 16 |Да |06.04.2017 |Да | | |
| HDI 3.5 |HDP 2.5 |Ubuntu 16 |Да |30.09.2016 |Да |05.07.2017 |31.05.2018 |
| HDI 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |Да |03/29/2016 |Да |29.12.2016 |09.01.2018 |
| HDI 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS или Windows Server 2012R2 |Да |12.02.2015 |Да |27.06.2016 |31.07.2017 |
| HDI 3.2 |HDP 2.2 |Ubuntu 12.04 LTS или Windows Server 2012R2 |Да |18.02.2015 |Нет |01.03.2016 |01.04.2017 |
| HDI 3,1 |HDP 2,1 |Windows Server 2012R2 |Да |24.06.2014 |Нет |18.05.2015 |30.06.2016 |
| HDI 3,0 |HDP 2,0 |Windows Server 2012R2 |Да |11.02.2014 |Нет |17.09.2014 |30.06.2015 |
| HDI 2,1 |HDP 1,3 |Windows Server 2012R2 |Да |28.10.2013 |Нет |12.05.2014 |31.05.2015 |
| HDI 1.6 |HDP 1.1 | |Нет |28.10.2013 |Нет |26.04.2014 |31.05.2015 |

## <a name="hdi-version-33-nearing-deprecation-date"></a>Приближается дата прекращения сопровождения HDI версии 3.3
Поддержка кластеров HDI 3.3 истекла 27.06.2016, и они будут считаться нерекомендуемыми с 31.07.2017. Если вы используете кластер HDI 3.3, то в ближайшее время обновите его до кластера HDI 3.5 или 3.6. Сроки прекращения сопровождения HDI 3.3 Windows могут отличаться в зависимости от региона. Если дата планового прекращения сопровождения для вашего региона отличается, вы будете уведомлены отдельно.

### <a name="the-service-level-agreement-for-hdinsight-cluster-versions"></a>Соглашение об уровне обслуживания (SLA) для версий кластера HDInsight
Соглашение об уровне обслуживания определяется в терминах **Окна поддержки**. Под окном поддержки понимается период времени, в течение которого определенная версия кластера HDInsight поддерживается службой поддержки клиентов корпорации Майкрософт. Кластер HDInsight находится вне окна поддержки, если **дата истечения срока технической поддержки его версии** уже миновала. Список поддерживаемых версий кластера HDInsight можно найти в приведенной выше таблице. Дата истечения срока действия технической поддержки для конкретной версии HDInsight (обозначается как версия X) рассчитывается как наиболее поздняя из следующих дат (в случае доступности более новой версии Х+1):  

* Формула 1: добавьте 180 дней к дате выхода версии X кластера HDInsight.
* Формула 2: добавьте 90 дней к дате, когда версия X + 1 (следующая за версией X) кластера HDInsight стала доступной на портале.

**Дата устаревания** — это дата, после которой версия кластера не может быть создана в HDInsight. С 31 июля 2017 г. изменение размера кластера будет недоступно после даты прекращения сопровождения. 

> [!NOTE]
> Кластеры HDInsight на основе Windows (включая версии 2.1, 3.0, 3.1, 3.2 и 3.3) работают на под управлением семейства версий 4 гостевой ОС Azure, которая использует 64-разрядную версию Windows Server 2012 R2 и поддерживает .NET Framework 4.0, 4.5, 4.5.1. и 4.5.2.
>
>

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Заметки о выпуске Hortonworks, связанные с версиями HDInsight
* Кластер HDInsight версии 3.6 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html). 
* Кластер HDInsight версии 3.5 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Это кластер Hadoop **по умолчанию** , который создается при использовании портала.
* Кластер HDInsight версии 3.4 использует пакет Hadoop, построенный на платформе [Нortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html). 
* Кластер HDInsight версии 3.3 использует пакет Hadoop, построенный на платформе [Нortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * Заметки о выпуске Apache Storm доступны [здесь](https://storm.apache.org/2015/11/05/storm0100-released.html).
  * Заметки о выпуске Apache Hive доступны [здесь](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843).
* Кластер HDInsight версии 3.2 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 2.2][hdp-2-2].  

  * Заметки о выпуске для отдельных компонентов Apache — [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* Кластер HDInsight версии 3.1 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 2.1.7][hdp-2-1-7]. Кластеры HDInsight 3.1, созданные до 7.11.2014, основаны на платформе [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* Кластер HDInsight версии 3.0 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 2.0][hdp-2-0-8].
* Кластер HDInsight версии 2.1 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 1.3][hdp-1-3-0].
* Кластер HDInsight версии 1.6 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 1.1][hdp-1-1-0].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight "Стандартный" и HDInsight "Премиум"

Azure HDInsight предлагает облачные решения для работы с большими данными в двух категориях: HDInsight **Стандартный** и **Премиум**. В таблице внизу раздела перечислены компоненты, доступные **только в рамках категории "Премиум"**. Компоненты, не указанные в этой таблице явно, доступны в рамках категории "Стандартный".

> [!NOTE]
> Предложение HDInsight "Премиум" сейчас находится в состоянии предварительной версии и доступно только для кластеров Linux.
>
>

| Компонент HDInsight "Премиум" | Описание |
| --- | --- |
| Присоединенные к домену кластеры HDInsight |Присоединение кластеров HDInsight к доменам Active Directory Azure (AAD) обеспечивает безопасность корпоративного уровня. Можно настроить список сотрудников организации, которые могут пройти проверку подлинности Azure Active Directory для входа в кластер HDInsight. Корпоративный администратор может также настроить управление доступом на основе ролей для защиты Hive с помощью [Apache Ranger](http://hortonworks.com/apache/ranger/) и ограничить доступ к данным до требуемого уровня. Наконец, администратор может провести аудит доступа к данным по сотрудникам, а также аудит любых изменений в политиках контроля доступа, обеспечивая высокий уровень контроля над корпоративными ресурсами. Дополнительные сведения см. в статье [Configure domain-joined HDInsight clusters](hdinsight-domain-joined-configure.md) (Настройка присоединенных к домену кластеров HDInsight). |

### <a name="cluster-types-supported-for-hdinsight-premium"></a>Типы кластеров, поддерживаемые HDInsight Premium
В следующей таблице перечислены типы кластеров HDInsight и приведена матрица поддержки HDInsight "Премиум".

| Тип кластера | Стандартная | Premium (предварительная версия) |
| --- | --- | --- |
| Hadoop |Да |Да (только HDInsight 3.5 и 3.6) |
| Spark |Да |Нет |
| HBase |Да |Нет |
| Storm |Да |Нет |
| R Server  |Да |Нет |
| Interactive Hive (предварительная версия) |Да |Нет |
| Kafka (предварительная версия)|Да|Нет| 

Эта таблица обновляется по мере включения дополнительных типов кластеров в HDInsight уровня "Премиум".

### <a name="features-not-supported-for-hdinsight-premium"></a>Функции, не поддерживаемые в HDInsight Premium

Для кластеров HDInsight Premium в настоящее время не поддерживаются следующие функции.

* **Отсутствует поддержка Azure Data Lake Store в качестве основного хранилища**. Azure Data Lake Store по-прежнему можно использовать в качестве дополнительного хранилища в кластерах HDInsight Premium.

### <a name="pricing-and-sla"></a>Цены и соглашение об уровне обслуживания
Сведения о ценах и соглашение об уровне обслуживания для категории HDInsight "Премиум" см. на странице [с ценами на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


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

