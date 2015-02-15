#Создание базы данных MySQL в Azure

В этом руководстве показано, как с помощью [ClearDB] создать базу данных MySQL из [магазина Azure], а также как создать базу данных MySQL в качестве связанного ресурса при создании [веб-сайта Azure][waws] . [ClearDB] является поставщиком отказоустойчивых баз данных вида "база данных как услуга", и эта служба позволяет запускать базы данных MySQL, управлять ими в центрах обработки данных Azure и подключаться к ним из любого приложения.  

##Оглавление
* [Практическое руководство. Создание базы данных MySQL из магазина Azure](#CreateFromStore)
* [Практическое руководство. Создание базы данных MySQL в качестве связанного ресурса для веб-сайта Azure](#CreateForWebSite)

> [AZURE.NOTE] При создании базы данных MySQL в рамках процесса разработки веб-сайта можно создавать базу данных только как бесплатную. При создании базы данных MySQL из магазина Azure можно создать бесплатный вариант базы данных или выбрать один из платных вариантов.

<h2><a id="CreateFromStore"></a>Практическое руководство. Создание базы данных MySQL из магазина Azure</h2>

Для создания базы данных MySQL из [магазина Azure] выполните следующие действия.

1. Войдите на [портал управления Azure][portal].
2. Щелкните **+СОЗДАТЬ** в нижней части страницы и выберите **МАГАЗИН**.

	![Select add-on from store](./media/create-mysql-db/select-store.png)

3. Выберите **База данных ClearDB MySQL**, затем щелкните стрелку в нижней части окна.

	![Select ClearDB MySQL Database](./media/create-mysql-db/select-cleardb-mysql.png)

4. Выберите план, введите имя базы данных, выберите область, затем щелкните стрелку в нижней части окна.

	![Purchase MySQL database from store](./media/create-mysql-db/purchase-mysql.png)

5. Щелкните флажок, чтобы завершить приобретение.

	![Review and complete your purchase](./media/create-mysql-db/complete-mysql-purchase.png)

6. После создания базой данных можно управлять на вкладке **НАДСТРОЙКИ** портала управления.

	![Manage MySQL database in Azure portal](./media/create-mysql-db/manage-mysql-add-on.png)

7. Сведения о подключении к базе данных можно получить, щелкнув **СВЕДЕНИЯ О ПОДКЛЮЧЕНИИ** в нижней части страницы (как показано выше).

	![MySql connection information](./media/create-mysql-db/mysql-conn-info.png) 


<h2><a id="CreateForWebSite"></a>Практическое руководство. Создание базы данных MySQL в качестве связанного ресурса для веб-сайта Azure</h2>

Чтобы создать базу данных MySQL в качестве связанного ресурса при создании [веб-сайта Azure][waws], выполните следующие действия.

1. Войдите на [портал управления Azure][portal].
2. Щелкните **+СОЗДАТЬ** в нижней части страницы, затем выберите **СРЕДА ВЫПОЛНЕНИЯ ПРИЛОЖЕНИЙ**, **ВЕБ-САЙТ** и **СОЗДАНИЕ С БАЗОЙ ДАННЫХ**.

	![Create website with database](./media/create-mysql-db/custom_create.png)

3. Введите **URL-адрес** веб-сайта, выберите **РЕГИОН** веб-сайта и щелкните пункт **Создать новую базу данных MySQL** из раскрывающегося списка **БАЗА ДАННЫХ**. При необходимости можно заменить имя по умолчанию для строки подключения. Щелкните стрелку в нижней части страницы.

	![Provide website details](./media/create-mysql-db/provide-website-details.png) 

4. Укажите **ИМЯ** базы данных, выберите **РЕГИОН** базы данных (это должен быть такой же регион, как и для веб-сайта), примите условия использования ClearDB и установите флажок в нижней части окна.

	![Provide MySQL details](./media/create-mysql-db/provide-mysql-details.png)

5. После создания веб-сайта щелкните по имени сайта и перейдите к его панели мониторинга.

	![Go to web site dashboard](./media/create-mysql-db/go-to-website-dashboard.png)

6. Щелкните **НАСТРОИТЬ**.

	![Go to configure tab](./media/create-mysql-db/go-to-configure-tab.png)

7. Прокрутите вниз до раздела **Строки подключения** и щелкните **Показать строки подключения**. 

	![Show connection string](./media/create-mysql-db/show-conn-string.png)

8. Скопируйте строку подключения для использования в приложении.

	![Shown connection string](./media/create-mysql-db/shown-conn-string.png)

> [AZURE.NOTE] Строки подключения доступны для приложения веб-сайта по имени строки подключения. В приложениях .NET строки подключения доступны в объекте **connectionStrings**. В других языках программирования строки подключения доступны как переменные среды. Дополнительные сведения можно найти в статье [Настройка веб-сайтов][configure].

[ClearDB]: http://www.cleardb.com/
[waws]: /ru-ru/documentation/services/web-sites/
[Магазин Azure]: /ru-ru/gallery/store/
[portal]: http://manage.windowsazure.com
[configure]: ../web-sites-configure/

<!--HONumber=42-->
