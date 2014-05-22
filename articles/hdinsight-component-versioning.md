<properties linkid="manage-services-hdinsight-version" urlDisplayName="Версия Hadoop HDInsight" pageTitle="Новые возможности в версиях кластеров, предоставляемых в HDInsight | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop Azure" description="HDInsight поддерживает несколько версий кластера Hadoop, развертываемых в любое время. Показаны поддерживаемые версии платформы данных HortonWorks Data Platform (HDP) и Hadoop." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Новые возможности в версиях кластеров, предоставляемых в HDInsight" authors="bradsev" />


#Новые возможности в версиях кластеров, предоставляемых в HDInsight

Azure HDInsight теперь поддерживает Hadoop 2.2 с кластером HDinsight версии 3.0, используя все возможности этой платформы для предоставления клиентам ряда существенных преимуществ. К ним относятся в первую очередь следующие:

- Hive: на порядок улучшены время ответа на запрос Hive (до 40 раз) и степень сжатия данных (до 80 %) благодаря использованию формата Optimized Row Columnar (ORC).

- YARN: новая универсальная распределенная платформа управления приложениями, которая заменила классическую платформу Apache Hadoop MapReduce для обработки данных в кластерах Hadoop. Эффективно выступает в качестве операционной системы Hadoop и преобразует однозадачную платформу работы с данными с пакетной обработкой в многозадачную платформу, которая поддерживает пакетную интерактивную потоковую обработку и обработку в режиме реального времени. Эта новая платформа управления повышает масштабируемость и эффективность использования кластера в соответствии с такими критериями, как гарантии по мощности, достоверность и соглашения об уровнях обслуживания.

- Pig, Sqoop, Qozie, Ambari: обновления версий компонентов для кластера HDinsight версии 3.0 (HDP 2.0), которые обеспечивают согласование с кластером HDinsight версии 2.1 (HDP 1.3). Подробные сведения см. в таблицах версий ниже. Обратите внимание, что Hbase, Mahout и Flume не включены.

**Развертывание**	
Создание кластеров HDInsight 3.0 на Hadoop 2.2 поддерживается с помощью портала Azure, пакета SDK HDinsight и Azure PowerShell.

**Глобальная доступность**		
С выпуском HDInsight Azure на Hadoop 2.2 корпорация Майкрософт сделала HDInsight доступным во всех основных географиях Azure, за исключением Китая и подчиненных территорий. В частности, подключены центры обработки данных в Западной Европе и Юго-Восточной Азии. Благодаря этому клиенты могут подобрать кластеры в центре обработки данных, который расположен неподалеку и, возможно, находится в зоне со схожими требованиями по соблюдению законодательства. 

**Критические изменения**	
В кластерах HDInsight 3.0 поддерживается только синтаксис "wasb: / /". Прежний синтаксис "asv: / /" поддерживается в кластерах HDInsight 2.1 и 1.6, однако не поддерживается в кластерах HDInsight 3.0 и не будет поддерживаться в последующих версиях. Это означает, что все задания, отправляемые в кластер HDInsight 3.0, в которых явно используется синтаксис "asv://", завершатся сбоем. Вместо этого следует использовать синтаксис "wasb://". Кроме того, сбоем будут завершатся задания, отправляемые в кластеры HDInsight 3.0, которые созданы с существующим метахранилищем, содержащим ссылки на ресурсы с использованием синтаксиса "asv://". Эти метахранилища потребуется создать повторно с использованием синтаксиса wasb://, чтобы адресовать ресурсы.

##Версии HDInsight
HDInsight поддерживает несколько версий кластера Hadoop, которые могут быть развернуты в любое время. Каждая из версий содержит конкретную версию платформы HortonWorks Data Platform (HDP) и набор компонентов, содержащихся в этой версии.

###Кластер версии 3.0

Azure HDInsight теперь поддерживает Hadoop 2.2. Она основана на версии 2.0 платформы данных Hortonworks Data Platform и предоставляет службы Hadoop с версиями компонентов, которые перечислены в следующей таблице:

<table border="1">
<tr><th>Компонент</th><th>Версия</th></tr>
<tr><td>Apache Hadoop</td><td>2.2.0</td></tr>
<tr><td>Apache Hive</td><td>0.12.0</td></tr>
<tr><td>Apache Pig</td><td>0.12</td></tr>
<tr><td>Apache Sqoop</td><td>1.4.4</td></tr>
<tr><td>Apache Oozie</td><td>4.0.0</td></tr>
<tr><td>Apache HCatalog</td><td>Объединено с Hive</td></tr>
<tr><td>Apache Templeton</td><td>Объединено с Hive</td></tr>
<tr><td>Ambari</td><td>API v1.0</td></tr>
</table>

###Кластер версии 2.1

Версия кластера 2.1 используется в [Azure HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601) по умолчанию. Она основана на версии 1.3.0 платформы данных Hortonworks Data Platform и предоставляет службы Hadoop с версиями компонентов, которые перечислены в следующей таблице:

<table border="1">
<tr><th>Компонент</th><th>Версия</th></tr>
<tr><td>Apache Hadoop</td><td>1.2.0</td></tr>
<tr><td>Apache Hive</td><td>0.11.0</td></tr>
<tr><td>Apache Pig</td><td>0.11</td></tr>
<tr><td>Apache Sqoop</td><td>1.4.3</td></tr>
<tr><td>Apache Oozie</td><td>3.2.2</td></tr>
<tr><td>Apache HCatalog</td><td>Объединено с Hive</td></tr>
<tr><td>Apache Templeton</td><td>Объединено с Hive</td></tr>
<tr><td>Ambari</td><td>API v1.0</td></tr>
</table>


