<properties 
	pageTitle="Подключение к локальному серверу SQL Server из приложения API в службе приложений Azure с помощью гибридных подключений" 
	description="Создание приложения API в Microsoft Azure и его подключение к локальной базе данных SQL Server"
	services="app-service\api" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="tdykstra"/>

# Подключение к локальному серверу SQL Server из приложения API в службе приложений Azure с помощью гибридных подключений

Гибридные подключения могут подключать приложения API [службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714) к локальным ресурсам, которые используют статический TCP-порт. В поддерживаемые ресурсы входят Microsoft SQL Server, MySQL, веб-интерфейсы API HTTP, мобильные службы и самые настраиваемые веб-службы.

В этом учебнике вы узнаете, как создать приложение API службы приложений на [портале предварительной версии Azure](http://go.microsoft.com/fwlink/?LinkId=529715), которое будет подключаться к локальной базе данных SQL Server с помощью новой функции гибридного подключения. Этот учебник разработан для пользователей, не имеющих опыта работы с Azure или SQL Server.

>[AZURE.NOTE]Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

## Предварительные требования

Для работы с этим учебником вам потребуются следующие продукты. Поскольку у всех этих продуктов есть бесплатные версии, вы можете начать разработку решений для Azure бесплатно.

- **Подписка Azure**. Бесплатную подписку можно найти на сайте [Бесплатная пробная версия Azure](/pricing/free-trial/). 

- **Visual Studio**. Загрузить бесплатную пробную версию Visual Studio 2013 или Visual Studio 2015 можно по ссылке [Загружаемые файлы для Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Прежде чем продолжить, установите одну из этих версий. (Приведенные в этом учебнике снимки экрана сделаны с использованием Visual Studio 2013.)

- **SQL Server 2014 Express с инструментами** — загрузите Microsoft SQL Server Express бесплатно на [странице базы данных веб-платформы Майкрософт](https://www.microsoft.com/ru-RU/download/details.aspx?id=42299). Далее из этой статьи вы узнаете, как [установить SQL Server](#InstallSQLDB). Это позволит вам правильно его настроить.

- **SQL Server Management Studio Express**. Сюда включается SQL Server 2014, экспресс-выпуск с инструментами, указанный выше, но если требуется установить его отдельно, можно скачать и установить его на [странице скачивания SQL Server Express](https://www.microsoft.com/ru-RU/download/details.aspx?id=42299).

В учебнике предполагается, что у вас есть подписка Azure, установлена служба Visual Studio 2013 и установлена или включена .NET Framework 3.5. В учебнике показано, как установить SQL Server 2014 Express в конфигурации, которая хорошо работает с функцией гибридных подключений Azure (экземпляр по умолчанию со статическим TCP-портом). Перед началом работы с учебником загрузите (но не устанавливайте) SQL Server 2014 Express с инструментами из указанного выше расположения, если SQL Server не установлен.

### Примечания
Для использования локальной базы данных SQL Server или SQL Server Express с помощью гибридного подключения в статическом порте должен быть включен протокол TCP/IP. Экземпляры SQL Server по умолчанию используют статический порт 1433, а именованные экземпляры не используют этот порт.

Компьютер, на котором установлен локальный агент диспетчера гибридных подключений:

- Необходимо иметь исходящее подключение к Azure через:

> <table border="1">
    <tr>
       <th><strong>Порт</strong></th>
        <th>Назначение</th>
    </tr>
    <tr>
        <td>80</td>
        <td><strong>Требуется</strong> для порта HTTP при проверке сертификатов и (необязательно) для подключения к данным.</td>
    </tr>
    <tr>
        <td>443</td>
        <td><strong>Необязательно</strong> для подключения к данным. Если исходящее подключение к порту 443 недоступно, то используется порт TCP 80.</td>
    </tr>
	<tr>
        <td>5671 и 9352</td>
        <td><strong>Рекомендуется</strong>, но необязательно для подключения к данным. Обратите внимание, что в этом режиме обеспечивается более высокая пропускная способность. Если исходящее подключение к этим портам недоступно, то используется порт TCP 443.</td>
	</tr>
</table>

- должен подключаться к *hostname*:*portnumber* локального источника. 

В шагах этой статьи предполагается, что вы используете браузер с компьютера, на котором размещается локальный агент гибридного подключения.

Если SQL Server уже установлен в конфигурации и среде, которые соответствуют приведенным выше условиям, можно пропустить этот этап и перейти к разделу [Создание локальной базы данных SQL Server](#CreateSQLDB).

<a name="InstallSQL"></a>
## Установка экспресс-выпуска SQL Server, включение TCP/IP и создание локальной базы данных SQL Server

В этом разделе мы расскажем, как установить SQL Server Express, включить TCP/IP и создать базу данных. Благодаря этому ваше приложение API сможет работать с [порталом предварительной версии Azure](https://portal.azure.com).

<a name="InstallSQLDB"></a>
### Установка SQL Server Express

1. Чтобы установить SQL Server Express, загрузите файл **SQLEXPRWT\_x64\_ENU.exe** (64-разрядная версия) или **SQLEXPR\_x86\_ENU.exe** (32-разрядная версия) и извлеките его в нужную папку. 

2. После извлечения файлов установки SQL Server Express запустите файл **setup.exe**.

3. Когда отобразится окно **Центр установки SQL Server**, выберите пункт **Новая установка изолированного экземпляра SQL Server или добавление компонентов к существующей установке**.

	![Центр установки SQL Server](./media/app-service-api-hybrid-on-premises-sql-server/sql-server-installation-center.png)

4. Следуйте инструкциям, принимая варианты и параметры по умолчанию, пока не откроется страница **Конфигурация экземпляров**.
	
5. На странице **Конфигурация экземпляра** выберите **Экземпляр по умолчанию** и нажмите кнопку **Далее**.
	
	![Конфигурация экземпляра](./media/app-service-api-hybrid-on-premises-sql-server/instance-configuration.png)
	
	Экземпляр SQL Server по умолчанию прослушивает запросы от клиентов SQL Server на статический порт 1433 в соответствии с требованиями функции гибридных подключений. Именованные экземпляры используют динамические порты и UDP, которые не поддерживаются гибридными подключениями.
	
6. Примите значения по умолчанию на странице **Конфигурация сервера** и нажмите кнопку **Далее**.
	
7. На странице **Конфигурация модуля базы данных** в разделе **Режим проверки подлинности** выберите **Смешанный режим (проверка подлинности SQL Server и проверка подлинности Windows)** и укажите пароль.
	
	![Конфигурация ядра СУБД](./media/app-service-api-hybrid-on-premises-sql-server/database-engine-configuration.png)
	
	В этом учебнике вы будете использовать проверку подлинности SQL Server. Убедитесь, что запомнили указанный пароль, поскольку он понадобится вам позже.
	
8. Выполните все инструкции мастера, принимая настройки по умолчанию, чтобы завершить установку.

9. Закройте диалоговое окно **Центр установки SQL Server**.

### Включение TCP/IP
Чтобы включить TCP/IP, необходимо использовать диспетчер конфигурации SQL Server, который устанавливается при установке SQL Server Express. Перед тем как продолжить, выполните шаги в разделе [Включение протокола сети TCP/IP для SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx).

<a name="CreateSQLDB"></a>
### Создание локальной базы данных SQL Server

1. В **SQL Server Management Studio** подключитесь к установленному серверу SQL Server. В качестве **типа сервера** выберите **модуль базы данных**. В качестве **имени сервера** можно использовать **localhost** или имя используемого компьютера. Выберите пункт **Проверка подлинности SQL Server**, а затем войдите с именем пользователя `sa` и паролем, созданным ранее.

	![Подключение к серверу](./media/app-service-api-hybrid-on-premises-sql-server/connect-to-server.png)
	
	Если диалоговое окно **Подключение к серверу** не открывается автоматически, перейдите в **обозреватель объектов** на левой панели, щелкните **Подключение**, а затем щелкните **Ядро СУБД**.
	
2. Чтобы создать новую базу данных, используя SQL Server Management Studio, щелкните правой кнопкой мыши **Базы данных** в обозревателе сервера, а затем нажмите кнопку **Создать базу данных**.
	
	![Меню создания базы данных](./media/app-service-api-hybrid-on-premises-sql-server/new-database-menu.png)
	
3. В диалоговом окне **Создание базы данных** введите в качестве имени базы данных `LocalDatabase`, а затем нажмите кнопку **ОК**.
	
	![Создание базы данных](./media/app-service-api-hybrid-on-premises-sql-server/new-database.png)
	
### Создание и заполнение таблицы SQL Server

1. В **обозревателе объектов** **SQL Server Management Studio** разверните запись `LocalDatabase`.

	![Развернутая база данных](./media/app-service-api-hybrid-on-premises-sql-server/local-database-expanded.png)

2. Щелкните правой кнопкой мыши запись **Таблицы** и выберите в контекстном меню пункт **Таблица**.

	![Меню новой таблицы](./media/app-service-api-hybrid-on-premises-sql-server/new-table-menu.png)

3. Когда появится сетка конструктора таблицы, введите сведения о столбце, как показано на следующем рисунке.

	![Столбцы новой таблицы](./media/app-service-api-hybrid-on-premises-sql-server/table-def.png)

4. Нажмите **&lt;Ctrl>S**, чтобы сохранить определение новой таблицы. Вам будет предложено ввести имя таблицы. Введите `Speakers` и нажмите кнопку **ОК**.

	![Сохранение новой таблицы](./media/app-service-api-hybrid-on-premises-sql-server/save-new-table.png)

5. В **обозревателе объектов** щелкните правой кнопкой мыши созданную таблицу `Speakers` и выберите в контекстном меню пункт **Изменить верхние 200 строк**.

	![Изменение верхних 200 строк](./media/app-service-api-hybrid-on-premises-sql-server/edit-top-200-rows.png)

6. Когда появится сетка для ввода/изменения данных таблицы, введите некоторые тестовые данные, как показано на следующем рисунке.

	![Тестирование данных](./media/app-service-api-hybrid-on-premises-sql-server/speakers-data.png)

## Создание и развертывание демонстрационной версии приложения API в Azure 

В этом разделе описывается создание демонстрационного приложения API.

1. Откройте Visual Studio 2013 и последовательно выберите **Файл > Создать > Проект**. 

2. Выберите шаблон **Visual C# > Веб > Веб-приложение ASP.NET**, снимите флажок **Добавить Application Insights в проект**, присвойте проекту имя *SpeakersList*, а затем нажмите кнопку **ОК**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project.png)

3. В диалоговом окне **Новый проект ASP.NET** выберите шаблон проекта **Приложение API Azure** и нажмите кнопку **ОК**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project-api-app.png)

4. В **обозревателе решений** щелкните правой кнопкой мыши папку **Модели**, а затем в контекстном меню выберите **Добавить > Класс**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-menu.png)

5. Присвойте новому файлу имя *Speaker.cs*, а затем нажмите кнопку **Добавить**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-class.png)

6. Замените все содержимое файла `Speaker.cs` следующим кодом.

		namespace SpeakersList.Models
		{
			public class Speaker
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

7. В **обозревателе решений** щелкните правой кнопкой мыши папку **Контроллеры**, а затем в контекстном меню выберите **Добавить > Контроллер**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-controller.png)

8. В диалоговом окне **Добавление Scaffold** выберите параметр **Контроллер веб-API 2 — пустой** и нажмите кнопку **Добавить**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-scaffold.png)

9. Присвойте контроллеру имя **SpeakersController** и нажмите кнопку **Добавить**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-controller-name.png)

10. Замените код в файле `SpeakersController.cs` приведенным ниже кодом. Не забудьте указать пользовательские значения вместо заполнителей &lt;serverName> (имя сервера) и &lt;password> (пароль) в `connectionString`. Значение &lt;serverName> — это имя компьютера, на котором расположен SQL Server, а значение &lt;password> задается вами при установке и настройке SQL Server.

	> [AZURE.NOTE]В следующем фрагменте кода содержатся сведения о пароле. Это сделано для упрощения демонстрации. В реальной рабочей среде не следует хранить учетные данные в коде. Вместо этого см. [рекомендации по развертыванию паролей (и других конфиденциальных данных) в ASP.NET и Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using SpeakersList.Models;
		using System.Data.Sql;
		using System.Data.SqlClient;
		
		namespace SpeakersList.Controllers
		{
		    public class SpeakersController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Speaker> Get()
		        {
					// Instantiate List object that will be populated and returned
					// from this method.
		            List<Speaker> speakers = new List<Speaker>();
	
					// Build database connection string.
		            string connectionString = "Server=<serverName>;" +
		                                      "Initial Catalog=LocalDatabase;" +
		                                      "User Id=sa;Password=<password>;" +
		                                      "Asynchronous Processing=true;";
		
					// Build query string to select all three columns from the Speakers table.
		            string queryString = "SELECT Id, EmailAddress, Name FROM dbo.Speakers;";

					// Instantiate the SqlConnection object using the connection string.
		            using (SqlConnection connection = new SqlConnection(connectionString))
		            {
						// Instantiate the SqlCommand object using the connection and query strings.
		                SqlCommand command = new SqlCommand(queryString, connection);

						// Open the connection to the database.
		                connection.Open();

						// Execute the command and return a SqlDataReader object
						// that can be iterated.
		                SqlDataReader reader = command.ExecuteReader();
		                try
		                {
							// Read a row from the SqlDataReader object
		                    while (reader.Read())
		                    {
								// For each row, use the returned data to instantiate
								// and add to the List a new Speaker object.
		                        speakers.Add(new Speaker 
		                            { 
		                                Id = Convert.ToInt32(reader[0]), 
		                                EmailAddress = reader[1].ToString(), 
		                                Name = reader[2].ToString() 
		                            }
		                        );
		                    }
		                }
		                finally
		                {
							// Close the SqlDataReader object.
		                    reader.Close();
		                }
		            }
					// Return the List of Speaker objects.
		            return speakers;
		        }
		    }
		}

### Включение пользовательского интерфейса Swagger

Включение пользовательского интерфейса Swagger позволит тестировать приложение API без необходимости писать клиентский код для его вызова.

1. Откройте файл *App\_Start/SwaggerConfig.cs* и найдите **EnableSwaggerUI**:

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-disabled.png)

