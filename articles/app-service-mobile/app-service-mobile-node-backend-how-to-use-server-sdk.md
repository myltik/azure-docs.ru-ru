<properties
	pageTitle="Как работать с пакетом SDK для серверной части на Node для мобильных приложений | Служба приложений Azure"
	description="Узнайте, как работать с пакетом SDK для серверной части на Node для мобильных приложений службы приложений Azure."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianha"
	manager=""
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="node"
	ms.topic="article"
	ms.date="11/13/2015"
	ms.author="adrianha"/>

# Использование пакета SDK для Node для мобильных приложений Azure

Эта статья содержит подробную информацию о программировании серверной части на Node в мобильных приложениях службы приложений Azure и соответствующие примеры.

	> [AZURE.NOTE] This SDK is in PREVIEW.  As a result, we do not recommend that you use this SDK in production.  The examples
	in this document use v2.0.0-beta1 of azure-mobile-apps.

## <a name="Introduction"></a>Введение

Мобильное приложение службы приложений Azure позволяет добавлять веб-API в веб-приложения для доступа к данным, оптимизированным для мобильных устройств. Пакет SDK для приложений службы приложений Azure используется в веб-приложениях ASP.NET и NodeJS для выполнения следующих операций.

- Табличные операции (чтение, вставка, обновление, удаление данных).
- Пользовательские операции API.

Операции обоих типов поддерживают проверку подлинности для всех поставщиков удостоверений, разрешенных службой приложений Azure, включая поставщиков удостоверений социальных сетей, таких как Facebook, Twitter, Google и Майкрософт, а также Azure Active Directory для корпоративной проверки подлинности.

Примеры для каждого варианта использования можно найти в [каталоге примеров на GitHub].

### <a name="howto-cmdline-basicapp"></a>Создание базовой серверной части на Node с помощью командной строки

Каждая серверная часть на Node для мобильного приложения службы приложений Azure запускается как приложение ExpressJS. ExpressJS является наиболее популярной платформой веб-служб для Node. Вот как можно создать простое приложение ExpressJS на Node.

1. В командной строке или в окне PowerShell создайте новый каталог для проекта.

	```
	mkdir basicapp
	```

2. Запустите команду npm init, чтобы инициализировать структуру пакета.

	```
	cd basicapp
	npm init
	```

	Для инициализации проекта командой npm init будет выведен ряд вопросов. См. пример выходных данных ниже.

	![Выходные данные npm init][0]

3. Установите библиотеки express и azure-mobile-apps из репозитория npm.

	```
	npm install --save express azure-mobile-apps
	```

4. Создайте файл app.js для реализации базового мобильного сервера.

	```
	var express = require('express'),
		azureMobileApps = require('azure-mobile-apps');

	var app = express(),
		mobile = azureMobileApps();

	// Define a TodoItem table
	mobile.tables.add('TodoItem');

	// Add the mobile API so it is accessible as a Web API
	app.use(mobile);

	// Start listening on HTTP
	app.listen(process.env.PORT || 3000);
	```

Это приложение создает простой WebAPI, оптимизированный для мобильных устройств, с одной конечной точкой — /tables/TodoItem, которая с помощью динамической схемы предоставляет доступ к базовому хранилищу данных SQL без проверки подлинности. Этот пример подходит к следующим примерам использования клиентских библиотек.

- [Быстрый запуск клиента iOS].
- [Быстрый запуск клиента Xamarin.iOS].
- [Быстрый запуск клиента Xamarin.Android].
- [Быстрый запуск клиента Xamarin.Forms].
- [Быстрый запуск клиента Windows Phone].
- [Быстрый запуск клиента HTML/JavaScript].

Код этого базового приложения можно найти в [примере basicapp на GitHub].

### <a name="howto-vs2015-basicapp"></a>Создание серверной части на Node с помощью Visual Studio 2015

Для разработки приложения на Node в Visual Studio 2015 требуется специальное расширение. Для начала скачайте и установите [средства Node.js 1.1 для Visual Studio]. После установки средств Node.js для Visual Studio создайте приложение Express 4.x.

