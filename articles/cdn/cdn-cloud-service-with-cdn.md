<properties
	pageTitle="Интеграция облачной службы с Azure CDN"
	description="В этом учебнике объясняется, как развернуть облачную службу, которая обслуживает содержимое из интегрированной конечной точки CDN Azure"
	services="cdn, cloud-services"
	documentationCenter=".net"
	authors="camsoper"
	manager="erikre"
	editor="tysonn"/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/11/2016"
	ms.author="casoper"/>


# <a name="intro"></a>Интеграция облачной службы с Azure CDN

Облачную службу можно интегрировать с сетью Azure CDN, которая обслуживает любое содержимое из расположения облачной службы. Такой подход обеспечивает следующие преимущества.

- Упрощение развертывания и обновления образов, скриптов и таблиц стилей в каталогах проекта облачной службы.
- Облегчение обновления пакетов NuGet в облачной службе, например, версий jQuery или Bootstrap.
- Управление веб-приложениями и контентом, обслуживаемым CDN, из одного интерфейса Visual Studio.
- Единый рабочий процесс развертывания для веб-приложения и контента, обслуживаемого CDN.
- Интеграция объединения и минификации ASP.NET с Azure CDN.

## Новые знания ##

Из этого учебника вы узнаете следующее:

-	[Интеграция конечной точки Azure CDN с облачной службой и обслуживание статического содержимого на веб-страницах из Azure CDN](#deploy)
-	[Настройка параметров кэша для статического содержимого в облачной службе](#caching)
-	[Обслуживание содержимого из действий контроллера с помощью Azure CDN](#controller)
-	[Обслуживание объединенного в пакет и минифицированного содержимого через Azure CDN с поддержкой интерфейса отладки сценариев в Visual Studio](#bundling)
-	[Настройка резервирования сценариев и CSS, когда Azure CDN находится в автономном режиме](#fallback)

## Что будет строиться ##

Будет выполняться развертывание веб-роли облачной службы с помощью шаблона MVC ASP.NET по умолчанию, добавление кода для обслуживания контента из интегрированной Azure CDN, такого как образ, результаты действий контроллера, файлы JavaScript и CSS по умолчанию, а также создание кода для настройки резервного механизма обслуживаемых пакетов в случае, когда CDN находится в автономном режиме.

## Необходимые условия ##

Для работы с этим учебником необходимы следующие компоненты.

-	Активная [учетная запись Microsoft Azure](/account/).
-	Наличие Visual Studio 2015 с [пакетом SDK Azure](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

> [AZURE.NOTE] Для работы с этим учебником требуется учетная запись Azure.
> + Вы можете [открыть учетную запись Azure бесплатно](/pricing/free-trial/) — вы получаете кредиты, которые можно использовать для опробования платных служб Azure, и даже после израсходования кредитов вы сохраняете учетную запись и возможность использовать бесплатные службы Azure, такие как веб-сайты.
> + Вы имеете возможность [активировать преимущества подписчика MSDN](/pricing/member-offers/msdn-benefits-details/) — ваша подписка MSDN каждый месяц приносит вам кредиты, которые можно использовать для оплаты за службы Azure.

<a name="deploy"></a>
## Развертывание облачной службы ##

В этом разделе будет выполняться развертывание шаблона приложения MVC ASP.NET по умолчанию в Visual Studio 2015 в веб-роли облачной службы, после чего оно будет интегрировано с новой конечной точкой CDN. Выполните приведенные далее инструкции.

1. В Visual Studio 2015 создайте новую облачную службу Azure, последовательно выбрав пункты **Файл > Создать > Проект > Облако > Облачная служба Azure**. Назначьте ей имя и нажмите кнопку **ОК**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. Выберите **веб-роль ASP.NET** и нажмите кнопку со значком **>**. Нажмите кнопку ОК.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. Выберите **MVC** и нажмите кнопку **ОК**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. Теперь опубликуйте эту веб-роль в облачной службе Azure. Щелкните правой кнопкой мыши проект облачной службы и выберите **Опубликовать**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. Если вы еще не вошли в Microsoft Azure, щелкните раскрывающийся список **Добавить учетную запись** и выберите пункт **Добавить учетную запись**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. На странице входа войдите с учетной записью Майкрософт, которая использовалась для активации учетной записи Azure.
7. После выполнения входа нажмите кнопку **Далее**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. Если облачная служба или учетная запись хранения еще не создана, Visual Studio поможет создать их. В диалоговом окне **Создание облачной службы и учетной записи** введите нужное имя службы и выберите нужный регион. Затем щелкните **Создать**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. На странице параметров публикации проверьте конфигурацию и нажмите **Опубликовать**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

	>[AZURE.NOTE] Процесс публикации для облачных служб занимает много времени. Параметр включения веб-развертывания для всех ролей может значительно ускорить отладку облачной службы, обеспечивая быстрые (но временные) обновления веб-ролей. Дополнительные сведения об этом параметре см. в статье [Публикация облачной службы с помощью инструментов Azure](http://msdn.microsoft.com/library/ff683672.aspx).

	Когда **журнал изменений Microsoft Azure** покажет, что публикация имеет состояние **Завершено**, будет создаваться конечная точка CDN, интегрированная с этой облачной службой.

	>[AZURE.WARNING] Если после публикации в развернутой облачной службе отображается экран сообщения об ошибке, вероятно, это связано с тем, что развернутая облачная служба использует [гостевую ОС без .NET 4.5.2](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates). Чтобы устранить эту проблему, [разверните .NET 4.5.2 в качестве задачи запуска](../cloud-services/cloud-services-dotnet-install-dotnet.md).

## Создание нового профиля сети CDN

Профиль сети CDN представляет собой коллекцию конечных точек сети CDN. Каждый профиль содержит одну или несколько конечных точек сети CDN. Вы можете использовать несколько профилей для упорядочения конечных точек сети CDN по домену Интернета, веб-приложению или согласно другим условиям.

> [AZURE.TIP] Если у вас уже есть профиль CDN для работы, перейдите к разделу [Создание новой конечной точки сети CDN](#create-a-new-cdn-endpoint).

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## Создание новой конечной точки сети CDN

**Создание новой конечной точки CDN для учетной записи хранения**

1. На [портале управления Azure](https://portal.azure.com) перейдите к профилю сети CDN. На предыдущем шаге вы могли прикрепить его к панели мониторинга. Если профиль не прикреплен, найдите его, нажав кнопку **Обзор**, выбрав **Профили CDN** и щелкнув профиль, к которому нужно добавить конечную точку.

    Появится колонка профиля сети CDN.

    ![Профиль сети CDN][cdn-profile-settings]

2. Нажмите кнопку **Добавить конечную точку**.

    ![Кнопка "Добавить конечную точку"][cdn-new-endpoint-button]

    Появится колонка **Добавление конечной точки**.

    ![Колонка "Добавление конечной точки"][cdn-add-endpoint]

3. Введите **имя** конечной точки сети CDN. Это имя будет использоваться для доступа к кэшированным ресурсам в домене `<EndpointName>.azureedge.net`.

4. В раскрывающемся списке **Тип источника** выберите *Облачная служба*.

5. В раскрывающемся списке **Имя узла источника** выберите облачную службу.

6. Оставьте значения по умолчанию для параметров **Путь к источнику**, **Заголовок узла источника** и **Протокол и порт источника**. Необходимо указать хотя бы один протокол (HTTP или HTTPS).

7. Нажмите кнопку **Создать**, чтобы создать новую конечную точку.

8. Созданная конечная точка отображается в списке конечных точек для профиля. В режиме списка отображается URL-адрес для доступа к кэшированному содержимому, а также исходному домену.

    ![Конечная точка сети CDN][cdn-endpoint-success]

    > [AZURE.NOTE] Конечная точка не сразу будет доступна для использования. Распространение регистрации по сети CDN может занять 90 минут. Если пользователь попытается незамедлительно воспользоваться именем домена CDN, он может столкнуться с кодом состояния 404, пока содержимое не станет доступно через CDN.

## Тестирование конечной точки сети CDN

Когда публикация перейдет в состояние **Завершено**, откройте окно браузера и перейдите по адресу **http://<cdnName>*.azureedge.net/Content/bootstrap.css** В моей настройке это следующий URL-адрес:

	http://camservice.azureedge.net/Content/bootstrap.css

Он соответствует следующему исходному URL-адресу в конечной точке CDN:

	http://camcdnservice.cloudapp.net/Content/bootstrap.css

При переходе по пути **http://*&lt;cdnName>*.azureedge.net/Content/bootstrap.css** в зависимости от браузера вам будет предложено загрузить или открыть файл bootstrap.css, происходящий из опубликованного веб-приложения.

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

Аналогичным образом можно получать доступ к любому общедоступному URL-адресу в **http://*&lt;serviceName>*.cloudapp.net/** прямо из конечной точки CDN. Например:

-	к JS-файлу в пути /Script;
-	к любому файлу контента в пути /Content;
-	к любому контроллеру или действию;
-	если в конечной точке CDN включена строка запроса, то к любому URL-адресу со строкой запроса;

Фактически при использовании вышеприведенной конфигурации можно размещать всю облачную службу из **http://*&lt;cdnName>*.vo.msecnd.net/**. Перейдя в ****http://camservice.azureedge.net/**, можно получить результат действия из Home/Index.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Однако это не означает, что следует всегда обслуживать всю облачную службу с помощью Azure CDN. Далее приводятся некоторые предупреждения.

-	Для использования этого подхода весь сайт должен быть общедоступным, поскольку на данный момент Azure CDN не может обслуживать конфиденциальный контент.
-	Если по какой-либо причине, будь то плановое обслуживание или ошибка пользователя, конечная точка CDN становится автономной, вся облачная служба становится автономной, если нельзя перенаправить клиентов на исходный URL-адрес **http://*&lt;serviceName>*.cloudapp.net/**.
-	Даже с настраиваемыми параметрами управления кэшем (см. раздел [Настройка параметров кэширования для статических файлов в облачной службе](#caching)) конечная точка CDN не улучшает производительность высокодинамичного контента. При попытке загрузить домашнюю страницу из конечной точки CDN, как показано выше, обратите внимание, что первая загрузка домашней страницы по умолчанию займет по крайней мере 5 секунд, хотя это довольно простая страница. Представьте, как бы выглядело взаимодействие с клиентом, если бы эта страница содержала динамический контент, который должен обновляться каждую минуту. Обслуживание динамического контента из конечной точки CDN требует, чтобы срок годности кэша был коротким,что приводит к частой потере кэша в конечной точке CDN. Это вредит производительности облачной службы, и смысл CDN теряется.

Альтернатива заключается в том, чтобы определить, какое содержимое должно обслуживаться из Azure CDN на индивидуальной основе в облачной службе. В отношении этого вы уже видели, как получать доступ к отдельным файлам контента из конечной точки CDN. Я покажу, как обслуживать определенное действие контроллера посредством конечной точки CDN, в разделе [Обслуживание контента из действий контроллера посредством Azure CDN](#controller).

<a name="caching"></a>
## Настройка параметров кэширования для статических файлов в облачной службе ##

С помощью интеграции Azure CDN в облачной службе можно указать, как статическое содержимое должно кэшироваться в конечной точке CDN. Для этого откройте *Web.config* из проекта веб-роли (например, WebRole1) и добавьте элемент `<staticContent>` для `<system.webServer>`. Следующий XML настраивает истечение срока годности кэша через 3 дня.

	<system.webServer>
	  <staticContent>
	    <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
	  </staticContent>
	  ...
	</system.webServer>

После этого все статические файлы в облачной службе будут соблюдать то же правило в кэше CDN. Для более детального управления параметрами кэша добавьте файл *Web.config* в папку и добавьте в этом месте свои параметры. Например, добавьте файл *Web.config* в папку *\\Content* и замените содержимое следующим XML:

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

Этот параметр приводит к кэшированию всех статических файлов из папки *\\Content* на 15 дней.

Дополнительные сведения о настройке элемента `<clientCache>` см. в статье [Кэш клиента &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

В разделе [Обслуживание контента из действий контроллера посредством Azure CDN](#controller) я также покажу, как можно настраивать параметры кэша для результатов действий контроллера в кэше CDN.

<a name="controller"></a>
## Обслуживание контента из действий контроллера посредством Azure CDN ##

При интеграции веб-роли облачной службы с Azure CDN сравнительно легко обслуживать содержимое от действий контроллера, используя Azure CDN. Помимо обслуживания облачной службы непосредственно через Azure CDN (показанного выше), [Мартин Баллиау](https://twitter.com/maartenballiauw) (Maarten Balliauw) в видеоролике [Уменьшение задержки в Интернете с помощью Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN) показывает, как это делать с помощью контроллера MemeGenerator. Я просто воспроизведу это здесь.

Предположим, вы хотите создать в облачной службе мемы на основе образа молодого Чака Норриса (фото [Алана Лайта (Alan Light)](http://www.flickr.com/photos/alan-light/218493788/)):

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Имеется простое действие `Index`, которое позволяет клиентам задавать гиперболы в образе, а затем создает мем, как только клиент отправит их в действие. Поскольку это Чак Норрис, можно ожидать, что страница станет очень популярной по всему миру. Это хороший пример обслуживания полудинамического контента с помощью Azure CDN.

Чтобы настроить это действие контроллера, выполните следующие действия.

1. В папке *\\Controllers* создайте новый CS-файл с именем *MemeGeneratorController.cs* и замените содержимое следующим кодом. Не забудьте заменить выделенные фрагменты именем CDN.  

		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Drawing;
		using System.IO;
		using System.Net;
		using System.Web.Hosting;
		using System.Web.Mvc;
		using System.Web.UI;

		namespace WebRole1.Controllers
		{
		    public class MemeGeneratorController : Controller
		    {
		        static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();

		        public ActionResult Index()
		        {
		            return View();
		        }

		        [HttpPost, ActionName("Index")]
		        public ActionResult Index_Post(string top, string bottom)
		        {
		            var identifier = Guid.NewGuid().ToString();
		            if (!Memes.ContainsKey(identifier))
		            {
		                Memes.Add(identifier, new Tuple<string, string>(top, bottom));
		            }

		            return Content("<a href="" + Url.Action("Show", new {id = identifier}) + "">here's your meme</a>");
		        }

		        [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
		        public ActionResult Show(string id)
		        {
		            Tuple<string, string> data = null;
		            if (!Memes.TryGetValue(id, out data))
		            {
		                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
		            }

		            if (Debugger.IsAttached) // Preserve the debug experience
		            {
		                return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
		            }
		            else // Get content from Azure CDN
		            {
		                return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
		            }
		        }

		        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
		        public ActionResult Generate(string top, string bottom)
		        {
		            string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
		            Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);

		            using (Graphics graphics = Graphics.FromImage(bitmap))
		            {
		                SizeF size = new SizeF();
		                using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
		                {
		                    graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
		                }
		                using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
		                {
		                    graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
		                }
		            }

		            MemoryStream ms = new MemoryStream();
		            bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
		            return File(ms.ToArray(), "image/png");
		        }

		        private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
		        {
		            // Compute actual size, shrink if needed
		            while (true)
		            {
		                size = g.MeasureString(text, font);

		                // It fits, back out
		                if (size.Height < i.Height &&
		                     size.Width < i.Width) { return font; }

		                // Try a smaller font (90% of old size)
		                Font oldFont = font;
		                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
		                oldFont.Dispose();
		            }
		        }
		    }
		}

2. Щелкните правой кнопкой мыши действие `Index()` по умолчанию и выберите **Добавить представление**.

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Примите параметры, показанные ниже, и нажмите кнопку **Добавить**.

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Откройте новый файл *Views\\MemeGenerator\\Index.cshtml* и замените его содержимое следующим простым HTML для отправки гипербол:

		<h2>Meme Generator</h2>

		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Снова опубликуйте облачную службу и перейдите в браузере по адресу **http://*&lt;serviceName>*.cloudapp.net/MemeGenerator/Index**.

При отправке значений формы в `/MemeGenerator/Index` метод действия `Index_Post` возвращает ссылку на метод действия `Show` с соответствующим идентификатором ввода. Если щелкнуть ссылку, появится следующий код:

	[OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
	public ActionResult Show(string id)
	{
		Tuple<string, string> data = null;
		if (!Memes.TryGetValue(id, out data))
		{
			return new HttpStatusCodeResult(HttpStatusCode.NotFound);
		}

		if (Debugger.IsAttached) // Preserve the debug experience
		{
			return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
		}
		else // Get content from Azure CDN
		{
			return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
		}
	}

Если подключен локальный отладчик, то вы получите обычный интерфейс отладки с локальным перенаправлением. Если отладчик работает в облачной службе, то перенаправление будет выполняться на следующий адрес:

	http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Он соответствует следующему исходному URL-адресу в конечной точке CDN:

	http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


Затем можно с помощью атрибута `OutputCacheAttribute` в методе `Generate` указать, как должен кэшироваться результат действия, которое будет выполнять Azure CDN. В следующем коде задается срок годности кэша в 1 час (3 600 секунд).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Аналогично можно обслуживать контент из любого действия контроллера в облачной службе посредством Azure CDN с использованием нужного параметра кэширования.

В следующем разделе будет показано, как обслуживать объединенные в пакет и минифицированные скрипты и CSS посредством Azure CDN.

<a name="bundling"></a>
## Интеграция объединения и минификации ASP.NET с Azure CDN. ##

Скрипты и таблицы стилей CSS изменяются нечасто и являются основными кандидатами для кэша Azure CDN. Обслуживание всей веб-роли посредством Azure CDN представляет простейший способ интеграции объединения и минификации с Azure CDN. Однако поскольку вы можете не хотеть это делать, я покажу, как это сделать, сохраняя желаемый интерфейс разработчика объединения и минификации ASP.NET со следующими характеристиками.

-	Отличное взаимодействие в режиме отладки.
-	Упрощенное развертывание.
-	Немедленные обновления клиентов при обновлении версий скриптов или CSS.
-	Резервный механизм на случай сбоя конечной точки CDN.
-	Минимальное изменение кода.

В проекте **WebRole1**, созданном при изучении раздела [Интеграция конечной точки Azure CDN с веб-сайтом Azure и обслуживание статического содержимого на веб-страницах из Azure CDN](#deploy), откройте *App\_Start\\BundleConfig.cs* и взгляните на вызовы метода `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

Первая инструкция `bundles.Add()` добавляет пакет скриптов в виртуальный каталог `~/bundles/jquery`. Затем откройте файл *Views\\Shared\_Layout.cshtml*, чтобы просмотреть, как обрабатывается тег пакета скриптов. Вы должны найти следующую строку кода Razor:

    @Scripts.Render("~/bundles/jquery")

При запуске этого кода Razor в веб-роли Azure он будет обрабатывать тег `<script>` для пакета скриптов следующим образом:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Однако, когда этот код запускается в Visual Studio путем нажатия клавиши `F5`, он будет обрабатывать каждый файл скрипта в пакете индивидуально (в вышеприведенном случае в пакете имеется только один файл скрипта):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Это позволяет отлаживать код JavaScript в среде разработки, уменьшая число параллельных клиентских соединений (объединение) и улучшая производительность загрузки файлов (минификация) в рабочей среде. Это отличная возможность для сохранения с использованием интеграции Azure CDN. Более того, поскольку обработанный пакет уже содержит автоматически созданную строку версии, имеет смысл реплицировать эту функциональность, чтобы при всяком обновлении версии jQuery с помощью NuGet она могла бы максимально быстро обновляться на стороне клиента.

Выполните приведенные ниже действия для интеграции объединения и минификации ASP.NET с конечной точкой CDN.

1. Вернитесь в файл *App\_Start\\BundleConfig.cs* и измените методы `bundles.Add()`, чтобы использовать другой [конструктор Bundle](http://msdn.microsoft.com/library/jj646464.aspx), который задает адрес CDN. Для этого замените определение метода `RegisterBundles` следующим кодом:  

		public static void RegisterBundles(BundleCollection bundles)
		{
		    bundles.UseCdn = true;
		    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
		        .GetName().Version.ToString();
		    var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;

		    bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
		                "~/Scripts/jquery-{version}.js"));

		    bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
		                "~/Scripts/jquery.validate*"));

		    // Use the development version of Modernizr to develop with and learn from. Then, when you're
		    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
		    bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
		                "~/Scripts/modernizr-*"));

		    bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
		                "~/Scripts/bootstrap.js",
		                "~/Scripts/respond.js"));

		    bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
		                "~/Content/bootstrap.css",
		                "~/Content/site.css"));
		}

	Не забудьте заменить `<yourCDNName>` своим именем Azure CDN.

	Простыми словами, устанавливается `bundles.UseCdn = true` и добавляется тщательно созданный URL-адрес CDN в каждый пакет. Например, первый конструктор в коде

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	тот же самый:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))

	Этот конструктор указывает, что при локальной отладке с помощью объединения и минификации ASP.NET должна выполняться обработка отдельных файлов скриптов, но с использованием указанного адреса CDN для доступа к соответствующему скрипту. Однако отметим две важных характеристики этого тщательно созданного URL-адреса CDN.

	-	Источником этого URL-адреса CDN служит адрес `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, который фактически является виртуальным каталогом пакета сценариев в облачной службе.
	-	Поскольку используется конструктор CDN, тег скрипта CDN для этого пакета больше не содержит автоматически созданную строку версии в обработанном URL-адресе. Необходимо вручную создавать уникальную строку версии каждый раз при изменении пакета скриптов, чтобы обеспечить промах кэша в Azure CDN. В то же время эта уникальная строка версии должна оставаться постоянной в течение всего срока существования развертывания для максимального увеличения попаданий в кэш в Azure CDN после развертывания пакета.
	-	Строка запроса v=<W.X.Y.Z> берется из файла *Properties\\AssemblyInfo.cs* в проекте веб-роли. Можно создать рабочий процесс развертывания, включающий увеличение версии сборки при каждой публикации в Azure. Можно также просто изменить файл *Properties\\AssemblyInfo.cs* в проекте, задав автоматическое увеличение строки версии при каждом построении с помощью подстановочного знака '*'. Например:

			[assembly: AssemblyVersion("1.0.0.*")]

		Здесь будет работать любая другая стратегия упрощения создания уникальной строки на срок существования развертывания.

3. Повторно опубликуйте облачную службу и откройте домашнюю страницу.

4. Просмотрите код HTML для этой страницы. При каждой публикации изменений облачной службы вы должны видеть обработанный URL-адрес CDN с уникальной строкой версии. Например:

		...

		<link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>

		<script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>

		...

		<script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>

		<script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>

		...

5. Перейдите в режим отладки облачной службы в Visual Studio, нажав клавишу `F5`.

6. Просмотрите код HTML для этой страницы. Вы по-прежнему будете видеть каждый, индивидуально обработанный файл скрипта, так что можно получить последовательные возможности отладки в Visual Studio.

		...

		    <link href="/Content/bootstrap.css" rel="stylesheet"/>
		<link href="/Content/site.css" rel="stylesheet"/>

		    <script src="/Scripts/modernizr-2.6.2.js"></script>

		...

		    <script src="/Scripts/jquery-1.10.2.js"></script>

		    <script src="/Scripts/bootstrap.js"></script>
		<script src="/Scripts/respond.js"></script>

		...   

<a name="fallback"></a>
## Резервный механизм для URL-адресов CDN ##

Желательно, чтобы в случае сбоя конечной точки Azure CDN по какой-либо причине веб-страница могла использовать в качестве резервной возможности доступ к исходному веб-серверу для загрузки JavaScript или Bootstrap. Одно дело – потерять изображения на веб-сайте из-за недоступности CDN, и совсем другое – потерять критически важные функциональные возможности страницы, обеспечиваемые сценариями и таблицами стилей.

Класс [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) содержит свойство с именем [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx), которое позволяет настраивать резервный механизм на случай сбоя CDN. Для использования этого свойства выполните приведенные ниже действия.

1. В проекте веб-роли откройте файл *App\_Start\\BundleConfig.cs*, где в каждом [конструкторе Bundle](http://msdn.microsoft.com/library/jj646464.aspx) добавлялся URL-адрес CDN, и внесите следующие выделенные изменения, чтобы добавить резервный механизм в пакеты по умолчанию:  

		public static void RegisterBundles(BundleCollection bundles)
		{
		    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
		        .GetName().Version.ToString();
		    var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
		    bundles.UseCdn = true;

		    bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
						{ CdnFallbackExpression = "window.jquery" }
		                .Include("~/Scripts/jquery-{version}.js"));

		    bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval"))
						{ CdnFallbackExpression = "$.validator" }
		            	.Include("~/Scripts/jquery.validate*"));

		    // Use the development version of Modernizr to develop with and learn from. Then, when you're
		    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
		    bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer"))
						{ CdnFallbackExpression = "window.Modernizr" }
						.Include("~/Scripts/modernizr-*"));

		    bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")) 	
						{ CdnFallbackExpression = "$.fn.modal" }
		        		.Include(
			              		"~/Scripts/bootstrap.js",
			              		"~/Scripts/respond.js"));

		    bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
		                "~/Content/bootstrap.css",
		                "~/Content/site.css"));
		}

	Когда выражение `CdnFallbackExpression` не пустое, скрипт внедряется в HTML, чтобы проверить успешность загрузки пакета, и в случае неуспешной загрузки обеспечивает доступ к пакету непосредственно на исходном веб-сервере. В этом свойстве необходимо устанавливать выражение JavaScript, проверяющее, загружен ли соответствующий пакет CDN должным образом. Выражения для проверки каждого пакета отличаются в соответствии с контентом. Для пакетов по умолчанию выше:

	-	`window.jquery` задается в jquery-{version}.js
	-	`$.validator` задается в jquery.validate.js;
	-	`window.Modernizr` задается в modernizer-{version}.js;
	-	`$.fn.modal` задается в bootstrap.js.

	Возможно, вы заметили, что я не установил CdnFallbackExpression для пакета `~/Cointent/css`. Это объясняется тем, что в настоящее время имеется [ошибка в System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104), которая вставляет тег `<script>` для резервной CSS вместо ожидаемого тега `<link>`.

	Однако существует хорошая [резервная форма пакета стилей](https://github.com/EmberConsultingGroup/StyleBundleFallback), предлагаемая [Ember Consulting Group](https://github.com/EmberConsultingGroup).

2. Чтобы использовать этот обходной путь для CSS, создайте в папке *App\_Start* новый CS-файл *StyleBundleExtensions.cs* и замените его содержимое [кодом от GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).

4. В *App\_Start\\StyleFundleExtensions.cs* измените имя пространства имен на имя веб-роли (например, **WebRole1**).

3. Вернитесь в файл `App_Start\BundleConfig.cs` и замените последний оператор `bundles.Add` следующим выделенным кодом:

		bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
		    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
		    .Include(
		          "~/Content/bootstrap.css",
		          "~/Content/site.css"));

	Этот новый метод расширения использует ту же идею внедрения скрипта в HTML для поиска в модели DOM имени класса, имени правила и значения правила, соответствующих заданным в пакете CSS, и возвращения на исходный веб-сервер при отсутствии соответствия.

4. Снова опубликуйте облачную службу и откройте домашнюю страницу.

5. Просмотрите код HTML для этой страницы. Вы должны найти внедренные скрипты, аналогичные показанным ниже.

		...

	    <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
		<script>(function() {
		                var loadFallback,
		                    len = document.styleSheets.length;
		                for (var i = 0; i < len; i++) {
		                    var sheet = document.styleSheets[i];
		                    if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
		                        var meta = document.createElement('meta');
		                        meta.className = 'sr-only';
		                        document.head.appendChild(meta);
		                        var value = window.getComputedStyle(meta).getPropertyValue('width');
		                        document.head.removeChild(meta);
		                        if (value !== '1px') {
		                            document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
		                        }
		                    }
		                }
		                return true;
		            }())||document.write('<script src="/Content/css"><\/script>');</script>

		    <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
		<script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>

		...

		    <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
		<script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>

		    <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
		<script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>

		...


	Обратите внимание, что внедренный скрипт для пакета CSS по-прежнему содержит ошибочный фрагмент из свойства `CdnFallbackExpression` в следующей строке:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	Но поскольку первая часть выражения '||' будет всегда возвращать значение true (в строке прямо над этой), функция document.write() никогда не будет выполняться.

## Дополнительные сведения ##
- [Общие сведения о сети доставки контента (CDN) Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Использование Azure CDN](cdn-create-new-endpoint.md)
- [Объединение и минификация ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)



[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png

<!---HONumber=AcomDC_0518_2016-->