2. Раскомментируйте следующие строки кода:

	        })
	    .EnableSwaggerUi(c =>
	        {

3. После завершения операции убедитесь, что файл соответствует приведенному ниже изображению.

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-enabled.png)

### Тестирование приложения API

1. Чтобы просмотреть тестовую страницу API, запустите приложение локально с помощью сочетания клавиш **&lt;Ctrl>F5**. Должна отобразиться ошибка, соответствующая изображенной ниже.

	![](./media/app-service-api-hybrid-on-premises-sql-server/error-forbidden.png)

2. В адресной строке браузера добавьте `/swagger` в конец URL-адреса и нажмите клавишу **&lt;ВВОД>**. Отобразится пользовательский интерфейс Swagger, включенный вами в предыдущем разделе.

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui.png)

3. Разверните щелчком раздел **Speakers**.

4. Разверните щелчком раздел **GET /api/speakers**.

5. Нажмите кнопку **Попробовать** и просмотрите данные, которые вы ранее внесли в базу данных.

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out.png)

### Развертывание приложения API

Теперь, когда приложение протестировано локально, пора развернуть его в Azure.

1. В **обозревателе решений** щелкните правой кнопкой мыши проект (не решение) и выберите пункт **Опубликовать...**. 

	![Пункт меню «Опубликовать проект»](./media/app-service-api-hybrid-on-premises-sql-server/publish-menu.png)

