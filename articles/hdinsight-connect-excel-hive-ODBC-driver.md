<properties linkid="manage-services-hdinsight-connect-excel-with-hive-ODBC" urlDisplayName="Подключение Excel к HDInsight" pageTitle="Подключение Excel к HDInsight с помощью драйвера Hive ODBC | Azure" metaKeywords="" description="Настройка и использование драйвера Microsoft Hive ODBC для Excel, чтобы запросить данные в кластере HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />



#Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC


Одной из ключевых особенностей решения Майкрософт для работы с данными большого размера является полная интеграция компонентов бизнес-аналитики Майкрософт с кластерами Apache Hadoop, которые были развернуты с помощью Azure HDInsight. Примером такой интеграции является возможность подключения Excel к хранилищу данных Hive на кластере HDInsight Hadoop с помощью драйвера Microsoft Hive ODBC. 

Можно также подключить данные, связанные с кластером HDInsight, и другими источниками данных, включая другие кластеры Hadoop (не HDInsight) из Excel с использованием надстройки Microsoft Power Query для Excel. Сведения об установке и использовании Power Query см. в разделе [Подключение Excel к HDInsight с использованием Power Query][hdinsight-power-query]

**Предварительные требования**:

Перед началом работы с этой статьей необходимо иметь следующее:

- Кластер HDInsight. Для настройки кластера ознакомьтесь с разделом [Приступая к работе с Azure HDInsight][hdinsight-get-started].
- Компьютер под управлением Windows 8, Windows 7, Windows Server 2012 или Windows Server 2008 R2.
- Office 2013 профессиональный плюс, Office 365 профессиональный плюс, Excel 2013 автономный или Office 2010 профессиональный плюс.

##В этой статье