1. Откройте диалоговое окно **Новый проект** (выберите **Файл** > **Создать** > **Проект...**).

2. Разверните **Шаблоны** > **JavaScript** > **Node.js**.

3. Выберите **Базовое приложение Node.js Express 4 для Azure**.

4. Введите имя проекта. Нажмите кнопку *ОК*.

	![Новый проект Visual Studio 2015][1]

5. Щелкните правой кнопкой мыши узел **npm** и выберите **Установка новых пакетов npm...**.

6. Вам потребуется обновить каталог npm перед созданием первого приложения на Node. Для этого нажмите **Обновить**.

7. В поле поиска введите _azure-mobile-apps_. Выберите пакет **azure-mobile-apps 2.0.0** и нажмите **Установить пакет**.

	![Установка новых пакетов npm][2]

8. Нажмите кнопку **Закрыть**.

9. Откройте файл _app.js_, чтобы добавить поддержку пакета SDK для мобильных приложений Azure:

  а. В строку 6 добавьте следующий код:

    ```
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

  b. Приблизительно в строке 27 добавьте следующий код:

	```
	app.use('/users', users);

	// Azure Mobile Apps Initialization
	var mobile = azureMobileApps();
	mobile.tables.add('TodoItem');
	app.use('mobile');
	```

  c. Сохраните файл .

10. Запустите приложение в локальном режиме (API будет расположен на http://localhost:3000) или опубликуйте его в Azure.

### <a name="howto-publish-to-azure"></a>Публикация серверной части на Node в Azure

Microsoft Azure предоставляет множество механизмов публикации серверной части на Node для мобильных приложений службы приложений Azure. К ним относятся средства развертывания, интегрированные в Visual Studio, средства командной строки и средства непрерывного развертывания на основе системы управления версиями. Дополнительные сведения по этому вопросу см. в статье [Руководство по развертыванию службы приложений Azure].

В отношении приложений на Node в службе приложений Azure имеются четкие рекомендации, с которыми вам следует ознакомиться перед развертыванием:

- [Указание версии Node]
- [Использование модулей Node]

## <a name="TableOperations"></a>Операции с таблицами

Серверная часть пакета SDK azure-mobile-apps для Node предоставляет механизмы доступа к таблицам, хранящимся в базах данных SQL Azure. Доступ осуществляется через WebAPI. Пакетом предоставляются пять операций.

| Операция | Описание |
| --------- | ----------- |
| GET /tables/\_tablename\_ | Получает все записи в таблице |
| GET /tables/\_tablename\_/:id | Получает определенную запись из таблицы |
| POST /tables/\_tablename\_ | Создает новую запись в таблице |
| PUT /tables/\_tablename\_/:id | Обновляет существующую запись в таблице |
| DELETE /tables/\_tablename\_/:id | Удаляет запись из таблицы |

Этот WebAPI поддерживает протокол [OData] и расширяет схему таблицы для поддержки [синхронизации автономных данных].

### <a name="howto-dynamicschema"></a>Определение таблиц с помощью динамических схем

Перед использованием таблицы ее необходимо определить. Таблицы можно определять с помощью статических схем (когда разработчик определяет столбцы в схеме) или динамических схем (когда схема определяется пакетом SDK в зависимости от поступающих запросов). Кроме того, разработчик может управлять некоторыми деталями работы WebAPI путем добавления в определение кода на JavaScript.

Рекомендуется определять все таблицы в отдельных файлах JavaScript и размещать их в каталоге tables, а затем использовать метод tables.import(), чтобы импортировать таблицы. Расширим базовое приложение, изменив файл app.js:

```
var express = require('express'),
	azureMobileApps = require('azure-mobile-apps');

var app = express(),
	mobile = azureMobileApps();

// Define the database schema that is exposed
mobile.tables.import('./tables');

// Provide initialization of any tables that are statically defined
mobile.tables.initialize().then(function () {
	// Add the mobile API so it is accessible as a Web API
	app.use(mobile);

	// Start listening on HTTP
	app.listen(process.env.PORT || 3000);
});
```

Определение таблицы в файле ./tables/TodoItem.js:

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here

module.exports = table;
```