2. Откройте вкладку **Профиль** и щелкните элемент **Приложения API Microsoft Azure (предварительная версия)**.

	![Пункт меню «Опубликовать проект»](./media/app-service-api-hybrid-on-premises-sql-server/publish-web.png)

3. Нажмите кнопку **Создать**, чтобы подготовить новое приложение API в подписке Azure.

	![Диалоговое окно «Выбор существующих служб API»](./media/app-service-api-hybrid-on-premises-sql-server/publish-to-existing-api-app.png)

4. В диалоговом окне **Создание приложения API** введите следующее.

	- В поле **Имя приложения API** введите имя приложения. 
	- Если у вас несколько подписок Azure, выберите ту, которую хотите использовать.
	- В разделе **План службы приложений** выберите один из существующих планов или щелкните **Создать план службы приложений** и введите имя нового плана. 
	- В разделе **Группа ресурсов** выберите одну из существующих групп ресурсов или щелкните **Создать новую группу ресурсов** и введите имя. Имя должно быть уникальным; рассмотрите возможность использования имени приложения в качестве префикса и добавления персональных данных, например учетной записи Майкрософт (без символа «@»).  
	- В разделе **Уровень доступа** выберите **Доступно всем пользователям**. Этот параметр сделает API общедоступным, что подходит для этого учебника. Позднее вы сможете ограничить доступ на [портале предварительной версии Azure](https://portal.azure.com).
	- Выберите регион.

	Нажмите кнопку **ОК**, чтобы создать приложение API в выбранной подписке.

	![Диалоговое окно «Настройка веб-приложения Microsoft Azure»](./media/app-service-api-hybrid-on-premises-sql-server/publish-new-api-app.png)

5. Процесс может занять несколько минут, поэтому в Visual Studio отобразится окно с уведомлением об инициации процесса. Нажмите кнопку **ОК** в диалоговом окне подтверждения.

	![Подтверждающее сообщение запуска создания службы API](./media/app-service-api-hybrid-on-premises-sql-server/create-api-app-confirmation.png)

6. Когда в ходе подготовки в подписке Azure создается группа ресурсов и приложение API, Visual Studio отображает ход выполнения в окне **Действия службы приложений Azure**.

	![Уведомление о состоянии через окно действий службы приложений Azure](./media/app-service-api-hybrid-on-premises-sql-server/app-service-activity.png)

7. После подготовки приложения API щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите пункт **Опубликовать**, чтобы повторно открыть диалоговое окно публикации. Профиль публикации, созданный на предыдущем шаге, должен быть уже выбран. Нажмите кнопку **Опубликовать**, чтобы начать процесс развертывания.

	![Развертывание приложения API](./media/app-service-api-hybrid-on-premises-sql-server/publish2.png)

В окне **Действия службы приложений Azure** отображается ход развертывания вплоть до завершения процесса публикации.

## Создание гибридного подключения и службы BizTalk ##

1. В браузере перейдите на [портал предварительной версии Azure](https://portal.azure.com). 

2. Щелкните пункт **Просмотреть все** в левой части окна.

3. В колонке **Обзор** выберите **Приложения API**.

4. В колонке **Приложения API** найдите приложение API и щелкните его.

5. В колонке приложения API щелкните значение в группе **Узел приложения API**.
 
	![Колонка приложения API](./media/app-service-api-hybrid-on-premises-sql-server/api-app-blade-api-app-host.png)

6. Когда появится колонка **Узел приложения API**, прокрутите вниз до раздела **Сети** и щелкните **Гибридные подключения**.
	
	![Гибридные подключения](./media/app-service-api-hybrid-on-premises-sql-server/api-app-host-blade-hybrid-connections.png)
	
7. В колонке **Гибридные подключения** щелкните **Добавить** > **Создать гибридное подключение**.
	
8. На **выноске Создание гибридного подключения**:
	- в поле **Имя** укажите имя подключения;
	- в поле **Имя узла** введите имя компьютера главного компьютера SQL Server;
	- В поле **Порт** введите `1433` (порт по умолчанию для SQL Server).
	- Нажмите **Служба Biz Talk** и введите имя для службы BizTalk.
	
	![Создание гибридного подключения](./media/app-service-api-hybrid-on-premises-sql-server/create-biztalk-service.png)
		
9. Нажмите **ОК** дважды.

	По завершении процесса в области **Уведомления** вы увидите зеленую мигающую надпись **ВЫПОЛНЕНО**, а в колонке **Гибридные подключения** будет отображено новое гибридное подключение с состоянием **Не подключено**.
	
	![Созданное гибридное подключение](./media/app-service-api-hybrid-on-premises-sql-server/hybrid-not-connected-yet.png)
	
На этот момент вы завершили важную часть облачной инфраструктуры гибридных подключений. Далее вы создадите соответствующую локальную часть.

<a name="InstallHCM"></a>
## Установка локального диспетчера гибридных подключений для завершения подключения

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

Теперь, когда инфраструктура гибридных подключений создана, пора протестировать приложение.

<a name="CreateASPNET"></a>
## Тестирование готового приложения API в Azure

1. На портале предварительной версии Azure вернитесь к колонке узла приложения API и щелкните значение в разделе **URL-адрес**.
	
2. Когда страница узла приложения API отобразится в браузере, добавьте `/swagger` к URL-адресу в адресной строке браузера и нажмите клавишу **&lt;ВВОД>**.
	
3. Разверните щелчком раздел **Speakers**.

4. Разверните щелчком раздел **GET /api/speakers**.

5. Нажмите кнопку **Попробовать** и просмотрите данные, которые вы ранее внесли в базу данных.

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out-azure.png)
	
**Поздравляем!** Вы создали приложение API, которое выполняется в Azure и использует гибридное подключение для соединения с локальной базой данных SQL Server.

## См. также
[Обзор гибридных подключений](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Джош Твист (Josh Twist) представляет гибридные подключения (видео Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Обзор гибридных подключений](/services/biztalk-services/)

[Службы BizTalk: вкладки "Панель мониторинга", "Монитор", "Масштаб", "Настройка" и "Гибридные подключения"](../biztalk-dashboard-monitor-scale-tabs/)

[Создание реального облака с гибридным подключением с помощью простой переносимости приложений (видео Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Подключение к локальному SQL Server из мобильной службы Azure с помощью гибридных подключений](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)

[Подключение к локальному SQL Server с мобильных служб Azure с помощью гибридных подключений (видео Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[Общие сведения об удостоверениях ASP.NET](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

<!---HONumber=Nov15_HO3-->