###Кластер версии 1.6

Также доступна версия кластера [Azure HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601) 1.6. Она основана на версии 1.1.0 платформы данных Hortonworks Data Platform и предоставляет службы Hadoop с версиями компонентов, которые описываются в следующей таблице:

<table border="1">
<tr><th>Компонент</th><th>Версия</th></tr>
<tr><td>Apache Hadoop</td><td>1.0.3</td></tr>
<tr><td>Apache Hive</td><td>0.9.0</td></tr>
<tr><td>Apache Pig</td><td>0.9.3</td></tr>
<tr><td>Apache Sqoop</td><td>1.4.2</td></tr>
<tr><td>Apache Oozie</td><td>3.2.0</td></tr>
<tr><td>Apache HCatalog</td><td>0.4.1</td></tr>
<tr><td>Apache Templeton</td><td>0.1.4</td></tr>
<tr><td>Драйвер JDBC для SQL Server</td><td>3.0</td></tr>
</table>


### Выбор версии при подготовке кластера HDInsight

При создании кластера с использованием командлетов HDInsight PowerShell или пакета SDK HDInsight .NET можно выбрать версию для кластера HDInsight Hadoop с помощью параметра "Version".

При выборе варианта **Быстрое создание** по умолчанию будет развернута версия 2.1 кластера HDInsight Hadoop. При выборе варианта **Настраиваемое создание** на портале Azure можно выбрать версию кластера для развертывания в раскрывающемся списке **Версия HDInsight** на странице **Сведения о кластере**. Кластер HDInsight Hadoop версии 3.0 доступен только как вариант в мастере **Настраиваемое создание**.

![HDI.Versioning.VersionScreen][image-hdi-versioning-versionscreen]


## Поддерживаемые версии
В следующей таблице перечислены версии HDInsight, доступные в настоящее время, соответствующие версии платформы данных Hortonworks Data Platform (HDP) и даты их выхода. Также будут указаны даты прекращения их сопровождения, если они известны.

<table border="1">
<tr><th>Версия HDInsight</th><th>Версия HDP</a></th><th>Дата выпуска</th></tr>
<tr><td>HDI 3.0</td><td>HDP 2.0</td><td>11/02/2014</td></tr>
<tr><td>HDI 2.1</td><td>HDP 1.3</td><td>28/10/2013</td></tr>
<tr><td>HDI 1.6</td><td>HDP 1.1</td><td>28/10/2013</td></tr>
</table><br/>


### Соглашение об уровнях обслуживания (SLA) для версий кластера HDInsight 
Соглашение об уровне обслуживания определяется в терминах "Окна поддержки". Под окном поддержки понимается период времени, в течение которого определенная версия кластера HDInsight поддерживается службой поддержки клиентов корпорации Майкрософт.  Кластер HDInsight находится вне окна поддержки, если дата истечения срока технической поддержки его версии уже миновала.  Список поддерживаемых версий кластера HDInsight можно найти в приведенной выше таблице.  Дата истечения срока действия технической поддержки для конкретной версии HDInsight (обозначается как версия X) рассчитывается как наиболее поздняя из следующих дат:  

- Формула 1: добавьте 180 дней к дате выхода версии кластера HDInsight X
- Формула 2: добавьте 90 дней к дате, когда версия кластера HDInsight X + 1 (следующая за версией X) стала доступной на портале управления Azure.

> [WACOM.NOTE] HDInsight 2.1 и 3.0 используют 64-разрядную версию Windows 2008 R2 с пакетом обновления 1 и платформой .NET Framework 4.0. 

**Дополнительные примечания и сведения об управлении версиями**	

* Драйвер JDBC для SQL Server используется в HDInsight и не используется для внешних операций. Если нужно подключиться к HDInsight с использованием ODBC, следует использовать драйвер ODBC для Microsoft Hive. Дополнительные сведения об использовании Hive ODBC см. в документе [Подключение Excel к HDInsight с помощью драйвера ODBC для Microsoft Hive][connect-excel-with-hive-ODBC].

* Кластер HDInsight версии 3.0 использует пакет Hadoop, основанный на [платформе данных Hortonworks Data Platform 2.0][hdp-2-0-8]

* Кластер HDInsight версии 2.1 использует пакет Hadoop, основанный на [платформе данных 1.3 Hortonworks Data Platform][hdp-1-3-0] Этот кластер Hadoop создается по умолчанию при использовании портала Azure HDInsight.

* Кластер HDInsight версии 1.6 использует пакет Hadoop, основанный на [платформе данных Hortonworks Data Platform 1.1][hdp-1-1-0] 

* Версии компонентов, связанных с версиями кластера HDInsight, могут быть изменены в будущих обновлениях для HDInsight. Один из способов определения доступных компонентов и проверки версий, которые используются для кластера — интерфейс API REST Ambari. Для извлечения сведений о компоненте службы можно воспользоваться командой GetComponentInformation. Дополнительные сведения см. в [документации по Ambari][ambari-docs]. Еще один способ получения этих сведений — вход в кластер с помощью удаленного рабочего стола и изучение содержимого папки "C:\apps\dist\".

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://www.windowsazure.com/ru-ru/support/forums/

[connect-excel-with-hive-ODBC]: /ru-ru/documentation/articles/hdinsight-connect-excel-hive-ODBC-driver

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