По умолчанию в таблицах используется динамическая схема. Чтобы глобально отключить динамические схемы, присвойте параметру приложения **MS\_DynamicSchema** значение false на портале Azure.

Полный пример можно найти в [примере todo на GitHub].

### <a name="howto-staticschema"></a>Определение таблиц с помощью статических схем

Вы можете явным образом определять столбцы, которые будут предоставляться через WebAPI. Пакет SDK azure-mobile-apps для Node автоматически добавляет к вашему списку дополнительные столбцы, необходимые для автономной синхронизации данных. Например, в примерах клиентских приложений требуется таблица с двумя столбцами: text (строка) и завершения complete (логическое значение). Эти столбцы могут определяться в определении таблицы в файле JavaScript (расположен в каталоге tables) следующим образом:

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table
table.columns = {
	"text": "string",
	"complete": "boolean"
};

// Turn off dynamic schema
table.dynamicSchema = false;

module.exports = table;
```

Если таблица определяется статически, то вам потребуется вызвать метод tables.initialize(), чтобы создать схему базы данных при запуске приложения. Метод tables.initialize() возвращает значение [Promise], которое не позволяет веб-службе обслуживать запросы до инициализации базы данных.

### <a name="howto-sqlexpress-setup"></a>Использование SQL Express в качестве хранилища данных при разработке на локальном компьютере

Пакет SDK для мобильных приложений Azure на Node предоставляет пользователям три варианта обслуживания данных:

- Драйвер **memory** для создания примера несохраняемого хранилища
- Драйвер **sql** для доступа к базе данных SQL Express во время разработки
- Драйвер **sql** для доступа к базе данных SQL Azure во время разработки

Для создания и использования подключения к базам данных SQL Express и SQL Azure пакет SDK для мобильных приложений Azure на Node использует пакет [mssql Node]. Для использования этого пакета необходимо включить TCP-подключения в вашем экземпляре SQL Express.

   >[AZURE.NOTE]Драйвер memory не предоставляет полный набор средств для тестирования. Для тестирования серверной части в локальном режиме рекомендуется использовать базу данных SQL Express и драйвер sql.

1. Скачайте и установите [Microsoft SQL Server 2014 Express]. Убедитесь, что устанавливается выпуск SQL Server 2014 Express с инструментами. Если вам явно не требуется 64-разрядная версия, воспользуйтесь 32-разрядной версией, которая потребляет меньше памяти во время выполнения.

2. Запустите диспетчер конфигурации SQL Server 2014.

  а. Разверните узел **Сетевая конфигурация SQL Server** в левой части дерева меню. б. Выберите **Протоколы для SQLEXPRESS**. в. Щелкните правой кнопкой мыши **TCP/IP** и выберите **Включить**. Нажмите **ОК** во всплывающем диалоговом окне. г. Щелкните правой кнопкой мыши **TCP/IP** и выберите **Свойства**. д. Перейдите на вкладку **IP-адреса**. е. Найдите узел **IPAll**. В поле **TCP-порт** введите **1433**.

  ![Настройка SQL Express для TCP/IP][3]

  ж. Нажмите кнопку **ОК**. Нажмите **ОК** во всплывающем диалоговом окне. з. В дереве слева выберите **Службы SQL Server**. и. Щелкните правой кнопкой мыши **SQL Server (SQLEXPRESS)** и выберите **Перезапуск**. к. Закройте диспетчер конфигурации SQL Server 2014.

3. Запустите SQL Server 2014 Management Studio и подключитесь к локальному экземпляру SQL Express.

  а. Щелкните правой кнопкой мыши экземпляр SQL Express в обозревателе объектов и выберите **Свойства** б. Перейдите на страницу **Безопасность**. в. Убедитесь что выбран режим **SQL Server и режим проверки подлинности Windows**. г. Нажмите кнопку **ОК**.

  ![Настройка проверки подлинности в SQL Express][4]

  д. В обозревателе объектов разверните узлы **Безопасность** > **Имена входа**. е. Щелкните правой кнопкой мыши пункт **Имена входа** и выберите **Новое имя входа...** ж. Введите имя входа. Выберите **Проверка подлинности SQL Server**. Введите пароль, а затем в поле **Подтверждение пароля** введите пароль еще раз. Обратите внимание, что пароль должен соответствовать требованиями к сложности паролей, установленным в Windows. з. Нажмите кнопку **ОК**.

  ![Добавление нового пользователя в SQL Express][5]

  i. Щелкните правой кнопкой мыши новое имя входа и выберите **Свойства**. к. Перейдите на страницу **Роли сервера**. л. Установите флажок рядом с ролью **dbcreator**. м. Нажмите кнопку **ОК**. н. Закройте SQL Server 2015 Management Studio.

Запишите имя созданного пользователя и пароль. В зависимости от требований конкретной базы данных, вам может потребоваться назначить дополнительные роли сервера или разрешения.

Для получения строки подключения к этой базе данных приложение на Node обращается к переменной среды **SQLCONNSTR\_MS\_TableConnectionString**. Значение переменной устанавливается непосредственно в вашей среде. Например, чтобы присвоить этой переменной значение, вы можете воспользоваться следующей командой PowerShell:

```
$env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"
```

Обратите внимание, что доступ к базе данных осуществляется через подключение TCP/IP и для этого подключения требуется указать имя пользователя и пароль.

### <a name="howto-config-localdev"></a>Настройка проекта для локальной разработки

Мобильные приложения Azure считывают файл JavaScript с именем _azureMobile.js_ из локальной файловой системы. В рабочей среде не следует использовать этот файл для настройки пакета SDK для мобильных приложений Azure. Вместо этого используйте параметры приложения на [портале Azure]. Файл _azureMobile.js_ должен экспортировать объект конфигурации. Ниже перечислены основные параметры.

- Параметры базы данных
- Параметры ведения журналов диагностики
- Дополнительные параметры CORS

Ниже приведен пример файла _azureMobile.js_, реализующего приведенные выше параметры базы данных:

```
module.exports = {
	cors: {
		origins: [ 'localhost' ]
	},
	data: {
		provider: 'sql',
		server: '127.0.0.1',
		database: 'mytestdatabase',
		user: 'azuremobile',
		password: 'T3stPa55word'
	},
	logging: {
		level: 'verbose'
	}
};
```

Мы рекомендуем добавить _azureMobile.js_ в файл _.gitignore_ (или другой файл исключений для системы управления версиями), чтобы не допустить сохранения паролей в облаке. Параметры рабочей версии всегда следует настраивать через «Параметры приложения» на [портале Azure].

### <a name="howto-use-sqlazure"></a>Использование базы данных SQL Azure в качестве рабочего хранилища

<!-- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Порядок использования баз данных SQL Azure в качестве хранилища данных идентичен для всех типов приложений службы приложений Azure. Если вы этого еще не сделали, создайте новую серверную часть мобильного приложения, выполнив указанные ниже действия.

1. Войдите на [портал Azure].

2. В верхней левой части окна нажмите кнопку **+СОЗДАТЬ** > **Интернет + мобильные устройства** > **Мобильное приложение**, а затем введите имя серверной части вашего мобильного приложения.

3. В окне **Группа ресурсов** введите имя своего приложения.

4. Будет выбран план службы приложений по умолчанию. Если вы хотите изменить план службы приложений, выберите «План службы приложений» > **+ Создать**. Введите имя нового плана службы приложений и выберите подходящее расположение. Щелкните "Ценовая категория" и выберите соответствующую категорию для своей службы. Щелкните **Просмотреть все**, чтобы просмотреть другие варианты тарификации, например **Бесплатный** и **Общий**. Выберите ценовую категорию и нажмите кнопку **Выбрать**. В колонке **План службы приложений** нажмите кнопку **ОК**.

5. Щелкните **Создать**. При этом создается серверная часть мобильного приложения, где позже будет развернут серверный проект. Подготовка серверной части мобильного приложения может занять несколько минут. После подготовки серверной части мобильного приложения на портале для нее откроется колонка **Параметры**.

Создав серверную часть мобильного приложения, вы можете подключить ее к существующей базе данных SQL Azure либо создать новую базу данных SQL Azure. В этом практическом руководстве мы создадим новую базу данных SQL.

    > [AZURE.NOTE] If you already have a database in the same location as the new mobile app backend, you can instead choose **Use an existing database** and then select that database. The use of a database in a different location is not recommended because of additional bandwidth costs and higher latencies.

6. В новой серверной части мобильного приложения щелкните **Параметры** > **Мобильное приложение** > **Данные** > **+Добавить**.

7. В колонке **Добавить подключение данных** щелкните **База данных SQL — Настройка обязательных параметров** > **Создать новую базу данных**. Введите имя новой базы данных в поле **Имя**.

8. Щелкните **Сервер**. В колонке **Новый сервер** введите уникальное имя сервера в поле **Имя сервера** и укажите подходящие **имя администратора сервера** и **пароль**. Убедитесь, что установлен флажок напротив пункта **Разрешить службам Azure доступ к серверу**. Нажмите кнопку **ОК**.

	![Создание базы данных SQL Azure][6]

9. В колонке **Новая база данных** щелкните **ОК**.

10. В колонке **Добавить подключение данных** выберите **Строка подключения** и введите имя и пароль, указанные при создании базы данных. При использовании существующей базы данных укажите соответствующие учетные данные для входа. Войдя в базу данных, нажмите кнопку **ОК**.

11. В колонке **Добавить подключение данных** снова щелкните **ОК**, чтобы создать базу данных.

<!-- END OF ALTERNATE INCLUDE -->

Создание базы данных может занять несколько минут. Используйте область **Уведомления**, чтобы отслеживать ход выполнения развертывания. Не выполняйте дальнейших действий, пока база данных не будет успешно развернута. После завершения развертывания в настройки серверной части мобильного приложения будет добавлена строка подключения к экземпляру базы данных SQL Azure. Эту настройку можно увидеть, выбрав **Параметры** > **Параметры приложения** > **Строки подключения**.

### <a name="howto-tables-auth"></a>Обязательная проверка подлинности для доступа к таблицам

Если вы хотите настроить обязательную проверку подлинности через конечную точку доступа к таблицам, вам потребуется настроить службу проверки подлинности в службе приложений на [портале Azure]. Дополнительные сведения о настройке проверки подлинности в службе приложений Azure приведены в руководстве по настройке соответствующего поставщика удостоверений.

- [Настройка проверки подлинности в Azure Active Directory].
- [Настройка проверки подлинности в Facebook].
- [Настройка проверки подлинности в Google].
- [Настройка проверки подлинности в Microsoft].
- [Настройка проверки подлинности в Twitter].

В каждой таблице имеется свойство доступа (access), которое можно использовать для контроля доступа к таблице. В следующем примере показана статически определенная таблица с обязательной проверкой подлинности.

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table
table.columns = {
	"text": "string",
	"complete": "boolean"
};

// Turn off dynamic schema
table.dynamicSchema = false;

// Require authentication to access the table
table.access = 'authenticated';

module.exports = table;
```

