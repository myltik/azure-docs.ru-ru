<properties urlDisplayName="Connect Excel to HDInsight" pageTitle="Подключение Excel к Hadoop с помощью драйвера Hive ODBC | Azure" metaKeywords="" description="Узнайте, как установить и использовать драйвер Microsoft Hive ODBC для Excel, чтобы запрашивать данные из кластера HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Connect Excel to Hadoop with the Microsoft Hive ODBC Driver" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />



#Подключение Excel к Hadoop с помощью драйвера Microsoft Hive ODBC


Одной из ключевых особенностей решения Майкрософт для работы с данными большого размера является полная интеграция компонентов бизнес-аналитики Майкрософт с кластерами Apache Hadoop, которые были развернуты с помощью Azure HDInsight. Примером такой интеграции является возможность подключения Excel к хранилищу данных Hive на кластере Hadoop в HDInsight с помощью драйвера Microsoft Hive ODBC. 

Можно также подключить данные, связанные с кластером HDInsight, и другими источниками данных, включая другие кластеры Hadoop (не HDInsight) из Excel с использованием надстройки Microsoft Power Query для Excel. Дополнительную информацию об установке и использовании Power Query см. в статье [Подключение Excel к HDInsight с использованием Power Query][hdinsight-power-query].

**Предварительные требования**

Перед началом работы с этой статьей необходимо иметь следующее:

- Кластер HDInsight. Информацию о настройке кластера см. в статье [Приступая к работе с Azure HDInsight][hdinsight-get-started].
- Компьютер под управлением Windows 8, Windows 7, Windows Server 2012 или Windows Server 2008 R2.
- Office 2013 профессиональный плюс, Office 365 профессиональный плюс, Excel 2013 автономный или Office 2010 профессиональный плюс.

##Содержание

