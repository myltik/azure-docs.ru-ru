<properties title="Troubleshooting Azure Websites in Visual Studio" pageTitle="Устранение неполадок веб-сайтов Azure в Visual Studio" metaKeywords="устранение неполадок, отладка, azure, веб-сайт, трассировка, ведение журнала" description="Узнайте, как устранять неполадки в работе веб-сайта Azure с помощью удаленной отладки, трассировки и средств ведения журналов, которые встроены Visual Studio 2013." metaCanonical="" services="web-sites" documentationCenter=".NET" authors="tdykstra" manager="wpickett" solutions="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/13/2014" ms.author="tdykstra" />

# Устранение неполадок, связанных с веб-сайтами Azure, в Visual Studio

В этом учебнике показано, как с помощью инструментов Visual Studio можно провести отладку приложения, открытого на веб-сайте Azure, путем запуска в [режиме отладки](http://www.visualstudio.com/ru-ru/get-started/debug-your-app-vs.aspx) удаленно или путем просмотра журналов как самого приложения, так и веб-сервера.

Вы узнаете следующее:

* Какие функции управления сайтом Azure доступны в среде Visual Studio.
* Как использовать удаленное представление Visual Studio для быстрого изменения удаленного веб-сайта.
* Как удаленно запустить режим отладки во время выполнения проекта в Azure.
* Как создать журналы трассировки приложения и просматривать их непосредственно во время создания.
* Как просматривать журналы веб-сервера, включая подробные сообщения об ошибках и трассировку неудачно завершившихся запросов.
* Как отправлять диагностические журналы в учетную запись хранения Azure и просматривать их там.

Если у вас установлена Visual Studio Ultimate, вы также можете использовать [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) для отладки. В этом учебнике отсутствует описание IntelliTrace.

### Сегменты учебника

- [Предварительные требования](#prerequisites)
- [Конфигурация и управление сайтом](#sitemanagement)
- [Доступ к файлам веб-сайта в обозревателе сервера](#remoteview)
- [Удаленная отладка](#remotedebug)
	- Удаленная отладка веб-сайтов
	- Удаленная отладка веб-заданий
	- Примечания о режиме удаленной отладки 
- [Обзор журналов диагностики](#logsoverview)
- [Создание и просмотр журналов трассировки приложения](#apptracelogs)
- [Просмотр журналов веб-сервера](#webserverlogs)
- [Просмотр подробных журналов сообщений об ошибках](#detailederrorlogs)
- [Загрузка журналов файловой системы](#downloadlogs)
- [Просмотр журналов хранилища](#storagelogs)
- [Просмотр журналов неудачно завершенных запросов](#failedrequestlogs)
- [Дальнейшие действия](#nextsteps)

<h2><a name="prerequisites"></a>Предварительные требования</h2>

Этот учебник работает со средой разработки, веб-проектом и веб-сайтом Azure, который вы настроили в учебнике [Приступая к работе с Azure и ASP.NET][GetStarted]. Для разделов веб-заданий вам понадобится приложение, созданное в учебнике [Приступаем к работе с пакетом SDK для Azure WebJobs][GetStartedWJ].

В этом учебнике приводятся примеры кода для веб-приложения C# MVC, однако в приложениях Visual Basic и веб-форм применяются те же процедуры устранения неполадок.

Для удаленной отладки требуется Visual Studio 2013 или Visual Studio 2012 с обновлением 4. Для функций удаленной отладки и **обозревателя сервера** для WebJobs требуется [Visual Studio 2013 с обновлением 4](http://go.microsoft.com/fwlink/?LinkID=510314) или более поздней версии. Другие функции, описанные в учебнике, также работают в Visual Studio 2013 Express для Web и в Visual Studio 2012 Express для Web. 

Журналы потоковой передачи работают только для приложений, которые предназначены для .NET Framework 4 и выше.

<h2><a name="sitemanagement"></a>Конфигурация и управление сайтом</h2>

Visual Studio обеспечивает доступ к подмножеству функций управления веб-сайтом и параметров конфигурации, доступных на портале управления. Они будут указаны в этом разделе.

1. Если вы еще не вошли в Azure в Visual Studio, нажмите кнопку **Подключиться к Azure** в **обозревателе сервера**.

	Также вы можете установить сертификат управления, обеспечивающий доступ к вашей учетной записи. Сертификат управления предоставляет **обозревателю сервера** доступ к дополнительным службам Azure (базе данных SQL и мобильным службам). Чтобы установить сертификат, щелкните правой кнопкой мыши узел **Azure** в **обозревателе сервера** и выберите пункт **Управление подписками** в контекстном меню. В диалоговом окне **Управление подписками Azure** откройте вкладку **Сертификаты**, а затем щелкните **Импорт**. Следуя указаниям, загрузите и импортируйте файл подписки (называемый также файлом *.publishsettings*) для вашей учетной записи Azure.

	> [WACOM.NOTE]
	> Если вы загружаете файл подписки, сохраните его в папку, не входящую в структуру каталогов с исходными кодами (например, в папку "Загрузки"), а затем удалите его сразу после завершения импорта. Злоумышленник, получивший доступ к файлу подписки, сможет изменять, создавать и удалять ваши службы Azure.

	Дополнительные сведения о подключении к ресурсам Azure из Visual Studio см. в разделе [Управление учетными записями, подписками и административными ролями](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).

2. В **обозревателе сервера** разверните узел **Azure** и затем узел **Веб-сайты**.

3. Щелкните правой кнопкой мыши узел веб-сайта, созданного в разделе [Приступая к работе с Azure и ASP.NET][GetStarted], и выберите пункт **Просмотреть параметры**.

	![View Settings in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

	Появится вкладка **Веб-сайт Azure**, где можно просмотреть задачи по управлению и конфигурации, доступные в Visual Studio.

	![Azure Website window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

	В этом учебнике используются раскрывающиеся списки ведения журналов и трассировки.	Для удаленной отладки вы будете использовать другой метод.
   
	Информацию о полях параметров приложения и строк подключения в этом окне см. в разделе [Веб-сайты Azure: как работают строки приложения и строки подключения](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx).

	Если вам необходимо выполнить задачу по управлению веб-сайтом, которую невозможно выполнить в этом окне, можете щелкнуть пункт **Полные параметры веб-сайта**, чтобы открыть в окне браузера портал управления. Дополнительные сведения см. в статье [Настройка веб-сайтов](/ru-ru/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

<h2><a name="remoteview"></a>Доступ к файлам веб-сайта в обозревателе сервера</h2>

Как правило, при развертывании сайта флагу customErrors в файле Web.config присваивается значение On (Вкл) или RemoteOnly (Только удаленно), из-за которого вы не видите сообщения о возникающих ошибках. В большинстве случаев при ошибке отображается страница следующего вида.

**Ошибка сервера в приложении "/":**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Произошла ошибка:**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Не удается отобразить страницу веб-сайта**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Подробное сообщение об ошибке, как правило, содержит полезную информацию для ее устранения. На предыдущих снимках экрана показано, как включить эту функцию. Для этого необходимо внести изменения в развернутый файл Web.config. Можно изменить файл *Web.config* в проекте и повторно развернуть проект либо создать [преобразование Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) и развернуть отладочную сборку. Однако вы можете значительно упростить этот процесс: с помощью **обозревателя решений**, напрямую просматривая и редактируя файлы на удаленном сайте с использованием функции *удаленное представление*.

1. В **обозревателе сервера** разверните узел **Azure**, затем узел **Веб-сайты** и узел веб-сайта, на который вы выполняете развертывание.

	Откроются узлы, обеспечивающие доступ к файлам содержимого и журналов веб-сайта.

	![File and log files](./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png)

2. Разверните узел **Файлы** и дважды щелкните файл *Web.config*.

	![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

	В Visual Studio откроется файл Web.config удаленного сайта, а в строке заголовка к его имени будет добавлен суффикс [Удаленный].

3. Добавьте следующую строку в элемент system.web:

	`<customErrors mode="off"></customErrors>`

	![Edit Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4. Обновите окно браузера, в котором отображается неинформативное сообщение. Вы увидите подробную информацию об ошибке, как в следующем примере:

	![Detailed error messaeg](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

	(Для создания отображаемой ошибки в файл *Views\Home\Index.cshtml* была добавлена показанная красным строка.)

Изменение файла Web.config является одним из нескольких способов упростить устранение неполадок путем считывания и редактирования файлов на вашем веб-сайте Azure.

<h2><a name="remotedebug"></a>Удаленная отладка</h2>

Если в подробном сообщении об ошибке содержится недостаточно сведений и вы не можете воспроизвести ошибку в локальной системе, попробуйте выполнить отладку в удаленном режиме. В этом режиме вы можете задавать точки останова, обращаться к памяти напрямую, осуществлять пошаговое выполнение кода и даже изменять путь кода. 

Удаленная отладка не поддерживается в выпусках Visual Studio Express.

### Удаленная отладка веб-сайтов

В этом разделе демонстрируется удаленная отладка с использованием проекта, созданного в разделе [Приступая к работе с Azure и ASP.NET][GetStarted].

1. Откройте веб-проект, созданный в разделе [Приступая к работе с Azure и ASP.NET][GetStarted].

1. Откройте *Controllers\HomeController.cs*.

2. Удалите метод `About()` и вставьте вместо него следующий код.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

2. [Задайте точку останова](http://www.visualstudio.com/ru-ru/get-started/debug-your-app-vs.aspx) в строке ViewBag.Message.

1. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите пункт **Опубликовать**.

2. В раскрывающемся списке **Профиль** выберите тот же профиль, который вы использовали в разделе [Приступая к работе с Azure и ASP.NET][GetStarted].

3. Откройте вкладку **Настройки** и измените значение **Конфигурация** на **Отладка**, затем выберите пункт **Опубликовать**.

	![Publish in debug mode](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

4. После завершения развертывания и открытия URL-адреса вашего сайта Azure закройте браузер.

5. Для Visual Studio 2013: в **Обозревателе сервера** разверните пункт **Azure**, разверните **Веб-сайты**, щелкните правой кнопкой мыши веб-сайт и выберите пункт **Подключить отладчик**. 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

	В браузере автоматически откроется ваша главная страница, размещенная в Azure. Возможно, придется подождать около 20 секунд, пока Azure настраивает сервер для отладки. Эта задержка возникает только при первом выполнении в режиме отладки на веб-сайте. При последующих отладках в течение 48 часов задержки не будет.

6. Для Visual Studio 2012 с обновлением 4:<a id="vs2012"></a>

	* На портале управления Azure перейдите на вкладку **Настройка** для вашего веб-сайта, затем прокрутите вниз до раздела **Диагностика сайта**.

	* Установите для параметра **Удаленная отладка** значение **Включено**, а для параметра **Удаленная отладка: версия Visual Studio** - значение **2012**.

	![Set remote debugging in management portal](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png)
   
	* В меню Visual Studio **Отладка** щелкните пункт **Присоединение к процессу**.

	* В поле **Квалификатор** введите URL-адрес для вашего веб-сайта без префикса http://. 

	* Выберите пункт **Отображать процессы всех пользователей**.

	* Когда появится запрос на ввод учетных данных, введите имя и пароль пользователя, который имеет разрешения на публикацию веб-сайта. Чтобы получить эти учетные данные, перейдите на вкладку "Панель мониторинга" для вашего веб-сайта на портале управления и щелкните пункт **Загрузить профиль публикации**. Откройте файл в текстовом редакторе; имя и пароль пользователя будут располагаться после первого вхождения соответственно **userName=** и **userPWD=**. 

	* Когда процессы появятся в таблице **Доступные процессы**, выберите **w3wp.exe** и щелкните **Присоединить**.

	* Откройте URL-адрес своего сайта в браузере.

	Возможно, придется подождать около 20 секунд, пока Azure настроит сервер для отладки. Эта задержка возникает только при первом выполнении в режиме отладки на веб-сайте. При последующих отладках в течение 48 часов задержки не будет.

6. Снова выберите пункт **О программе** в меню.

	Visual Studio прерывается в точке останова, при этом код выполняется в Azure, а не на локальном компьютере.

7. Наведите указатель мыши на переменную `currentTime`, чтобы просмотреть значение времени.

	![View variable in debug mode running in Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

	Время, которое отображается здесь, является временем сервера Azure, который может находиться в другом часовом поясе, отличном от часового пояса локального компьютера.

8. Введите новое значение переменной currentTime, например "Теперь выполняется в Azure".

5. Нажмите клавишу F5, чтобы продолжить работу.

	На странице "О программе" в Azure отображается новое значение, введенное в переменной currentTime.

	![About page with new value](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

### <a name="remotedebugwj"></a> Удаленная отладка веб-заданий

В этом разделе демонстрируется удаленная отладка с использованием проекта, созданного в разделе [Приступая к работе с пакетом SDK для Azure WebJobs](../websites-dotnet-webjobs-sdk). Компоненты, приведенные в этом разделе, доступны только в Visual Studio 2013 с обновлением 4.

1. Откройте веб-проект, созданный в разделе [Приступая к работе с пакетом SDK для Azure WebJobs][GetStartedWJ].

1. В проекте ContosoAdsWebJob откройте *Functions.cs*.

2. [Установите точку останова](http://www.visualstudio.com/ru-ru/get-started/debug-your-app-vs.aspx) на первой инструкции в методе GnerateThumbnail.

	![Set breakpoint](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

1. В **обозревателе решений** щелкните правой кнопкой мыши проект (не проект веб-задания) и выберите **Опубликовать**.

2. В раскрывающемся списке **Профиль** выберите тот же профиль, который вы использовали в учебнике [Приступая к работе с пакетом SDK для Azure WebJobs](../websites-dotnet-webjobs-sdk).

3. Откройте вкладку **Настройки** и измените значение **Конфигурация** на **Отладка**, затем выберите пункт **Опубликовать**.

	Visual Studio развернет веб-проект и проект веб-задания, и в браузере откроется URL-адрес Azure вашего сайта.

5. В **обозревателе сервера** разверните **Azure** > **Веб-сайты** > ваш веб-сайт > **Веб-задания** > **Непрерывно**, а затем щелкните правой кнопкой мыши **ContosoAdsWebJob**.

7. Щелкните **Подключить отладчик**. 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

	В браузере автоматически откроется ваша главная страница, размещенная в Azure. Возможно, придется подождать около 20 секунд, пока Azure настраивает сервер для отладки. Эта задержка возникает только при первом выполнении в режиме отладки на веб-сайте. В следующий раз задержка не возникнет при подключении отладчика в течение 48 часов.

6. В веб-браузере с открытой главной страницей Contoso Ads создайте новое рекламное объявление. 

	При создании рекламного объявления будет создано сообщение очереди, которое будет подобрано веб-заданием и обработано. При вызове функции обработки сообщения из очереди с помощью пакета WebJobs SDK код достигнет вашей точки останова.

7. После прерывания точки останова с помощью отладчика вы можете проверить и изменить значения переменных, пока программа работает в облаке. На следующем рисунке отладчик отображает содержимое объекта blobInfo, который передан в метод GenerateThumbnail.

	![blobInfo object in debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)
 
5. Нажмите клавишу F5, чтобы продолжить работу.

	Метод GenerateThumbnail завершает создание эскиза.

6. В браузере обновите страницу индекса и посмотрите эскиз.

6. В Visual Studio нажмите сочетание клавиш SHIFT+F5, чтобы остановить отладку.

7. В **обозревателе сервера** щелкните правой кнопкой мыши узел ContosoAdsWebJob и нажмите кнопку **Представление панели мониторинга**.

8. Выполните вход с помощью учетных данных Azure и щелкните имя веб-задания, чтобы перейти к странице вашего веб-задания. 

	![Click ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

	Панель мониторинга показывает, что функция GenerateThumbnail выполнена недавно.

	(В следующий раз при нажатии кнопки **Представление панели мониторинга** не нужно выполнять вход, браузер перейдет непосредственно на страницу вашего веб-задания.)

9. Щелкните имя функции для просмотра подробных сведений о выполнении функции.

	![Function details](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Если функция [записала журналы](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs), для их просмотра щелкните пункт **ToggleOutput**.

### Примечания о режиме удаленной отладки

*  В рабочей среде выполнение в режиме отладки не рекомендуется. Если производственный сайт не масштабирован до нескольких экземпляров сервера, отладка будет препятствовать веб-серверу отвечать на другие запросы. Если имеется несколько экземпляров веб-сервера, при присоединении отладчика будет использован случайно выбранный экземпляр и не будет никаких возможностей убедиться в том, что запросы браузера передаются именно на этот экземпляр. Кроме того, как правило, отладочная сборка не развертывается в производственной среде, а оптимизации компилятора для разворачиваемых сборок делают невозможным построковое отображения действий в исходном коде. Для устранения неполадок в рабочей среде лучше всего использовать трассировку приложения и журналы веб-сервера.

*  Во время удаленной отладки избегайте длинных остановок на точках останова. Azure обрабатывает процесс, остановленный дольше, чем на несколько минут, как процесс, не отвечающий на запросы, и закрывает его.

*  Во время отладки сервер отправляет данные в среду Visual Studio, что может повлиять на расходы, связанные с пропускной способностью. Сведения о тарифах на пропускную способность см. в разделе [Цены на Azure](/ru-ru/pricing/calculator/).

* Убедитесь, что атрибут debug элемента compilation в файле *Web.config* имеет значение true. По умолчанию при публикации отладочной конфигурации сборки для него задано значение true.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

*  Если вы обнаружите, что отладчик не выполняет пошаговое прохождение кода, который следует отладить, возможно необходимо изменить параметр пошаговую отладку кода, который требуется отладить, может потребоваться изменить параметр "Только мой код".  Дополнительные сведения см. в разделе [Ограничение выполнения шагов только моим кодом](http://msdn.microsoft.com/ru-ru/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).

*  При активации функции удаленной отладки на сервере запускается таймер, который автоматически отключает эту функцию по истечении 48 часов. Это 48-часовое ограничение существует в целях повышения безопасности и производительности. Вы можете в любое время снова активировать эту функцию. Если вы не ведете отладку, эту функцию рекомендуется оставить отключенной.

* Можно вручную присоединить отладчик к любому процессу, а не только к процессу веб-сайта (w3wp.exe). Дополнительные сведения о том, как использовать режим отладки в Visual Studio, см. в разделе [Отладка в Visual Studio](http://msdn.microsoft.com/ru-ru/library/vstudio/sc65sadd.aspx).

<h2><a name="logsoverview"></a>Обзор журналов диагностики</h2>

Приложения ASP.NET, которое работает на веб-сайте Azure, может создавать следующие виды журналов:

* **Журналы трассировки приложения**<br/>
  Приложение создает эти журналы, вызывая методы класса [System.Diagnostics.Trace](http://msdn.microsoft.com/ru-ru/library/system.diagnostics.trace.aspx).
* **Журналы веб-сервера**<br/>
  Веб-сервер создает запись журнала для каждого HTTP-запроса, адресованного сайту.
* **Подробные журналы сообщений об ошибках**<br/>
  Веб-сервер создает страницу HTML с некоторыми дополнительными сведениями об ошибочных HTTP-запросах (те запросы, которые приводят к появлению кода состояния 400 и выше). 
* **Журналы трассировки неудачно завершенных запросов**<br/>
  Веб-сервер создает XML-файл с подробными сведениями трассировки ошибочных HTTP-запросов. Веб-сервер также предоставляет XSL-файл для форматирования XML-кода в браузере.
  
Ведение журнала влияет на производительность веб-сайта, поэтому Azure предоставляет возможность при необходимости включить или отключить каждый тип журнала. Для журналов приложений можно указать, что следует писать только журналы с уровнем серьезности выше заданного. При создании нового веб-сайта все ведение журналов по умолчанию отключено.

Журналы записываются в файлы, содержащиеся в папке *LogFiles* файловой системы вашего веб-сайта, и доступны через FTP. Журналы приложений и журналы веб-сервера также могут записываться в учетную запись хранения Azure. В учетной записи хранения можно держать больший объем журналов, чем в файловой системе. При использовании файловой системы на журналы отводится 100 мегабайтов. Журналы файловой системы хранятся в течение короткого срока. При достижении установленного ограничения Azure удаляет старые файлы журналов, чтобы освободить место под новые.  

<h2><a name="apptracelogs"></a>Создание и просмотр журналов трассировки приложения</h2>

В этом разделе предстоит выполнить следующие задачи:

* Добавьте инструкции трассировки в веб-проект, созданный в разделе [Приступая к работе с Azure и ASP.NET][GetStarted].
* Просмотр журналов при локальном выполнении проекта.
* Просмотр журналов, создаваемых во время работы приложения в Azure. 

Сведения о том, как создавать журналы приложений в веб-заданиях, см. в разделе [Работа с хранилищем очередей Azure с помощью пакета SDK веб-заданий - создание записей в журналах](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs). Следующие инструкции для просмотра журналов и управления их хранением в Azure также применяются для журналов приложений, созданных с помощью Azure. 

### Добавление инструкций трассировки в приложение

1. Откройте *Controllers\HomeController.cs* и замените содержимое файла на следующий код, чтобы добавить инструкции Trace и Using для System.Diagnostics:

		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.Diagnostics;
		using System.Linq;
		using System.Web;
		using System.Web.Configuration;
		using System.Web.Mvc;
		namespace MyExample.Controllers
		{
		    public class HomeController : Controller
		    {
		        public ActionResult Index()
		        {
		            Trace.WriteLine("Вход в метод Index");
		            ViewBag.Message = "Измените этот шаблон для быстрого запуска приложения ASP.NET MVC.";
		            Trace.TraceInformation("Отображение страницы индекса на " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Выход из метода Index");
		            return View();
		        }
		
		        public ActionResult About()
		        {
		            Trace.WriteLine("Вход в метод About");
		            ViewBag.Message = "Страница описания вашего приложения.";
		            Trace.TraceWarning("Временная ошибка на странице о программе в " + DateTime.Now.ToShortTimeString());
		            Trace.WriteLine("Выход из метода About");
		            return View();
		        }
		
		        public ActionResult Contact()
		        {
		            Trace.WriteLine("Вход в метод Contact");
		            ViewBag.Message = "Ваша страница контактов.";
		            Trace.TraceError("Неустранимая ошибка на странице контактов в " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Выход из метода Contact");
		            return View();
		        }
		    }
		}
		
				
### Просмотр результатов трассировки локально

3. Нажмите F5, чтобы выполнить приложение в режиме отладки.

	Прослушиватель трассировки по умолчанию записывает весь вывод трассировки в окно **Вывод** вместе со всем остальным выводом отладки. На следующем рисунке показан вывод инструкций трассировки, добавленных в метод Index.

	![Tracing in Debug window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

	Ниже показано, как просмотреть выходные данные трассировки на веб-странице без компиляции в режиме отладки.

2. Откройте файл Web.config приложения (находится в папке проекта) и добавьте элемент <system.diagnostics> в конец файла непосредственно перед закрывающим элементом </configuration>.

  		<system.diagnostics>
		    <trace>
		      <listeners>
		        <add name="WebPageTraceListener"
                    type="System.Web.WebPageTraceListener, 
                    System.Web, 
                    Version=4.0.0.0, 
                    Culture=neutral,
                    PublicKeyToken=b03f5f7f11d50a3a" />
		      </listeners>
		    </trace>
		  </system.diagnostics>

	`WebPageTraceListener` позволяет просматривать результаты трассировки, открыв `/trace.axd`.

3. Добавьте <a href="http://msdn.microsoft.com/ru-ru/library/vstudio/6915t83k(v=vs.100).aspx">элемент трассировки</a> под строкой <system.web> в файле Web.config, например следующим образом.

		<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

3. Для запуска приложения нажмите сочетание клавиш CTRL+F5.

4. В адресной строке окна браузера добавьте *trace.axd* к URL-адресу и нажмите клавишу "ВВОД" (URL-адрес должен быть аналогичен http://localhost:53370/trace.axd).

5. На странице **Трассировка приложения** щелкните **Просмотр сведений** в первой строке (не строке BrowserLink).

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

	Откроется страница **Сведения о запросе**, где в разделе **Сведения о трассировке** отображаются выходные данные инструкций трассировки, добавленных в метод Index.

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

	По умолчанию trace.axd доступен только локально. Если он должен быть доступен также на удаленном сайте, можно добавить в элемент trace файла *Web.config* строку localOnly="false", как показано в следующем примере.

		<trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

	Однако включение trace.axd на рабочем сайте, как правило, не рекомендуется по соображениям безопасности, и в следующих разделах будет приведен более простой способ чтения журналов трассировки веб-сайта Azure.

### Просмотр результатов трассировки в Azure

1. В **обозревателе решений** щелкните правой кнопкой веб-проект и выберите пункт **Опубликовать**.

2. В диалоговом окне **Публикация веб-сайта** щелкните **Опубликовать**.

	После того как Visual Studio опубликует обновление, главная страница откроется в окне браузера (если не снят флажок **URL-адрес назначения** на вкладке **Подключение**).

3. В **обозревателе сервера** щелкните правой кнопкой мыши веб-сайт и выберите **Просмотр журналов потоковой передачи**. 

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

	В окне **Вывод** показано, что вы подключены к службе потоковой передачи журналов; каждую минуту, когда отсутствует журнал для отображения, появляется строка уведомления.

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4. В окне браузера с открытой главной страницей щелкните пункт **Контакт**.

	Через несколько секунд результаты трассировки уровня ошибки, добавленные в метод Contact, появятся в окне **Вывод**.

	![Error trace in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

	Visual Studio отображает только трассировки уровня ошибок, так как это настройка выставляется по умолчанию при включении службы мониторинга журналов. При создании нового веб-сайта Azure ведение всех журналов по умолчанию отключено, что вы видели ранее при открытии страницы параметров сайта.

	![Application Logging off](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)


	Однако при выборе **Просмотр журналов потоковой передачи** Visual Studio автоматически изменяет значение параметра **Ведение журнала приложений (файловая система)** на **Ошибка**, что означает представление только журналов уровня ошибок. Чтобы просмотреть все журналы трассировки, следует изменить значение этого параметра на **Подробно**. Если выбран уровень серьезности меньше уровня ошибок, начинают предоставляться и все журналы с более высокими уровнями серьезности. Поэтому при выборе подробного уровня вы также сможете просматривать журналы информационных сообщений, предупреждений и ошибок.  

4. В **обозревателе сервера **щелкните правой кнопкой мыши веб-сайт, затем щелкните **Просмотреть параметры**, как и раньше.

5. Измените значение параметра **Ведение журнала приложений (файловая система)** на **Подробно**, затем нажмите кнопку **Сохранить**.
 
	![Setting trace level to Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

6. В окне браузера, в котором теперь отображается страница **Контакт**, нажмите кнопку **Главная**, затем выберите **О программе** и щелкните **Контакт**.

	Через несколько секунд в окне **Вывод** появятся все результаты трассировки.

	![Verbose trace output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

	В этом разделе вы включили и отключили ведение журналов с помощью параметров веб-сайта Azure. Можно также включить и отключить прослушиватели трассировки, изменив содержимое файла Web.config. Однако изменение файла Web.config приводит к перезапуску домена приложения, тогда как включение ведения журналов через веб-сайт не приводит к аналогичным результатам. Если воспроизведение проблемы занимает много времени или проблема возникает только периодически, перезапуск домена приложения может устранить неполадку, так что вам придется ждать следующего ее возникновения. Включение диагностики в Azure не приводит к подобным результатам, поэтому сбор сведений об ошибках можно начать незамедлительно.

### Функции окна вывода

На вкладке **Журналы Azure** окна **Вывод** содержатся несколько кнопок и текстовое поле:

![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Служат для выполнения следующих функций:

* Очистка окна **Вывод**.
* Включение или отключение переноса слов.
* Запуск или остановка журналов мониторинга.
* Определение журналов мониторинга.
* Скачивание журналов.
* Фильтрация журналов на основе регулярного выражения или строки поиска.
* Закрытие окна **Вывод**.

Если ввести строку поиска или регулярное выражение, Visual Studio фильтрует сведения в журналах на стороне клиента. Это означает, что можно ввести критерии после отображения журналов в окне **Вывод**, а также изменить критерии фильтрации без необходимости повторного создания журналов.

<h2><a name="webserverlogs"></a>Просмотр журналов веб-сервера</h2>

Журналы веб-сервера регистрируют все действия по протоколу HTTP на сайте. Чтобы увидеть их в окне **Вывод**, следует включить их на сайте и оповестить Visual Studio о необходимости их мониторинга. 

1. На вкладке **Конфигурация веб-сайта Azure**, открытой в окне **Обозреватель сервера**, измените значение параметра "Ведение журналов веб-сервера" на **Включено** и щелкните **Сохранить**.

	![Enable web server logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2. В окне **Вывод** нажмите кнопку **Укажите, какие журналы Azure отслеживать**.
	
	![Specify which Azure logs to monitor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3. В диалоговом окне **Параметры ведения журналов Azure** выберите **Журналы веб-сервера** и нажмите кнопку **ОК**.

	![Monitor web server logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4. В окне браузера, в котором отображается веб-сайт, щелкните **Главная**, затем **О программе** и, наконец, **Контакт**.

	Как правило, первыми отображаются журналы приложений, за которыми следуют журналы веб-сервера. Подождите некоторое время, чтобы дождаться отображения журналов. 

	![Web server logs in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)


По умолчанию при первом включении журналов веб-сервера с помощью Visual Studio Azure записывает журналы в файловой системе. В качестве альтернативы можно использовать портал управления для указания, что журналы веб-сервера должны записываться в контейнер blob, содержащийся в учетной записи хранения. Дополнительную информацию см. в разделе **Диагностика сайта** статьи [Настройка веб-сайтов](/ru-ru/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig). 

If you use the management portal to enable web server logging to an Azure storage account, and then disable logging in Visual Studio, when you re-enable logging in Visual Studio your storage account settings are restored. 

<h2><a name="detailederrorlogs"></a>View detailed error message logs</h2>

Detailed error logs provide some additional information about HTTP requests that result in error response codes (400 or above). In order to see them in the **Output** window, you have to enable them on the site and tell Visual Studio that you want to monitor them.

1. In the **Azure Website Configuration** tab that you opened from **Server Explorer**, change **Detailed Error Messages** to **On**, and then click **Save**.

	![Enable detailed error messages](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. In the **Output** Window, click the **Specify which Azure logs to monitor** button.

3. In the **Azure Logging Options** dialog box, click **All logs**, and then click **OK**.

	![Monitor all logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. In the address bar of the browser window, add an extra character to the URL to cause a 404 error (for example, `http://localhost:53370/Home/Contactx`), and press Enter.

	After several seconds the detailed error log appears in the Visual Studio **Output** window.

	![Detailed error log in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

	Control+click the link to see the log output formatted in a browser:

	![Detailed error log in browser window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

<h2><a name="downloadlogs"></a>Download file system logs</h2>

Any logs that you can monitor in the **Output** window can also be downloaded as a *.zip* file. 

1. In the **Output** window, click **Download Streaming Logs**.

	![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

	File Explorer opens to your *Downloads* folder with the downloaded file selected.

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2. Extract the *.zip* file, and you see the following folder structure:

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

	* Application tracing logs are in *.txt* files in the *LogFiles\Application* folder.
	* Web server logs are in *.log* files in the *LogFiles\http\RawLogs* folder. You can use a tool such as [Log Parser](http://www.microsoft.com/ru-ru/download/details.aspx?displaylang=en&id=24659) to view and manipulate these files.
	* Detailed error message logs are in *.html* files in the *LogFiles\DetailedErrors* folder.

	(The *deployments* folder is for files created by source control publishing; it doesn't have anything related to Visual Studio publishing. The *Git* folder is for traces related to source control publishing and the log file streaming service.)  

<h2><a name="storagelogs"></a>View storage logs</h2>

Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the management portal, and view them in the **Logs** tab of the **Azure Website** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination. 

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

5. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

3. In the **Create Storage Account** dialog, enter a name for the storage account. 

	The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

	The URL to access your storage account will be *{name}*.core.windows.net. 

5. Set the **Region or Affinity Group** drop-down list to the region closest to you.

	This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production site you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The website (which you'll create later) should be as close as possible to the browsers accessing your site in order to minimize latency.

6. Set the **Replication** drop-down list to **Locally redundant**. 

	When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [How To Manage Storage Accounts](/ru-ru/documentation/articles/storage-manage-storage-account/).

5. Щелкните **Создать**. 

	![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)	

1. В окне Visual Studio **Веб-сайт Azure** перейдите на вкладку **Журналы**, а затем щелкните **Настроить ведение журналов на портале управления**.

	![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

	Это приведет к открытию вкладки **Настройка** на портале управления вашим веб-сайтом. Другим способом открытия этой вкладки является переход на вкладку **Веб-сайты** с последующим выбором вкладки **Настройка**.

2. На вкладке портала управления **Настройка** прокрутите вниз до раздела диагностики приложения и задайте для параметра **Ведение журнала приложений (табличное хранилище)** значение **Включено**.

3. Измените значение параметра **Уровень ведения журнала** на **Сведения**.

4. Щелкните **Управление табличным хранилищем**.

	![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

	В поле **Управление табличным хранилищем для диагностики приложений** можно выбрать учетную запись хранения, если у вас их несколько. Вы можете как создать новую, так и использовать существующую таблицу.

	![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

6. В поле **Управление табличным хранилищем для диагностики приложений** щелкните флажок, чтобы закрыть окно.

6. На вкладке **Настройка** портала управления щелкните **Сохранить**.

7. В окне браузера, в котором отображается веб-сайт, щелкните **Главная**, затем **О программе** и, наконец, **Контакт**.

	Ведение журнала сведений, полученных при просмотре веб-страниц, будут записаны в учетную запись хранения.

8. На вкладке **Журналы** окна **Веб-сайт Azure** в Visual Studio щелкните **Обновить** в разделе **Сводка по диагностике**.

	![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

	В разделе **Сводка по диагностике** по умолчанию отображаются журналы за последние 15 минут. Можно изменить период для просмотра дополнительных журналов. 

	(При появлении ошибки "Таблица не найдена" убедитесь, что вы перешли к страницам, которые выполняют трассировку, после включения параметра **Ведение журналов приложения (хранилище)** и нажатия кнопки **Сохранить**.)

	![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

	Обратите внимание, что в этом представлении отображаются **Идентификатор процесса** и **Идентификатор потока** для каждого журнала, что отсутствует в журналах файловой системы. Дополнительные поля можно увидеть, напрямую просмотрев таблицу хранилища Azure.

8. Щелкните **Просмотреть все журналы приложений**.

	Таблица журнала трассировки отображается в средстве просмотра таблицы хранилища Azure.
   
	(Если возникает ошибка "последовательность не содержит элементов", откройте **обозреватель сервера**, разверните узел для вашей учетной записи хранения в узле **Azure**, а затем щелкните правой кнопкой мыши пункт **Таблицы** и нажмите кнопку **Обновить**.)

	![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png)

	![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

	Это представление показывает дополнительные поля, которые не отображаются в других представлениях. Это представление также позволяет фильтровать журналы, используя для создания запроса специальный пользовательский интерфейс построителя запросов. Дополнительные сведения см. в разделе "Работа с ресурсами таблицы - фильтрация объектов" учебника [Обзор ресурсов хранилища с помощью обозревателя сервера](http://msdn.microsoft.com/ru-ru/library/windowsazure/ff683677.aspx).

7. Чтобы просмотреть подробные сведения по отдельной строке, дважды щелкните одну из строк.

	![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

<h2><a name="failedrequestlogs"></a>Просмотр журналов неудачно завершенных запросов</h2>

Журналы трассировки неудачно завершенных запросов полезны при просмотре подробных сведений по обработке сервером IIS HTTP-запроса в таких сценариях, как перезапись URL-адреса, или при возникновении проблем, связанных с проверкой подлинности. 

Веб-сайты Azure используют те же функциональные возможности трассировки неудачно завершенных запросов, которые доступны в IIS, начиная с версии 7.0. Однако у вас нет доступа к параметрам IIS, которые определяют, какие ошибки будут регистрироваться. При включении трассировки неудачно завершенных запросов захватываются все ошибки. 

Можно включить трассировку неудачно завершенных запросов с помощью Visual Studio, однако эту трассировку невозможно просмотреть в Visual Studio. Эти журналы представляют собой XML-файлы. Служба потоковой передачи журналов только отслеживает файлы, которые могут быть прочтены в режиме обычного текста: это  файлы с расширением *.txt*, *.html* и *.log*.

Журналы трассировки неудачно завершенных запросов можно просматривать напрямую в браузере через FTP или локально после загрузки их на локальный компьютер с помощью средства для работы с FTP. В этом разделе они будут просматриваться непосредственно в браузере.

1. На вкладке **Конфигурация** окна **Веб-сайт Azure**, которое было открыто в окне **Обозреватель сервера**, измените значение параметра **Трассировка неудачно завершенных запросов** на **Включено**, затем щелкните **Сохранить**.

	![Enable failed request tracing](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

4. В адресной строке окна браузера, в котором отображается веб-сайт, добавьте дополнительный символ в URL-адрес, чтобы вызвать ошибку 404, и нажмите клавишу ВВОД.

	Это приведет к созданию журнала трассировки неудачно завершенных запросов. Ниже приведены действия, демонстрирующие, как просмотреть или загрузить журнал.

2. На вкладке Visual Studio **Настройка** окна **Веб-сайт Azure** щелкните **Открыть на портале управления**.

3. На портале управления щелкните **Панель мониторинга**, затем щелкните **Сброс учетных данных развертывания** в разделе **Краткое описание**.

	![Reset FTP credentials link in Dashboard](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png)

4. Введите новое имя пользователя и пароль.

	![New FTP user name and password](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

5. На вкладке портала управления **Панель мониторинга** нажмите клавишу F5, чтобы обновить страницу, затем прокрутите вниз до раздела **Пользователь /ftp развертывания**. Обратите внимание, что префиксом для имени пользователя служит имя сайта. **При выполнении входа необходимо использовать это полное имя пользователя с префиксом в виде имени сайта, как показано ниже.**

5. В новом окне браузера перейдите на URL-адрес, который отображается в разделе **Имя узла FTP** на вкладке **Панель мониторинга** страницы портала управления для вашего веб-сайта. Пункт **Имя узла FTP** расположен рядом с пунктом **Пользователь развертывания / FTP** в разделе **Краткое описание**.

6. Выполните вход с использованием учетных данных FTP, созданных ранее (включая имя сайта в виде префикса к имени пользователя).

	В браузере отображается корневая папка сайта.

6. Откройте папку *LogFiles*.

	![Open LogFiles folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

7. Откройте папку с именем W3SVC плюс числовое значение.

	![Open W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

	Папка содержит XML-файлы, связанные с любыми ошибками, которые были зарегистрированы после включения трассировки неудачно завершенных запросов, а также XSL-файл, который может использоваться браузером для форматирования XML.

	![W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

8. Щелкните XML-файл, связанный с неудачно завершенным запросом, данные трассировки которого необходимо просмотреть.

	На следующем рисунке показана часть сведений трассировки примерной ошибки.

	![Failed request tracing in browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)


<h2><a name="nextsteps"></a>Дальнейшие действия</h2>

Вы видели, как Visual Studio позволяет без труда просматривать журналы, созданные веб-сайтом Azure. В следующих разделах содержатся ссылки на дополнительные ресурсы по связанным темам:

* Устранение неполадок веб-сайтов Azure
* Отладка в Visual Studio 
* Удаленная отладка в Azure
* Трассировка в приложениях ASP.NET
* Анализ журналов веб-сервера
* Анализ журналов трассировки неудачно завершенных запросов
* Отладка облачных служб

### Устранение неполадок веб-сайтов Azure

Дополнительную информацию об устранении неполадок, связанных с веб-сайтами Azure, см. на следующих ресурсах:

* [Как отслеживать веб-сайты](/ru-ru/manage/services/web-sites/how-to-monitor-websites/)
* [Исследование проблемы утечки памяти на веб-сайтах Azure с использованием Visual Studio 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Запись блога Microsoft ALM о функциях Visual Studio для анализа проблем с управляемой памятью.
* [Интерактивные средства веб-сайтов Windows Azure, о которых вам следует знать](/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Запись в блоге Амита Эппла (Amit Apple).

Справочные данные об определенном вопросе, связанном с устранением неполадок, можно получить, начав беседу на одном из следующих форумов:

* [Форум Azure на веб-сайте ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Форум Azure на портале MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### Отладка в Visual Studio 

Дополнительные сведения об использовании режима отладки в Visual Studio см. в теме MSDN [Отладка в Visual Studio](http://msdn.microsoft.com/ru-ru/library/vstudio/sc65sadd.aspx) и статье [Рекомендации по отладке в Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### Удаленная отладка в Azure

Дополнительную информацию об удаленной отладке для веб-сайтов и веб-заданий Azure см. на следующих ресурсах:

* [Введение в удаленную отладку на веб-сайтах Azure](/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Введение в удаленную отладку на веб-сайтах Azure (часть 2) - подробней об удаленной отладке](/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Введение в удаленную отладку на веб-сайтах Azure (часть 3) - среда с несколькими экземплярами и GIT](/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Отладка веб-заданий (видео)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Если на вашем веб-сайте используется веб-API Azure или серверная часть мобильных служб, дополнительную информацию об отладке см. в разделе [Отладка серверной части .NET в Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### Трассировка в приложениях ASP.NET

В Интернете отсутствуют обновленные и подробные вводные данные по трассировке ASP.NET. Лучшее, что можно сделать, это начать со старых вводных материалов, написанных для Web Forms, так как тогда MVC еще не существовало. А также дополнить эти сведения новыми постами из блогов, посвященных тем или иным конкретным вопросам. Можно также продуктивно начать изучение со следующих ресурсов:

* [Мониторинг и телеметрия (создание реальных облачных приложений в Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br> 
  Глава электронной книги с рекомендациями по трассировке в облачных приложениях Azure.
* [Трассировка ASP.NET](http://msdn.microsoft.com/ru-ru/library/ms972204.aspx)<br/>
  Старый, но по-прежнему хороший ресурс для введения в тему.
* [Прослушиватели трассировки](http://msdn.microsoft.com/ru-ru/library/4y5y10s7.aspx)<br/>
  Сведения о прослушивателях трассировки, но без упоминания [WebPageTraceListener](http://msdn.microsoft.com/ru-ru/library/system.web.webpagetracelistener.aspx).
* [Пошаговое руководство: интеграция трассировки ASP.NET с трассировкой System.Diagnostics](http://msdn.microsoft.com/ru-ru/library/b0ectfxd.aspx)<br/>
  Это также старый ресурс, который однако содержит некоторые дополнительные сведения, которые не освещаются во вводной статье.
* [Трассировка в представлениях ASP.NET MVC Razor](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Помимо трассировки в представлениях Razor, в этом посте также описывается, как создать фильтр ошибок, чтобы регистрировать все необработанные исключения в приложении MVC. Сведения о записи в журнал всех необработанных исключений в приложении Web Forms см. в примере Global.asax в разделе [Полный пример для обработчиков ошибок](http://msdn.microsoft.com/ru-ru/library/bb397417.aspx) на MSDN. Если в MVC или Web Forms понадобится зарегистрировать определенные исключения, но позволить платформе их обрабатывать, можно перехватить и повторно создать элементы, как показано на следующем примере.

        try
        {
           // Код, который мог вызвать выдаваемое исключение.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

* [Потоковая передача диагностических журналов трассировки из командной строки Azure (включая Glimpse!)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
 Как использовать командную строку, чтобы сделать все, что показано в этом руководстве на примере Visual Studio. [Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) - это средство для отладки приложений ASP.NET. 
* [Использование функций диагностики и ведения журнала для веб-сайта Azure - Дэвид Эббо (David Ebbo)](http://www.windowsazure.com/ru-ru/documentation/videos/azure-web-site-logging-and-diagnostics/) и [Потоковая передача журналов с веб-сайтов Azure - Дэвид Эббо (David Ebbo)](http://www.windowsazure.com/ru-ru/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Авторы видео: Скотт Хансельман (Scott Hanselman) и Дэвид Эббо (David Ebbo).

Для ведения журнала ошибок вместо написания своего кода трассировки можно использовать платформу ведения журналов с открытым исходным кодом, например [ELMAH](http://nuget.org/packages/elmah/). Дополнительные сведения см. в [записях блога Скотта Хансельмана (Scott Hanselman), посвященных ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Обратите внимание, что не нужно использовать трассировку ASP.NET или System.Diagnostics для получения потоковых журналов из Azure. Служба потоковой передачи журналов веб-сайта Azure будет передавать любые файлы с расширением *.txt*, *.html* или *.log*, которые будут найдены в папке *LogFiles*. Поэтому вы можете создать собственную систему ведения журналов, которая записывает данные в файловую систему или на веб-сайт, а файлы будут автоматически передаваться и скачиваться. Все, что нужно сделать, - это написать код приложения, который создает файлы в папке *d:\home\logfiles*. 

### Анализ журналов веб-сервера

Дополнительные сведения об анализе журналов веб-сервера см. на следующих ресурсах:

* [LogParser](http://www.microsoft.com/ru-ru/download/details.aspx?id=24659)<br/>
  Средство для просмотра данных в журналах веб-сервера (файлы *.log*).
* [Устранение неполадок, связанных с производительностью IIS, или ошибок приложений с помощью LogParser ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Введение в средство LogParser, которое можно использовать для анализа журналов веб-сервера.
* [Посты блога Роберта МакМюррея (Robert McMurray), посвященные использованию LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Код состояния HTTP в IIS 7.0, IIS 7.5 и IIS 8.0](http://support.microsoft.com/kb/943891)

### Анализ журналов трассировки неудачно завершенных запросов

Веб-сайт Microsoft TechNet содержит раздел [Использование трассировки неудачно завершенных запросов](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing), который может оказаться полезным для понимания того, как используются эти журналы. Однако эта документация посвящена преимущественно настройке трассировки неудачно завершенных запросов в IIS, которую невозможно выполнить на веб-сайтах Azure.

### Отладка облачных служб

Если вас интересует отладка облачной службы Azure, а не веб-сайта, ознакомьтесь с разделом [Отладка облачных служб](http://msdn.microsoft.com/ru-ru/library/windowsazure/ee405479.aspx).




[GetStarted]: ../web-sites-dotnet-get-started/
[GetStartedWJ]: ../websites-dotnet-webjobs-sdk/