Свойство доступа может принимать два значения:

  - значение *проверка подлинности* указывает, что клиентское приложение вместе с запросом должно отправлять действительный маркер проверки подлинности;
  - значение *отключено* указывает, что эта таблица в настоящее время отключена.

Если свойство доступа нельзя определить, будет разрешен доступ без проверки подлинности.

### <a name="howto-tables-disabled"></a>Отключение доступа к определенным операциям с таблицей

Свойство доступа может относиться не только к таблице в целом, но и к отдельным операциям. Существует четыре операции:

  - *read* — совместимая с REST операция GET;
  - *insert* — совместимая с REST операция POST;
  - *update* — совместимая с REST операция PATCH
  - *delete* — совместимая с REST операция DELETE.

Например, вы хотите создать таблицу с доступом только для чтения без проверки подлинности. Для этого можно использовать следующее определение таблицы:

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-Only table - only allow READ operations
table.read.access = undefined;
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="howto-tables-query"></a>Изменение запроса, используемого в операциях с таблицей

Частой задачей в операциях с таблицей является ограничение доступа к определенным данным. Например, вам может потребоваться таблица с указанием идентификатора пользователя, выполнившего вход, при том чтобы каждый пользователь мог считывать или обновлять только свои записи. Эту возможность обеспечивает следующее определение таблицы:

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define a static schema for the table
table.columns = {
	"userId": "string",
	"text": "string",
	"complete": "boolean"
};
table.dynamicSchema = false;

