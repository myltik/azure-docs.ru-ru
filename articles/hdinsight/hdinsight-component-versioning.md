---
title: Компоненты и версии Hadoop в Azure HDInsight | Документация Майкрософт
description: Сведения о компонентах и версиях Hadoop в HDInsight, а также об уровнях обслуживания, доступных в этой облачной реализации платформы данных HortonWorks Data Platform.
keywords: версии hadoop, компоненты экосистемы hadoop, компоненты hadoop, проверка версии hadoop
services: hdinsight
editor: cgronlun
manager: asadk
author: kkampf
tags: azure-portal
documentationcenter: ''
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: kakampf
ms.openlocfilehash: 3d98ed2be7a040ad889d1d504f303569c265360b
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271597"
---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>Что представляют собой компоненты и версии Hadoop, доступные в HDInsight?

Узнайте о компонентах и версиях экосистемы Apache Hadoop в Microsoft Azure HDInsight, а также о пакете безопасности предприятия. Кроме того, узнайте, как проверить версии компонентов Hadoop в HDInsight. 

Каждая версия HDInsight является облачной реализацией версии платформы данных Hortonworks Data Platform (HDP).

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Компоненты Hadoop, доступные в разных версиях HDInsight
Azure HDInsight поддерживает несколько версий кластера Hadoop, которые могут быть развернуты в любое время. Каждая из версий создает конкретную версию платформы HDP и набор компонентов, содержащихся в этой версии. Начиная с 17 февраля 2017 г. Azure HDInsight по умолчанию использует версию кластера 3.5 на основе HDP 2.5.

Версии компонентов, связанные с версиями кластера HDInsight, указаны в указанной ниже таблице. 

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
| Apache Spark |2.1.0, 2.2.0 |1.6.2, 2.0 |1.6.0 |1.5.2 |1.3.1 (только для Windows) |-|-|
| Apache Livy |0,4 |0,3 |0,3 |0,2 |-|-|-|
| Apache Kafka | 0.10.1 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.6.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
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
В приведенных ниже таблицах перечислены версии HDInsight. Версии HDP, соответствующие каждой версии HDInsight, отображаются вместе с датой выпуска продукта. Даты прекращения поддержки и прекращения использования также предоставляются, когда они известны.

### <a name="available-versions"></a>Доступные версии

В этой таблице перечислены версии HDInsight, доступные на портале Azure, а также другие методы развертывания, например PowerShell и .NET SDK.

| Версия HDInsight | Версия HDP | ОС виртуальной машины | Дата выпуска | Дата прекращения поддержки | Дата вывода | высокую доступность; |  Доступность на портале Azure | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16.0.4 LTS |4 апреля 2017 г. | | |Yes |Yes |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30 сентября 2016 г. |5 сентября 2017 г. |28 июня 2018 года |Yes |Yes |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2 декабря 2015 г. |27 июня 2016 г. |31 июля 2018 г. |Yes |Нет  |

> [!NOTE]
> По истечении срока поддержки для версии она может стать недоступной на портале Microsoft Azure. Однако доступ к версиям кластера по-прежнему можно будет получить с помощью параметра `Version` в командлете Windows PowerShell [New-AzureHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) и пакета SDK для .NET до даты вывода.
>

### <a name="retired-versions"></a>Устаревшие версии

В приведенной ниже таблице перечислены версии HDInsight, которые **не** предоставляются на портале Azure.

| Версия HDInsight | Версия HDP | ОС виртуальной машины | Дата выпуска | Дата прекращения поддержки | Дата вывода | высокую доступность; |  Доступность на портале Azure | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 марта 2016 г. |29 декабря 2016 г. |9 января 2018 г. |Yes |Нет  |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 декабря 2015 г. |27 июня 2016 г. |31 июля 2017 г. |Yes |Нет  |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS или Windows Server 2012 R2 |18 февраля 2015 г. |1 марта 2016 г. |1 апреля 2017 г. |Yes |Нет  |
| HDInsight 3.1 |HDP 2,1 |Windows Server 2012 R2 |24 июня 2014 г. |18 мая 2015 г. |30 июня 2016 г. |Yes |Нет  |
| HDInsight 3.0 |HDP 2,0 |Windows Server 2012 R2 |11 февраля 2014 г. |17 сентября 2014 г. |30 июня 2015 г. |Yes |Нет  |
| HDInsight 2.1 |HDP 1,3 |Windows Server 2012 R2 |28 октября 2013 г. |12 мая 2014 г. |31 мая 2015 г. |Yes |Нет  |
| HDInsight 1.6 |HDP 1.1 | |28 октября 2013 г. |26 апреля 2014 г. |31 мая 2015 г. |Нет  |Нет  |

> [!NOTE]
> Высокодоступные кластеры с двумя головными узлами развертываются по умолчанию для HDInsight версии 2.1 и более поздних. Они недоступны для кластеров HDInsight версии 1.6.

## <a name="enterprise-security-package-for-hdinsight"></a>Пакет безопасности предприятия для HDInsight

Корпоративный пакет безопасности — это дополнительный пакет, который можно добавить в кластер HDInsight во время рабочего процесса создания кластера. Пакет безопасности предприятия поддерживает следующие возможности:

