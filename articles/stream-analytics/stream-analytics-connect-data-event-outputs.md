<properties 
	pageTitle="Создание выходных данных Stream Analytics | Microsoft Azure" 
	description="Информация о подключении к местам назначения выходных данных, а также их настройке для решений Stream Analytics." 
	documentationCenter="" 
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="11/06/2015" 
	ms.author="jeffstok"/>

# Создание выходных данных Stream Analytics

## Что такое выходные данные Stream Analytics? ##
---
Создавая задание Stream Analytics, необходимо учитывать, каким образом будут использоваться его выходные данные. Как получатели преобразованных данных будут просматривать результаты задания Stream Analytics? Какие инструменты будут использоваться для анализа выходных данных? Требуется ли хранение данных?

Служба Azure Stream Analytics предоставляет семь различных вариантов хранения и просмотра выходных данных, полученных в результате выполнения заданий: база данных SQL, хранилище больших двоичных объектов, концентраторы событий, очереди служебной шины, разделы служебной шины, Power BI и табличное хранилище. Это позволяет легко просматривать выходные данные задания, гибко их применять, передавать в хранилище данных и использовать в других целях.

## Использование базы данных SQL как места назначения выходных данных ##
---
База данных SQL Azure может служить местом назначения для выходных реляционных данных, а также для выходных данных приложений, которые зависят от содержимого, размещенного в реляционной базе данных. Дополнительные сведения о базах данных SQL Azure см. в статье [Базы данных SQL Azure](http://azure.microsoft.com/services/sql-database/).

### Параметры ###

Чтобы начать работу с базой данных SQL, необходимо получить следующие сведения об этой базе данных:

1. Имя сервера
2. Имя базы данных
3. Действительное имя пользователя и соответствующий пароль.
4. Имя выходной таблицы. Такая таблица не создается заданием и должна быть создана заранее.

### Добавление базы данных SQL как места назначения выходных данных ###

![рисунок1][graphic1]

Откройте вкладку выходных данных задания и выберите команду **ДОБАВИТЬ ВЫХОДНЫЕ ДАННЫЕ**, а затем нажмите кнопку "Далее".

![рисунок2][graphic2]

Выберите в качестве места назначения выходных данных вариант **База данных SQL**.

![рисунок3][graphic3]

На следующей странице введите сведения о базе данных. Псевдоним вывода — это понятное имя, которое используется в запросах для направления выходных данных запроса в соответствующую базу данных. Если выбрано только одно место назначения выходных данных, по умолчанию используется псевдоним «Выходные данные».

![рисунок4][graphic4]

Если в подписке задания Stream Analytics существует база данных, становится активным флажок «Использовать базу данных SQL из текущей подписки». Выберите базу данных из раскрывающегося списка.

![рисунок5][graphic5]

Чтобы добавить это место назначения выходных данных, нажмите кнопку «Далее». После этого будут запущены две операции. Первая — добавление выходных данных.

![рисунок6][graphic6]

Вторая — проверка подключения. Служба Azure Stream Analytics попытается подключиться к базе данных SQL и проверить правильность введенных учетных данных и доступность таблицы.

## Использование хранилища больших двоичных объектов как места назначения для выходных данных ##
---
Общие сведения о хранилище больших двоичных объектов Azure и его использовании см. в документации по [работе с большими двоичными объектами](./articles/storage/storage-dotnet-how-to-use-blobs.md).

### Параметры ###

Чтобы начать использование выходных данных хранилища больших двоичных объектов, потребуются следующие сведения:

1. Если учетная запись хранения относится не к той подписке, в которую входит задание потоковой передачи, будут показаны также поля для ввода имени и ключа учетной записи хранения.
2. Имя контейнера.
3. Префикс имени файла.
4. Формат сериализации, используемый для потока данных (Avro, CSV, JSON).

### Добавление хранилища больших двоичных объектов как места назначения для выходных данных ###

Выберите в качестве места назначения выходных данных хранилище больших двоичных объектов.

![рисунок20][graphic20]

Укажите необходимые данные, как представлено ниже:

![рисунок21][graphic21]

## Использование концентратора событий как места назначения для выходных данных ##
---
### Общие сведения ###
 
Концентраторы событий — это высокомасштабируемые активные приемники событий, которые, как правило, используются для получения входящих событий Stream Analytics. Они хорошо справляются с обработкой большого числа событий, что делает их идеальным местом назначения для выходных данных задания. Концентратор событий может использоваться в качестве места назначения выходных данных, например в случае, когда выходные данные задания Stream Analytics становятся входными данными для другого задания потоковой передачи. Более подробные сведения о концентраторах событий см. на портале в разделе [Концентраторы событий](https://azure.microsoft.com/services/event-hubs/ "Концентраторы событий").
 
### Параметры ###

Для настройки потоков данных концентраторов событий требуется ряд параметров.

1. Пространство имен Service Bus — пространство имен Service Bus концентратора событий. Пространство имен Service Bus — это контейнер для набора сущностей обмена сообщениями. При создании нового концентратора событий также создается пространство имен служебной шины. 
2. Имя концентратора событий — имя, которым обозначается концентратор событий. Оно указывается при создании нового концентратора событий. 
3. Имя политики концентратора событий — имя политики общего доступа, используемой для доступа к концентратору событий. Политики общего доступа можно настроить в концентраторе событий на вкладке «Настройка». Каждой политике общего доступа присваивается имя, разрешения и ключи доступа.
4. Ключ политики концентратора событий — первичный или вторичный ключ политики общего доступа для доступа к концентратору событий.  
5. Ключевой столбец секции — необязательный параметр для выходных данных концентратора событий. Этот столбец содержит ключ раздела для выходных данных концентратора событий.

### Добавление концентратора событий как места назначения для выходных данных ###

1. Щелкните **Выходные данные** в верхней части страницы, затем щелкните **Добавить выходные данные**. Выберите концентратор событий в качестве места назначения выходных данных и нажмите правую кнопку в нижней части окна.

    ![рисунок38][graphic38]

2. Укажите соответствующие параметры выходных данных и по завершении нажмите правую кнопку в нижней части окна, чтобы продолжить.

    ![рисунок36][graphic36]

3. Проверьте формат сериализации событий, тип кодирования и формат и щелкните флажок, чтобы завершить это действие.

    ![рисунок37][graphic37]

## Использование Power BI как места назначения для выходных данных ##
---
### Обзор ###
Power BI в качестве места назначения для выходных данных задания Stream Analytics позволяет предоставить пользователям службы Stream Analytics широкие возможности визуализации. Их можно использовать для создания панелей мониторинга оперативных данных, формирования отчетов и составления отчетности на основе метрик. Дополнительные сведения о Power BI см. на сайте [Power BI](https://powerbi.microsoft.com/).

### Параметры ###

Для настройки выходных данных Power BI потребуются несколько параметров.

1. Псевдоним выходных данных — любое понятное название для выходных данных. Его особенно удобно использовать, если задание включает несколько мест назначения для выходных данных. В этом случае псевдоним будет указываться в запросе. Псевдоним выходных данных может иметь, например, следующий вид: OutPbi.
2. Имя набора данных — имя набора данных для использования в выходных данных Power BI. Для примера можно выбрать имя pbidemo.
2. Имя таблицы — имя таблицы в наборе выходных данных Power BI. Для примера можно выбрать имя pbidemo. **В настоящее время для вывода выходных данных из заданий Stream Analytics в Power BI можно использовать только одну таблицу в наборе данных.**

### Добавление Power BI как места назначения для выходных данных ###

1.  Щелкните **Выходные данные** в верхней части страницы, затем щелкните **Добавить выходные данные**. Выберите Power BI в качестве места назначения выходных данных.

    ![рисунок22][graphic22]

2.  Появится следующий экран:

    ![рисунок23][graphic23]

3.  На этом этапе укажите рабочую или учебную учетную запись для авторизации выходных данных Power BI. Если вы еще не зарегистрировались в Power BI, выберите параметр **Зарегистрироваться сейчас**.
4.  Появится экран следующего вида:

    ![рисунок24][graphic24]


>	[AZURE.NOTE] One should not explicitly create the dataset and table in the Power BI dashboard. The dataset and table will be automatically populated when the job is started and the job starts pumping output into Power BI. Note that if the job query doesn’t return any results, the dataset and table will not be created. Also be aware that if Power BI already had a dataset and table with the same name as the one provided in this Stream Analytics job, the existing data will be overwritten.

*	Нажмите кнопку **ОК** и щелкните **Проверить подключение**. Настройка выходных данных завершена.


## Использование табличного хранилища Azure для выходных данных ##
---
Табличное хранилище отличается высокой степенью доступности и масштабируемости, позволяя приложению автоматически осуществлять масштабирование в соответствии с нуждами пользователя. Табличное хранилище является хранилищем ключей и атрибутов NoSQL корпорации Майкрософт и позволяет работать со структурированными данными с менее жесткими ограничениями схемы. Хранилище таблиц Azure можно использовать для постоянного хранения данных и эффективного их извлечения. Дополнительные сведения о табличном хранилище Azure см. в статье [Табличное хранилище Azure](./articles/storage/storage-introduction.md).

### Параметры ###

Чтобы начать работы с табличным хранилищем Azure, потребуются следующие данные:

1. Имя учетной записи хранения (если подписка, по которой предоставляется хранилище, отличается от подписки задания потоковой передачи).
2. Ключ учетной записи хранения (если подписка, по которой предоставляется хранилище, отличается от подписки задания потоковой передачи).
3. Имя выходной таблицы (будет создана, если это еще не сделано).
4. Ключ секции (нужен обязательно).
5. Ключ строки.

Оптимальный подход к использованию ключа секции и ключа строки см. в статье [Проектирование стратегии масштабируемого секционирования для хранилища таблиц Azure](https://msdn.microsoft.com/library/azure/hh508997.aspx).

### Добавление табличного хранилища Azure для выходных данных ###

![рисунок11][graphic11]

Откройте вкладку выходных данных задания и выберите команду **ДОБАВИТЬ ВЫХОДНЫЕ ДАННЫЕ**, а затем нажмите кнопку "Далее".

![рисунок12][graphic12]

Выберите в качестве места назначения выходных данных **Табличное хранилище**.

![рисунок13][graphic13]

На следующей странице введите сведения о таблице Azure. Псевдоним выходных данных — имя, которое можно использовать в запросе для направления выходных данных запроса в соответствующую таблицу.

![рисунок14][graphic14]

![рисунок15][graphic15]

Размер пакета — это количество записей на пакетную операцию. Для большинства заданий достаточно значения по умолчанию. Дополнительные сведения об изменении этого параметра см. в статье о [спецификациях пакетных операций с таблицами](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx).

Если в подписке, которая используется для создания задания, уже существует хранилища Azure, установите флажок "Использовать учетную запись хранения из текущей подписки" и выберите учетную запись хранения в раскрывающемся списке.

Чтобы добавить это место назначения выходных данных, нажмите кнопку «Далее». После этого будут запущены две операции. Первая — добавление выходных данных.

![рисунок16][graphic16]

Вторая — проверка подключения. Azure Stream Analytics попытается подключиться к этой учетной записи хранения и проверить правильность введенных учетных данных.

## Очереди служебной шины ##
---
### Основные понятия, связанные с очередями служебной шины ###
Очереди служебной шины доставляют сообщения конкурирующим потребителям по типу FIFO (первым пришел, первым вышел). Обычно получатели принимают и обрабатывают сообщения в порядке их добавления в очередь, и каждое сообщение принимается и обрабатывается только одним потребителем сообщений.

Дополнительные сведения об очередях служебной шины см. в статье [Очереди шины обслуживания, разделы и подписки](https://msdn.microsoft.com/library/azure/hh367516.aspx "Очереди, разделы и подписки Service Bus") и [Вводные сведения об очередях служебной шины](http://blogs.msdn.com/b/appfabric/archive/2011/05/17/an-introduction-to-service-bus-queues.aspx "Вводные сведения об очередях служебной шины").

### Параметры ###

Чтобы начать использование выходных данных очередей служебной шины, потребуются следующие сведения:

1. псевдоним выходных данных — любое понятное название для выходных данных. Его особенно удобно использовать, если задание включает несколько мест назначения для выходных данных. В этом случае псевдоним будет указываться в запросе задания.
2. Пространство имен и имя служебной шины.
3. Имя очереди — очереди являются сущностями обмена сообщениями, так же как концентраторы событий и разделы. Они предназначены для сбора потоков событий с нескольких различных устройств и служб. Вновь созданной очереди присваивается определенное имя.
4. Формат сериализации, используемый для потока данных (Avro, CSV, JSON).

### Добавление очереди служебной шины как места назначения выходных данных ###

![рисунок31][graphic31]

Укажите необходимые данные, как показано ниже, и нажмите кнопку "Далее".

![рисунок32][graphic32]

Проверьте правильность формата данных и сериализации.

![рисунок33][graphic33]

## Разделы шины обслуживания ##
---
### Основные понятия, связанные с разделами служебной шины ###
Если очереди служебной шины предоставляют метод взаимодействия "один к одному", то разделы позволяют использовать форму взаимодействия "один ко многим".

Дополнительные сведения об разделах служебной шины см. в статье [Очереди шины обслуживания, разделы и подписки](https://msdn.microsoft.com/library/azure/hh367516.aspx "Очереди, разделы и подписки Service Bus") и [Вводные сведения о разделах служебной шины](http://blogs.msdn.com/b/appfabric/archive/2011/05/25/an-introduction-to-service-bus-topics.aspx "Вводные сведения о разделах служебной шины").

### Параметры ###

Чтобы начать использование выходных данных разделов служебной шины, потребуются следующие сведения:

1. псевдоним выходных данных — любое понятное название для выходных данных. Его особенно удобно использовать, если задание включает несколько мест назначения для выходных данных. В этом случае псевдоним будет указываться в запросе.
2. Пространство имен и имя служебной шины.
3. Имя раздела — разделы являются сущностями обмена сообщениями, так же как концентраторы событий и очереди. Они предназначены для сбора потоков событий с нескольких различных устройств и служб. Вновь созданному разделу присваивается определенное имя. Сообщения, отправленные в раздел, будут недоступны, если не создана подписка, поэтому убедитесь, что раздел содержит одну или несколько подписок.
4. Формат сериализации, используемый для потока данных (Avro, CSV, JSON).

### Добавление разделов служебной шины как места назначения выходных данных ###

Выберите разделы служебной шины в качестве выходных данных.

![рисунок34][graphic34]

Укажите необходимые данные, как показано ниже, и нажмите кнопку "Далее".

![рисунок35][graphic35]

Проверьте правильность формата данных и сериализации.

![рисунок33][graphic33]


## Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=AzureStreamAnalytics).

## Дальнейшие действия

- [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
- [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
- [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-outputs/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-outputs/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-outputs/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-outputs/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-outputs/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-outputs/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-outputs/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-outputs/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-outputs/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-outputs/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-outputs/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-outputs/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-outputs/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-outputs/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-outputs/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-outputs/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-outputs/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-outputs/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-outputs/19-stream-analytics-connect-data-event-input-output.png
[graphic20]: ./media/stream-analytics-connect-data-event-outputs/20-stream-analytics-connect-data-event-input-output.png
[graphic21]: ./media/stream-analytics-connect-data-event-outputs/21-stream-analytics-connect-data-event-input-output.png
[graphic22]: ./media/stream-analytics-connect-data-event-outputs/22-stream-analytics-connect-data-event-input-output.png
[graphic23]: ./media/stream-analytics-connect-data-event-outputs/23-stream-analytics-connect-data-event-input-output.png
[graphic24]: ./media/stream-analytics-connect-data-event-outputs/24-stream-analytics-connect-data-event-input-output.png
[graphic25]: ./media/stream-analytics-connect-data-event-outputs/25-stream-analytics-connect-data-event-input-output.png
[graphic26]: ./media/stream-analytics-connect-data-event-outputs/26-stream-analytics-connect-data-event-input-output.png
[graphic27]: ./media/stream-analytics-connect-data-event-outputs/27-stream-analytics-connect-data-event-input-output.png
[graphic28]: ./media/stream-analytics-connect-data-event-outputs/28-stream-analytics-connect-data-event-input-output.png
[graphic29]: ./media/stream-analytics-connect-data-event-outputs/29-stream-analytics-connect-data-event-input-output.png
[graphic30]: ./media/stream-analytics-connect-data-event-outputs/30-stream-analytics-connect-data-event-input-output.png
[graphic31]: ./media/stream-analytics-connect-data-event-outputs/31-stream-analytics-connect-data-event-input-output.png
[graphic32]: ./media/stream-analytics-connect-data-event-outputs/32-stream-analytics-connect-data-event-input-output.png
[graphic33]: ./media/stream-analytics-connect-data-event-outputs/33-stream-analytics-connect-data-event-input-output.png
[graphic34]: ./media/stream-analytics-connect-data-event-outputs/34-stream-analytics-connect-data-event-input-output.png
[graphic35]: ./media/stream-analytics-connect-data-event-outputs/35-stream-analytics-connect-data-event-input-output.png
[graphic36]: ./media/stream-analytics-connect-data-event-outputs/36-stream-analytics-connect-data-event-input-output.png
[graphic37]: ./media/stream-analytics-connect-data-event-outputs/37-stream-analytics-connect-data-event-input-output.png
[graphic38]: ./media/stream-analytics-connect-data-event-outputs/38-stream-analytics-connect-data-event-input-output.png

<!---HONumber=Nov15_HO3-->