// Require authentication for this table
table.access = 'authenticated';

// Ensure that only records for the authenticated user are retrieved
table.read(function (context) {
	context.query.where({ userId: context.user.id });
	return context.execute();
});

// When adding records, add or overwrite the userId with the authenticated user
table.insert(function (context) {
	context.item.userId = context.user.id;
	return context.execute();
}

module.exports = table;
```

Операции, которые подразумевают выполнение запроса, будут иметь свойство query, в которое можно добавить предложение where. Свойство query представляет собой объект [QueryJS], используемый для преобразования запроса OData в то, что может быть обработано серверной частью. В простых операциях сравнения (как в примере выше) можно использовать карты. Добавлять новые предложения SQL относительно несложно:

```
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Настройка обратимого удаления из таблицы

При обратимом удалении записи фактически не удаляются. Вместо этого они помечаются как удаленные установкой значения true в столбце deleted. Пакет SDK для мобильных приложений Azure автоматически удаляет записи, помеченные как удаленные, из результатов запроса, если только в методе не используется конструкция IncludeDeleted(). Чтобы настроить обратимое удаление из таблицы, настройте свойство softDelete в файле определения таблицы. Например:

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table
table.columns = {
	"text": "string",
	"complete": "boolean"
};

// Turn off dynamic schema
table.dynamicSchema = false;

// Turn on Soft Delete
table.softDelete = true;

// Require authentication to access the table
table.access = 'authenticated';

module.exports = table;
```

Вам потребуется создать механизм удаления записей из клиентского приложения через веб-задания или пользовательский механизм.

### <a name="howto-tables-seeding"></a>Заполнение базы данных данными

При создании нового приложения вам может потребоваться заполнить таблицу данными. Это можно сделать в файле JavaScript определения таблицы:

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table
table.columns = {
	"text": "string",
	"complete": "boolean"
};
table.seed = [
	{ text: 'Example 1', complete: false },
	{ text: 'Example 2', complete: true }
];

// Turn off dynamic schema
table.dynamicSchema = false;

// Require authentication to access the table
table.access = 'authenticated';

module.exports = table;
```

Важно отметить, что заполнение данных выполняется только при создании таблицы с помощью пакета SDK для мобильных приложений Azure. Если таблица уже существует в базе данных, данные не добавляются. Если включены динамические схемы, то схема будет определена на основании добавляемых данных.

Чтобы создать таблицу при запуске службы, рекомендуется явно вызывать метод initialize().

## <a name="CustomAPI"></a>Настраиваемая служба API

Помимо API доступа к данным через конечную точку, мобильные приложения Azure могут предоставлять настраиваемые службы API. Настраиваемые службы API определяются так же, как и таблицы, и могут использовать тот же набор возможностей, включая проверку подлинности.

Если вы хотите настроить обязательную проверку подлинности через настраиваемую службу API, вам потребуется настроить службу проверки подлинности в службе приложений на [портале Azure]. Дополнительные сведения о настройке проверки подлинности в службе приложений Azure приведены в руководстве по настройке соответствующего поставщика удостоверений.

- [Настройка проверки подлинности в Azure Active Directory].
- [Настройка проверки подлинности в Facebook].
- [Настройка проверки подлинности в Google].
- [Настройка проверки подлинности в Microsoft].
- [Настройка проверки подлинности в Twitter].

### <a name="howto-customapi-basic"></a>Определение простой настраиваемой службы API

Определение настраиваемых API схоже с определением API таблиц.

1. Создайте каталог **api**
2. В каталоге **api** создайте файл JavaScript с определением API.
3. Вызовите метод import, чтобы импортировать каталог **api**.

Вот прототип определения API для использованного выше примера basic-app.

```
var express = require('express'),
	azureMobileApps = require('azure-mobile-apps');

var app = express(),
	mobile = azureMobileApps();

// Import the Custom API
mobile.api.import('./api');

// Add the mobile API so it is accessible as a Web API
app.use(mobile);

// Start listening on HTTP
app.listen(process.env.PORT || 3000);
```

Рассмотрим простой API, который возвращает дату сервера с помощью метода _Date.now()_. Ниже приведено содержимое файла api/date.js:

```
var api = {
	get: function (req, res, next) {
		var date = { currentTime: Date.now() };
		res.status(200).type('application/json').send(date);
	});
};

module.exports = api;
```

Каждый параметр соответствует стандартным RESTful-вызовам: GET, POST, PATCH и DELETE. Этот метод является стандартной функцией [промежуточного слоя ExpressJS], которая отправляет требуемые выходные данные.

### <a name="howto-customapi-auth"></a>Обязательная проверка подлинности для доступа к настраиваемой службе API

Пакет SDK для мобильных приложений Azure реализует проверку подлинности точно так же, как конечные точки доступа к таблицам и настраиваемые службы API. Чтобы добавить проверку подлинности к API-интерфейсу, разработанному в предыдущем разделе, добавьте свойство **access**:

```
var api = {
	get: function (req, res, next) {
		var date = { currentTime: Date.now() };
		res.status(200).type('application/json').send(date);
	});
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

Также можно определить проверку подлинности для конкретных операций:

```
var api = {
	get: function (req, res, next) {
		var date = { currentTime: Date.now() };
		res.status(200).type('application/json').send(date);
	});
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

Для настраиваемых служб API, требующих проверки подлинности, необходимо использовать тот же маркер, что и для конечных точек доступа к таблицам.

## <a name="Debugging"></a>Отладка и устранение неполадок

Служба приложений Azure предоставляет несколько методов отладки и устранения неполадок в приложениях на Node. Для пользователей доступны следующие инструменты.

- [Мониторинг службы приложений Azure]
- [Включение ведения журналов диагностики в службе приложений Azure]
- [Диагностика службы приложений Azure в Visual Studio]

### <a name="howto-diagnostic-logs"></a>Запись информации в журналы диагностики мобильных приложений Azure

Приложениям на Node предоставляется доступ к различным средствам работы с журналами диагностики. Для ведения журнала диагностики в пакете SDK для мобильных приложений Azure на Node используется [Winston]. Ведение журналов включается автоматически в режиме отладки или установкой значения true для параметра **MS\_DebugMode** на [портале Azure]. Созданные журналы можно просмотреть на странице «Журналы диагностики» [портала Azure].

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ../../includes/media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png

<!-- URLs -->
[Быстрый запуск клиента iOS]: app-service-mobile-ios-get-started.md
[Быстрый запуск клиента Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Быстрый запуск клиента Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Быстрый запуск клиента Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Быстрый запуск клиента Windows Phone]: app-service-mobile-windows-store-dotnet-get-started.md
[Быстрый запуск клиента HTML/JavaScript]: app-service-html-get-started.md
[синхронизации автономных данных]: app-service-mobile-offline-data-sync.md
[Настройка проверки подлинности в Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Настройка проверки подлинности в Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Настройка проверки подлинности в Google]: app-service-mobile-how-to-configure-google-authentication.md
[Настройка проверки подлинности в Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Настройка проверки подлинности в Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md
[Руководство по развертыванию службы приложений Azure]: ../app-service-web/web-site-deploy.md
[Мониторинг службы приложений Azure]: ../app-service-web/web-sites-monitor.md
[Включение ведения журналов диагностики в службе приложений Azure]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Диагностика службы приложений Azure в Visual Studio]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[Указание версии Node]: ../nodejs-specify-node-version-azure-apps.md
[Использование модулей Node]: ../nodejs-use-node-mobiles-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/

[портал Azure]: https://portal.azure.com/
[портала Azure]: https://portal.azure.com/
[портале Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/ru-RU/docs/Web/JavaScript/Reference/Global_Objects/Promise
[примере basicapp на GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[примере todo на GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[каталоге примеров на GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[средства Node.js 1.1 для Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/ru-RU/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[промежуточного слоя ExpressJS]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=Nov15_HO4-->