<properties linkid="manage-services-hdinsight-version" urlDisplayName="HDInsight Hadoop Version" pageTitle="What's new in Hadoop cluster versions of HDInsight? | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight supports multiple Hadoop cluster versions deployable at any time. See the Hadoop and HortonWorks Data Platform (HDP) distribution versions supported." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="What's new in the cluster versions provided by HDInsight?" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Новые возможности в версиях кластеров Hadoop, предоставляемых в HDInsight

## Версии HDInsight

HDInsight поддерживает несколько версий кластера Hadoop, которые могут быть развернуты в любое время. Каждая из версий содержит конкретную версию платформы HortonWorks Data Platform (HDP) и набор компонентов, содержащихся в этой версии. Версии компонентов, связанных с версиями кластера HDInsight, описываются в следующей таблице. Обратите внимание, что [Azure HDInsight][Azure HDInsight] в настоящее время по умолчанию использует версию кластера 3.1 на основе HDP 2.1.

<table>
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Компонент</th>
<th align="left">HDInsight версия 3.1 (по умолчанию)</th>
<th align="left">HDInsight версия 3,0</th>
<th align="left">HDInsight версия 2.1</th>
<th align="left">HDInsight версия 1.6</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Платформа данных Hortonworks Data Platform (HDP)</td>
<td align="left">2.1</td>
<td align="left">2.0</td>
<td align="left">1.3</td>
<td align="left">1.1</td>
</tr>
<tr class="even">
<td align="left">Apache Hadoop &amp; YARN</td>
<td align="left">2.4.0</td>
<td align="left">2.2.0</td>
<td align="left">1.2.0</td>
<td align="left">1.0.3</td>
</tr>
<tr class="odd">
<td align="left">Tez</td>
<td align="left">0.4.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Apache Pig</td>
<td align="left">0.12.1</td>
<td align="left">0.12.0</td>
<td align="left">0.11.0</td>
<td align="left">0.9.3</td>
</tr>
<tr class="odd">
<td align="left">Apache Hive &amp; HCatalog</td>
<td align="left">0.13.0</td>
<td align="left">0.12.0</td>
<td align="left">0.11.0</td>
<td align="left">0.9.0</td>
</tr>
<tr class="even">
<td align="left">HBase</td>
<td align="left">0.98.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Apache Sqoop</td>
<td align="left">1.4.4</td>
<td align="left">1.4.4</td>
<td align="left">1.4.3</td>
<td align="left">1.4.2</td>
</tr>
<tr class="even">
<td align="left">Apache Oozie</td>
<td align="left">4.0.0</td>
<td align="left">4.0.0</td>
<td align="left">3.3.2</td>
<td align="left">3.2.0</td>
</tr>
<tr class="odd">
<td align="left">Apache HCatalog</td>
<td align="left">Объединено с Hive</td>
<td align="left">Объединено с Hive</td>
<td align="left">Объединено с Hive</td>
<td align="left">0.4.1</td>
</tr>
<tr class="even">
<td align="left">Apache Templeton</td>
<td align="left">Объединено с Hive</td>
<td align="left">Объединено с Hive</td>
<td align="left">Объединено с Hive</td>
<td align="left">0.1.4</td>
</tr>
<tr class="odd">
<td align="left">Ambari</td>
<td align="left">1.5.1</td>
<td align="left">1.4.1</td>
<td align="left">API v1.0</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Zookeeper</td>
<td align="left">3.4.5</td>
<td align="left">3.4.5</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Mahout</td>
<td align="left">0.9.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
</tbody>
</table>

**Поучение информации о текущей версии компонентов**

Версии компонентов, связанных с версиями кластера HDInsight, могут быть изменены в будущих обновлениях для HDInsight. Один из способов определения доступных компонентов и проверки версий, которые используются для кластера — интерфейс API REST Ambari. Для извлечения сведений о компоненте службы можно воспользоваться командой **GetComponentInformation**. Дополнительные сведения см. в [документации по Ambari][документации по Ambari]. Еще один способ получения этих сведений — вход в кластер с помощью удаленного рабочего стола и непосредственное изучение содержимого каталога "C:\\apps\\dist".

**Заметки о выпуске**

Дополнительные заметки о выпусках последних версий HDInsight см. в разделе [Заметки о выпуске НDInsight][Заметки о выпуске НDInsight].

### Выбор версии при подготовке кластера HDInsight

При создании кластера с использованием командлетов HDInsight PowerShell или пакета SDK HDInsight .NET можно выбрать версию для кластера HDInsight Hadoop с помощью параметра "Version".

При выборе параметра **Быстрое создание** по умолчанию будет развернута версия 3.1 кластера HDInsight Hadoop. При выборе варианта **Настраиваемое создание** на портале Azure можно выбрать версию кластера для развертывания в раскрывающемся списке **Версия HDInsight** на странице **Сведения о кластере**.

## Краткие сведения о возможностях