1. [Установка драйвера Microsoft Hive ODBC](#InstallHiveODBCDriver)
2. [Создание источника данных Hive ODBC](#CreateHiveODBCDataSource)
3. [Импорт данных в Excel из кластера HDInsight](#ImportData)
4. [Дальнейшие действия](#nextsteps)

##<a id="InstallHiveODBCDriver"></a>Установка драйвера Microsoft Hive ODBC

Скачайте и установите драйвер Microsoft Hive ODBC из [Центра загрузки][hive-odbc-driver-download]. 

Этот драйвер может быть установлен на 32-разрядной или 64-разрядной версии Windows 7, Windows 8, Windows Server 2008 R2 и Windows Server 2012. Он обеспечит подключение к Azure HDInsight (версии 1.6 и выше) и эмулятору Azure HDInsight (версии 1.0.0.0 и выше). Необходимо установить версию, которая соответствует версии приложения, где будет использоваться драйвер ODBC. В этом учебнике драйвер будет использоваться в Office Excel. 

##<a id="CreateHiveODBCDataSource"></a>Создание источника данных Hive ODBC

Ниже показано, как создать источник данных Hive ODBC.

1. В ОС Windows 8 нажмите клавишу Windows, чтобы открыть начальный экран, а затем введите **источники данных**.
2. Нажмите кнопку **Настройка источников данных ODBC (32-разр.)** или **Настройка источников данных ODBC (64-разр.)** в зависимости от версии Office. Если вы используете ОС Windows 7, выберите **Источники данных ODBC (32-разр.)** или **Источники данных ODBC (32-разр.)** в разделе **Администрирование**. Это приведет к запуску диалогового окна **Администратор источников данных ODBC**. 
 
	![OBDC data source administrator][img-hdi-simbahiveodbc-datasource-admin]

3. В DNS пользователя щелкните **Добавить**, чтобы открыть мастер **Создание нового источника данных**. 
4. Выберите **Драйвер Microsoft Hive ODBC** и нажвите кнопку **Готово**. Это приведет к открытию диалогового окна **Настройка DNS драйвера Microsoft Hive ODBC**. 

5. Введите или выберите следующие значения:

	<table border="1">
	<tr><td><strong>Свойство</strong></td><td><strong>Описание</strong></td></tr>
	<tr><td>Имя источника данных</td><td>Присвойте имя источнику данных</td></tr>
	<tr><td>Узел</td><td>Введите <HDInsightClusterName>.azurehdinsight.net. Например, myHDICluster.azurehdinsight.net</td></tr>
	<tr><td>Порт</td><td>Используйте порт <strong>443</strong>. (Этот порт был изменен с 563 на 443.)</td></tr>
	<tr><td>База данных</td><td>Используйте <strong>значение по умолчанию</strong>.</td></tr>
	<tr><td>Тип сервера Hive</td><td>Выберите <strong>Hive Server 2</strong></td></tr>
	<tr><td>Механизм</td><td>Выберите <strong>Служба Azure HDInsight</strong></td></tr>
	<tr><td>Путь HTTP</td><td>Оставьте пустым.</td></tr>
	<tr><td>Имя пользователя</td><td>Введите имя пользователя кластера HDInsight. Это имя пользователя, созданное во время подготовки кластера. При использовании параметра "Быстрое создание" именем пользователя по умолчанию является <strong>admin</strong>.</td></tr>
	<tr><td>Пароль</td><td>Введите пароль пользователя кластера HDInsight.</td></tr>
	</table>

	Существует несколько важных параметров, о которых вам следует помнить при выборе пункта **Расширенные параметры**.

	<table border="1">
	<tr><td>Использовать исходный запрос</td><td>При выборе этого параметра драйвер ODBC НЕ будет пытаться преобразовать TSQL в HiveQL. Следует использовать только при 100% уверенности в отправке действительных инструкций HiveQL. При подключении к серверу SQL Server или базе данных Azure SQL необходимо снять этот флажок.</td></tr>
	<tr><td>Строки, загружаемые для каждого блока</td><td>При получении большого объема записей включение этого параметра может обеспечить оптимальную производительность.</td></tr>
	<tr><td>Длина столбца строки по умолчанию, <br/>
			Длина столбца двоичного кода,  <br/>
			Масштаб столбца десятичных значений</td><td>Длина и точность типа данных может повлиять на способ выведения данных. Это приведет к возврату недопустимой информации вследствие потери точности и (или) усечения.</td></tr>
	</table>

	![Advanced options][img-HiveOdbc-DataSource-AdvancedOptions]

6. Щелкните **Тест**, чтобы проверить источник данных. Если источник данных настроен правильно, отображается надпись "ТЕСТ УСПЕШНО ЗАВЕРШЕН!".
7. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно тестирования. Теперь новый источник данных должен быть указан в **администраторе источников данных ODBC**. 
8. Нажмите кнопку **ОК**, чтобы завершить работу мастера.
	
##<a id="ImportData"></a>Импорт данных в Excel из кластера HDInsight

Ниже описан способ импорта данных из таблицы hive в рабочую книгу Excel с помощью источника данных ODBC, созданного ранее.

1. Откройте новую или существующую рабочую книгу в Excel.
2. На вкладке **Данные** щелкните плитку **Получить внешние данные**, а затем последовательно выберите **Из других источников данных** и **Из мастера подключения данных**, чтобы запустить **мастер подключения данных**.

	![Open data connection wizard][img-hdi-simbahiveodbc.excel.dataconnection]

3. Выберите **ODBC DSN** в качестве источника данных и нажмите кнопку **Далее**.
4. В источниках данных ODBC выберите имя источника данных, которое было создано на предыдущем шаге, а затем нажмите кнопку **Далее**.
5. Повторно введите в мастере пароль для кластера, а затем щелкните **Тест**, чтобы проверить конфигурацию.
6. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно тестирования.
7. Нажмите кнопку **ОК**. Подождите, пока откроется диалоговое окно **Выбор базы данных и таблицы**. Это может занять несколько секунд.
8. Выберите таблицу, которую следует импортировать, и нажмите кнопку **Далее**. hivesampletable - это пример таблицы hive, которая содержится в кластерах HDInsight.  Можно выбрать ее, если такая таблица еще не была создана. Дополнительную информацию о выполнении запросов Hive и создании таблиц Hive см. в статье [Использование Hive с HDInsight][hdinsight-use-hive].
8. Нажмите кнопку **Готово**.
9. В диалоговом окне **Импорт данных** вы можете изменить или указать запрос. Для этого щелкните **Свойства**. Это может занять несколько секунд.
10. Выберите вкладку **Определение**, а затем в текстовом поле **Текст команды** добавьте к инструкции Select **LIMIT 200**. Это изменение приведет к ограничению возвращаемого набора записей 200 шт.

	![Connection Properties][img-hdi-simbahiveodbc-excel-connectionproperties]

11. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно "Свойства подключения".
12. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Импорт данных**.  
13. Повторно введите пароль и нажмите кнопку **ОК**. Пройдет несколько секунд, прежде чем данные будут импортированы в Excel.

##<a id="nextsteps"></a>Дальнейшие действия

В этой статье было рассмотрено, как использовать драйвер Microsoft Hive ODBC для получения данных из службы HDInsight в Excel. Аналогичным образом можно получать данные из службы HDInsight в базу данных SQL. Можно также передавать данные в службу HDInsight. Дополнительные сведения см. на следующих ресурсах:

- [Aнализ данных о задержке рейсов с помощью HDInsight][hdinsight-analyze-flight-data]
- [Отправка данных в HDInsight][hdinsight-upload-data]
- [Использование Sqoop с HDInsight][hdinsight-use-sqoop]


[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-get-started]: ../hdinsight-get-started/

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png 
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png 
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png 
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png 

<!--HONumber=35.1-->