1. [Установка драйвера Microsoft Hive ODBC](#InstallHiveODBCDriver)
2. [Создание источника данных Hive ODBC](#CreateHiveODBCDataSource)
3. [Импорт данных в Excel из кластера HDInsight](#ImportData)
4. [Дальнейшие действия](#nextsteps)

##<a id="InstallHiveODBCDriver"></a>Установка драйвера Microsoft Hive ODBC

Загрузите и установите драйвер Microsoft Hive ODBC из [Центра загрузок][hive-odbc-driver-download]. 

Этот драйвер может быть установлен на 32-разрядной или 64-разрядной версии Windows 7, Windows 8, Windows Server 2008 R2 и Windows Server 2012. Он обеспечит подключение к Azure HDInsight (версии 1.6 и выше) и эмулятору Azure HDInsight (версии 1.0.0.0 и выше). Необходимо установить версию, которая соответствует версии приложения, где будет использоваться драйвер ODBC. В этом учебнике драйвер будет использоваться в Office Excel. 

##<a id="CreateHiveODBCDataSource"></a>Создание источника данных Hive ODBC

Ниже показано, как создать источник данных Hive ODBC.

1. В Windows 8 нажмите клавишу Windows, чтобы открыть начальный экран, затем введите **источники данных**.
2. Нажмите кнопку **Настройка источников данных ODBC (32-разр)** или **Настройка источников данных ODBC (64-разр)** в зависимости от используемой версии Office. Если вы используете Windows 7, выберите **Источники данных ODBC (32-разр.)** или **Источники данных ODBC (64-разр.)** в разделе **Администрирование**. Это приведет к запуску диалогового окна **Администратор источника данных ODBC**. 
 
	![Администратор источника данных ODBC][img-hdi-simbahiveodbc-datasource-admin]

3. От DNS пользователя щелкните **Добавить**, чтобы открыть мастер **Создать новый источник данных**. 
4. Выберите **Драйвер Microsoft Hive ODBC** и нажмите кнопку **Готово**. Это приведет к открытию диалогового окна **Настройка DNS драйвера Microsoft Hive ODBC**. 

5. Введите или выберите следующие значения:

	<table border="1">
	<tr><td><strong>Описание</strong></td><td><strong>Свойство</strong></td></tr>
	<tr><td>Имя источника данных</td><td>Укажите имя для своего источника данных</td></tr>
	<tr><td>Узел</td><td>Введите <HDInsightClusterName>.azurehdinsight.net. Например, myHDICluster.azurehdinsight.net</td></tr>
	<tr><td>Порт</td><td>Используйте <strong>443</strong> (Этот порт был изменен с 563 на 443.)</td></tr>
	<tr><td>База данных</td><td>Используйте <strong>По умолчанию</strong>.</td></tr>
	<tr><td>Тип сервера Hive</td><td>Выберите <strong>Сервер Hive 2</strong></td></tr>
	<tr><td>Механизм</td><td>Выберите <strong>Служба Azure HDInsight</strong></td></tr>
	<tr><td>Путь HTTP</td><td>Оставьте пустым.</td></tr>
	<tr><td>Имя пользователя</td><td>Введите имя пользователя кластера HDInsight. Это имя пользователя, созданное во время подготовки кластера. При использовании возможности быстрого создания именем пользователя по умолчанию является <strong>admin</strong>.</td></tr>
	<tr><td>Пароль</td><td>Введите пароль пользователя кластера HDInsight.</td></tr>
	</table>

	Существует несколько важных параметров, о которых следует помнить при выборе пункта **Расширенные параметры**.

	<table border="1">
	<tr><td>Использовать исходный запрос</td><td>При выборе этого параметра драйвер ODBC НЕ будет пытаться преобразовать TSQL в HiveQL. Следует использовать только при 100% уверенности в отправке действительных инструкций HiveQL. При подключении к серверу SQL Server или базе данных Azure SQL необходимо оставить этот параметр невыделенным.</td></tr>
	<tr><td>Строки, загружаемые для каждого блока</td><td>При получении большого объема записей включение этого параметра может обеспечить оптимальную производительность.</td></tr>
	<tr><td>Длина столбца строки по умолчанию, <br/>
			Длина столбца двоичного кода,  <br/>
			Масштаб столбца десятичных значений</td><td>Длина и точность типа данных может повлиять на способ выведения данных. Это приведет к возврату недопустимой информации вследствие потери точности или усечения.</td></tr>
	</table>

	![Дополнительные параметры][img-HiveOdbc-DataSource-AdvancedOptions]

6. Нажмите **Тест** для проверки источника данных. При правильной настройке источника данных здесь отображается *ТЕСТЫ УСПЕШНО ЗАВЕРШЕНЫ!*
7. Нажмите **ОК**, чтобы закрыть диалоговое окно тестирования. Новый источник данных не должен быть указан в **администраторе источника данных ODBC**. 
8. Нажмите **ОК**, чтобы завершить работу мастера.
	
##<a id="ImportData"></a>Импорт данных в Excel из кластера HDInsight

Ниже описан способ импорта данных из таблицы hive в рабочую книгу Excel с помощью источника данных ODBC, созданного ранее.

1. Откройте новую или существующую рабочую книгу в Excel.
2. На вкладке **Данные** щелкните плитку **Получить внешние данные**, щелкните **Из других источников данных**, затем щелкните **Из мастера подключения к данным** для запуска **мастера подключения к данным**.

	![Мастер подключения к данным][img-hdi-simbahiveodbc.excel.dataconnection]

3. Выберите **ODBC DSN** в качестве источника данных, затем нажмите **Далее**.
4. В источниках данных ODBC выберите имя источника данных, которое было создано на предыдущем шаге, затем нажмите **Далее**.
5. Повторно введите в мастере пароль для кластера, затем щелкните **Тест** для проверки конфигурации
6. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно тестирования.
7. Нажмите кнопку **ОК**. Подождите открытие диалогового окна **Выбор базы данных и таблицы**. Это может занять несколько секунд.
8. Выберите таблицу, которую следует импортировать, и нажмите кнопку **Далее**. *hivesampletable* является примером таблицы hive, которая содержится в кластерах HDInsight.  Можно выбрать ее, если такая таблица еще не была создана. Дополнительные сведения о выполнении запросов Hive и создании таблиц Hive см. в разделе [Использование Hive вместе с HDInsight][hdinsight-hive].
8. Нажмите **Готово**.
9. В диалоговом окне **Импорт данных** можно изменить или указать запрос. Для этого щелкните **Свойства**. Это может занять несколько секунд.
10. Щелкните вкладку **Определение**, затем добавьте **LIMIT 200** к инструкции Select в текстовом поле **Текст команды**. Это изменение приведет к ограничению возвращаемого набора записей 200 шт.

	![Свойства подключения][img-hdi-simbahiveodbc-excel-connectionproperties]

11. Нажмите **ОК**, чтобы закрыть диалоговое окно "Свойства подключения".
12. Нажмите **ОК**, чтобы закрыть диалоговое окно **Импорт данных**.  
13. Повторно введите пароль, затем нажмите кнопку **ОК**. Пройдет несколько секунд, прежде чем данные будут импортированы в Excel.

##<a id="nextsteps"></a>Дальнейшие действия

В этой статье было рассмотрено, как использовать драйвер Microsoft Hive ODBC для получения данных из службы HDInsight в Excel. Аналогичным образом можно получать данные из службы HDInsight в базу данных SQL. Можно также передавать данные в службу HDInsight. Дополнительные сведения см. на следующих ресурсах:

- [Анализ данных о задержке рейсов с помощью HDInsight][hdinsight-analyze-flight-delay-data]
- [Передача данных в HDInsight][hdinsight-upload-data]
- [Использование Sqoop вместе с HDInsight][hdinsight-sqoop]


[hdinsight-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-analyze-flight-delay-data]: /ru-ru/documentation/articles/hdinsight-analyze-flight-delay-data/
[hdinsight-hive]: /ru-ru/documentation/articles/hdinsight-use-hive/
[hdinsight-upload-data]: /ru-ru/documentation/articles/hdinsight-upload-data/
[hdinsight-power-query]: /ru-ru/documentation/articles/hdinsight-connect-excel-power-query/
[hdinsight-get-started]: /ru-ru/documentation/articles/hdinsight-get-started/

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png 
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png 
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png 
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png 