Azure HDInsight теперь по умолчанию поддерживает Hadoop 2.4 с кластером HDInsight версии 3.1 (при использовании платформы данных Hortonworks Data Platform 2.1). При этом используются все возможности этой платформы для предоставления клиентам ряда существенных преимуществ. К ним относятся в первую очередь следующие:

-   **HBase**: HBase— это база данных NoSQL с низкой задержкой, допускающая интерактивную обработку транзакций с большими данными. База данных НBase предлагается как управляемый кластер, интегрированный в среду Azure. Кластеры настраиваются таким образом, чтобы хранить данные напрямую в в хранилище Blob-объектов Azure. Это обеспечивает низкую задержку и повышает гибкость с точки зрения выбора производительность/ затраты. Такой подход позволяет клиентам создавать интерактивные веб-сайты, работающие с большими наборами данных, службы для хранения данных датчиков и телеметрии с миллионов конечных точек, а также анализировать эти данные с помощью заданий Hadoop.

-   **Панель мониторинга кластеров**: Это новое веб-приложение, которое развертывается в кластере HDInsight. Приложение можно использовать для выполнения запросов Hive, проверки журналов заданий и просмотра хранилища Blob-объектов Azure. Для доступа к веб-приложению используется URL-адрес: \<*ClusterName*\>.azurehdinsight.net.

-   **Библиотека Microsoft Avro**: В этой библиотеке реализуется система сериализации данных Apache Avro для среды Microsoft.NET. Apache Avro обеспечивает компактный формат обмена двоичными данными для сериализации. Библиотека использует JSON для определения не зависящей от языка схемы, которая гарантирует взаимодействие языков. Данные, сериализованные в одном языке, могут быть прочитаны в другом. В настоящее время поддерживаются C, C++, C#, Java, PHP, Python и Ruby. Формат сериализации Apache Avro широко используется в Azure HDInsight для представления сложных структур данных в заданиях Hadoop MapReduce.

-   **YARN**: Новая универсальная распределенная платформа управления приложениями, которая заменила классическую платформу Apache Hadoop MapReduce для обработки данных в кластерах Hadoop. Эффективно выступает в качестве операционной системы Hadoop и преобразует однозадачную платформу работы с данными с пакетной обработкой в многозадачную платформу, которая поддерживает пакетную интерактивную потоковую обработку и обработку в режиме реального времени. Эта новая платформа управления повышает масштабируемость и эффективность использования кластера в соответствии с такими критериями, как гарантии по мощности, достоверность и соглашения об уровнях обслуживания.

-   **Tez (только HDInsight 3.1)**: Универсальная и настраиваемая платформа, которая используется для создания упрощенных задач обработки данных, как для малых, так и для больших задач в Hadoop. Эта платформа обеспечивает возможность выполнения сложных направленных ациклических графов (DAG) задач для одного задания. Благодаря этому проекты экосистемы Apache Hadoop, такие как Apache Hive и Apache Pig, могут отвечать требованиям в отношении времени отклика при взаимодействии с человеком, а также максимально большой пропускной способности на уровне петабайт. Обратите внимание, что Hive 0.13 допускает выполнение запросов Hive поверх Tez, а не MapReduce.

-   **Высокая доступность**: К кластерам Hadoop, развернутым в HDInsight, добавлен второй головной узел для повышения доступности службы. Стандартные реализации кластеров Hadoop обыкновенно имеют один головной узел. HDInsight устраняет эту единственную точку отказа благодаря добавлению второго головного узла. Переход на новую конфигурацию кластера НA не изменяет цену кластера, если только клиенты не подготавливают кластеры с головным узлом очень большого размера, а не с размером который определяется по умолчанию.

-   **Производительность Hive**: на порядок улучшены время ответа на запрос Hive (до 40 раз) и степень сжатия данных (до 80 %) благодаря использованию формата **Optimized Row Columnar** (ORC).

-   **Pig, Sqoop, Qozie, Ambari**: Обновления версий компонентов для кластера HDInsight версии 3.0 (HDP 2.0/Hadoop 2.2), которые обеспечивают согласование с кластером HDInsight версии 2.1 (HDP 1.3/Hadoop 1.2). Подробные сведения см. в таблицах версий ниже.

-   **Mahout**: Эта библиотека масштабируемых алгоритмов машинного обучения предварительно установления в кластерах HDInsight 3.1 Hadoop. Благодаря этому можно выполнять задания Mahout без необходимости дополнительно настраивать кластер.

## Поддерживаемые версии

В следующей таблице перечислены версии HDInsight, доступные в настоящее время, соответствующие версии платформы данных Hortonworks Data Platform (HDP) и даты их выхода. Также будут указаны даты прекращения их сопровождения, если они известны. Обратите внимание на следующее:

-   Высокодоступные кластеры с двумя головными узлами развертываются по умолчанию для кластеров HDInsight 2.1, 3.0 и 3.1. Они недоступны для кластеров HDInsight 1.6.
-   При истечении срока поддержки для определенной версии, она может стать недоступной через портал управления Azure. В таблице ниже указаны версии, доступные на портале управления Аzure.Кластерные версии по-прежнему будут доступны при использовании параметра "Version" с командой PowerShell [New-AzureHDInsightCluster][New-AzureHDInsightCluster] и пакетом .NET SDK вплоть до истечения срока действия.