- Интеграция с Active Directory для аутентификации.

    Раньше можно было создавать кластеры HDInsight только с правами локального администратора и локального пользователя SSH. Локальный администратор может получить доступ ко всем файлам, папкам, таблицам и столбцам.  Пакет безопасности предприятия позволяет включить управление доступом на основе ролей. Для этого кластеры HDInsight интегрируются с вашей собственной службой Active Directory, которая включает локальную версию Active Directory, доменные службы Azure AD или Active Directory на виртуальной машине IaaS. Администратор домена в кластере может предоставить пользователям доступ к кластеру с помощью их собственных корпоративных (принадлежащих домену) имен пользователя и паролей. 

    Дополнительные сведения можно найти в разделе 

    - [An introduction to Hadoop security with domain-joined HDInsight clusters](./domain-joined/apache-domain-joined-introduction.md) (Основные сведения о безопасности Hadoop в присоединенных к домену кластерах HDInsight)
    - [Plan Azure domain-joined Hadoop clusters in HDInsight](./domain-joined/apache-domain-joined-architecture.md) (Планирование архитектуры присоединенных к домену кластеров Hadoop в Azure HDInsight)
    - [Configure domain-joined sandbox environment](./domain-joined/apache-domain-joined-configure.md) (Настройка среды с присоединенной к домену песочницей)
    - [Настройка присоединенных к домену кластеров HDInsight с помощью доменных служб Azure Active Directory](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Авторизация данных.

    - Интеграция с Apache Ranger для авторизации очередей Hive, Spark SQL и Yarn.
    - Можно задать управление доступом к файлам и папкам.

    Дополнительные сведения можно найти в разделе 

    - [Настройка политик Hive в присоединенном к домену кластере HDInsight (предварительная версия)](./domain-joined/apache-domain-joined-run-hive.md)

- Просмотр журналов аудита для отслеживания доступа и настроенных политик. 

### <a name="supported-cluster-types"></a>Поддерживаемые типы кластеров

Сейчас только следующие типы кластеров поддерживают пакет безопасности предприятия:

- Hadoop (только HDInsight 3.6);
- Spark
- Interactive Query

### <a name="support-for-azure-data-lake-store"></a>Поддержка хранилища озера данных Azure

Пакет безопасности предприятия поддерживает использование Azure Data Lake Store в качестве основного и дополнительного хранилища.

### <a name="pricing-and-sla"></a>Цены и соглашение об уровне обслуживания
Сведения о ценах и соглашение об уровне обслуживания для пакета безопасности предприятия см. на странице [Цены на Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="hdinsight-windows-retirement"></a>Дата вывода HDInsight под управлением Windows
Microsoft Azure HDInsight версии 3.3 был последней версией HDInsight под управлением Windows. Дата вывода HDInsight под управлением Windows — 31 июля 2018 г. Если у вас есть какие-либо кластеры HDInsight под управлением Windows версии 3.3 или более ранней, их необходимо перенести в HDInsight в Linux (HDInsight версии 3.5 или более поздней) до 31 июля 2018 г. Переход на ОС Linux позволяет сохранить возможность создавать или изменять размер кластеров HDInsight. Срок действия поддержки для HDInsight версии 3.3 в Windows истек 27 июня 2016 г.

Начиная с HDInsight версии 3.4 корпорация Майкрософт выпускает HDInsight только для ОС Linux. В результате некоторые компоненты в HDInsight доступны только для Linux. Сюда входят Apache Ranger, Kafka, Interactive Query, Spark, приложения HDInsight и Azure Data Lake Store в качестве первичной файловой системы. Будущие выпуски HDInsight доступны только в ОС Linux. Будущих выпусков HDInsight под управлением Windows не будет. 

## <a name="faqs"></a>Часто задаваемые вопросы

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>Что такое временная шкала прекращения использования HDInsight в Windows?
Дата вывода HDInsight под управлением Windows — 31 июля 2018 г. Если планируемая дата прекращения использования отличается для вашего региона, вы получите отдельное уведомление. 

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
Зависимость кластера Linux можно разрешить с помощью [проекта Mono](http://www.mono-project.com/). Эта реализация .NET с открытым кодом доступна для кластеров HDInsight под управлением Linux. Дополнительные сведения о версиях Azure HDInsight см. в статье [Миграция из кластера HDInsight под управлением Windows на кластер под управлением Linux](hdinsight-migrate-from-windows-to-linux.md). 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>Я новый клиент HDInsight под управлением Windows. Как создать кластер HDInsight под управлением Windows?
Начиная с 3 июля 2017 г. только имеющиеся клиенты HDInsight под управлением Windows могут создавать кластеры HDInsight. Новые клиенты не могут создавать кластер HDInsight под управлением Windows на портале Azure с помощью PowerShell или пакета SDK. Рекомендуется, чтобы новые клиенты создавали кластер HDInsight под управлением Linux. Имеющиеся клиенты могут создавать кластеры HDInsight под управлением Windows до наступления его даты вывода. 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>Влияет ли на цену переход с HDInsight под управлением Windows на HDInsight в Linux?
Нет, цены одинаковы для HDInsight в любой операционной системе. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>Каковы преимущества для клиента в связи с использованием только HDInsight под управлением Linux?
* Ускоренный выход на рынок для технологий обработки больших данных с открытым кодом через службу HDInsight.
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

## <a name="next-steps"></a>Дополнительная информация
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