<table border="1">
<tr>
<th>
Версия HDInsight

</th>
<th>
Версия HDP</a>

<th>
Высокая доступность

</th>
</th>
<th>
Дата выпуска

</th>
<th>
Доступна на портале управления Azure

</th>
<th>
Дата прекращения поддержки

</th>
<th>
Дата прекращения сопровождения

</th>
</tr>
<tr>
<td>
HDI 3,1

</td>
<td>
HDP 2,1

</td>
<td>
Да

</td>
<td>
</td>
<td>
Да

</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td>
HDI 3,0

</td>
<td>
HDP 2,0

</td>
<td>
Да

</td>
<td>
02/11/2014

</td>
<td>
Да

</td>
<td>
09/17/2014

</td>
<td>
06/30/2015

</td>
</tr>
<tr>
<td>
HDI 2,1

</td>
<td>
HDP 1,3

</td>
<td>
Да

</td>
<td>
10/28/2013

</td>
<td>
Нет

</td>
<td>
05/12/2014

</td>
<td>
05/31/2015

</td>
</tr>
<tr>
<td>
HDI 1.6

</td>
<td>
HDP 1.1

</td>
<td>
Нет

</td>
<td>
10/28/2013

</td>
<td>
Нет

</td>
<td>
04/26/2014

</td>
<td>
05/31/2015

</td>
</tr>
</table>
**Развертывание кластеров не по умолчанию**

Создание кластеров HDInsight 3.1 на Hadoop 2.4 поддерживается с помощью портала Azure, пакета SDK HDinsight и Azure PowerShell. Обратите внимание, что кластеры HDInsight 3.1 создаются на Hadoop 2.4 по умолчанию. Таким образом, пользователям необходимо использовать параметр **Настраиваемое создание** на портале, чтобы задать другие версии кластеров HDInsight, если их нужно создать.

### Соглашение об уровнях обслуживания (SLA) для версий кластера HDInsight

Соглашение об уровне обслуживания определяется в терминах "Окна поддержки". Под окном поддержки понимается период времени, в течение которого определенная версия кластера HDInsight поддерживается службой поддержки клиентов корпорации Майкрософт. Кластер HDInsight находится вне окна поддержки, если **дата истечения срока технической поддержки его версии** уже миновала. Список поддерживаемых версий кластера HDInsight можно найти в приведенной выше таблице. Дата истечения срока действия технической поддержки для конкретной версии HDInsight (обозначается как версия X) рассчитывается как наиболее поздняя из следующих дат (в случае доступности более новой версии Х+1):

-   Формула 1: добавьте 180 дней к дате выхода версии Х кластера HDInsight
-   Формула 2: добавьте 90 дней к дате, когда версия кластера HDInsight X + 1 (следующая за версией X) стала доступной на портале управления Azure.

**Дата прекращения сопровождения** — это дата после которой версия кластер не может быть создана на HDInsight.

> [WACOM.NOTE] Кластеры HDInsight 2.1 и 3.0 работают на под управлением гостевой ОС Azure [Family 4][Family 4], которая использует 64-разрядную версию Windows Server 2012 R2 и поддерживает .NET Framework 4.0, 4.5. и 4.5.1.

## Заметки о выпуске Hortonworks, связанные с версиями HDInsight

-   Кластер HDInsight версии 3.1 использует пакет Hadoop, основанный на [Платформе данных Нortonworks Data Platform 2.1][Платформе данных Нortonworks Data Platform 2.1]. Этот кластер Hadoop создается по умолчанию при использовании портала Azure HDInsight.

-   Кластер HDInsight версии 3,0 использует пакет Hadoop, основанный на [Платформе данных Нortonworks Data Platform 2,0][Платформе данных Нortonworks Data Platform 2,0].

-   Кластер HDInsight версии 2,1 использует пакет Hadoop, основанный на [Платформе данных Нortonworks Data Platform 1,3][Платформе данных Нortonworks Data Platform 1,3].

-   Кластер HDInsight версии 1.6 использует пакет Hadoop, основанный на [Платформе данных Нortonworks Data Platform 1.1][Платформе данных Нortonworks Data Platform 1.1].

  [Azure HDInsight]: http://go.microsoft.com/fwlink/?LinkID=285601
  [документации по Ambari]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Заметки о выпуске НDInsight]: http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-release-notes/
  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/ru-ru/library/dn593744.aspx
  [Family 4]: http://msdn.microsoft.com/ru-ru/library/azure/ee924680.aspx#explanation
  [Платформе данных Нortonworks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [Платформе данных Нortonworks Data Platform 2,0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [Платформе данных Нortonworks Data Platform 1,3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [Платформе данных Нortonworks Data Platform 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html
