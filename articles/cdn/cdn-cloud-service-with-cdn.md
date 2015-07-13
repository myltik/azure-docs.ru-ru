<properties 
	pageTitle="Интеграция облачной службы с Azure CDN" 
	description="В этом учебнике объясняется, как развернуть облачную службу, которая обслуживает содержимое из интегрированной конечной точки CDN Azure" 
	services="cdn, cloud-services" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="cephalin"/>

#<a name="intro"></a>Интеграция облачной службы с Azure CDN #

<!-- Keeping this article pinned to the old portal because CDN is not yet lit up in the new portal -->

Облачную службу можно интегрировать с Azure CDN (сетью доставки содержимого), которая выдает любое содержимое из пути облачной службы `~/CDN`. Такой подход обеспечивает следующие преимущества.

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
-	Наличие Visual Studio 2013 с [пакетом SDK Azure](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409).

> [AZURE.NOTE]Для выполнения этого учебника необходима учетная запись Azure. + Вы можете [открыть учетную запись Azure бесплатно](/pricing/free-trial/) — вы получаете кредиты, которые можно использовать, чтобы опробовать платные службы Azure, и даже израсходовав кредиты вы сохраняете учетную запись и возможность использовать бесплатные службы Azure, например веб-приложения [службы приложений](http://go.microsoft.com/fwlink/?LinkId=529714). + Вы можете [активировать преимущества подписчика MSDN](/pricing/member-offers/msdn-benefits-details/) — ваша подписка MSDN каждый месяц приносит вам кредиты, которые можно использовать для оплаты служб Azure.

<a name="deploy"></a>
## Развертывание облачной службы с интегрированной конечной точкой CDN ##

В этом разделе будет выполняться развертывание шаблона приложения MVC ASP.NET по умолчанию в Visual Studio 2013 в веб-роли облачной службы, а затем интеграция его с новой конечной точкой CDN. Выполните приведенные далее инструкции.

1. В Visual Studio 2013 создайте новую облачную службу Azure в строке меню, последовательно выбрав пункты **Файл > Создать > Проект > Облако > Облачная служба Azure**. Назначьте ей имя и нажмите кнопку **ОК**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. Выберите **веб-роль ASP.NET** и нажмите кнопку со значком **>**. Нажмите кнопку ОК.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. Выберите **MVC** и нажмите кнопку **ОК**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. Теперь опубликуйте эту веб-роль в облачной службе Azure. Щелкните правой кнопкой мыши проект облачной службы и выберите **Опубликовать**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. Если вход в Microsoft Azure еще не выполнен, нажмите кнопку **Войти**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. На странице входа войдите с учетной записью Майкрософт, которая использовалась для активации учетной записи Azure.
7. После выполнения входа нажмите кнопку **Далее**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. Если облачная служба или учетная запись хранения еще не создана, Visual Studio поможет создать их. В диалоговом окне **создания облачной службы и учетной записи** введите нужное имя службы. Затем щелкните **Создать**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. На странице параметров публикации проверьте конфигурацию и нажмите **Опубликовать**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

	>[AZURE.NOTE]Процесс публикации для облачных служб занимает много времени. Параметр включения веб-развертывания для всех ролей может значительно ускорить отладку облачной службы, обеспечивая быстрые (но временные) обновления веб-ролей. Дополнительные сведения об этом параметре см. в статье [Публикация облачной службы с помощью инструментов Azure](http://msdn.microsoft.com/library/ff683672.aspx).

	Когда **журнал изменений Microsoft Azure** покажет, что публикация имеет состояние **Завершено**, будет создаваться конечная точка CDN, интегрированная с этой облачной службой.

1. Чтобы создать конечную точку CDN, войдите на [портал управления Azure](http://manage.windowsazure.com/).
2. Последовательно выберите пункты **Создать** > **Службы приложений** > **CDN** > **Быстрое создание**. Выберите **http://*&lt;servicenamehttp://.cloudapp.net/cdn/** и щелкните **Создать**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-10-createcdn.png)

	>[AZURE.NOTE]После создания конечной точки CDN портала Azure показан его URL-адрес и исходный домен, с которым она интегрирована. Однако полное распространение конфигурации новой конечной точки CDN во все расположения узлов CDN может занять некоторое время.

	Обратите внимание, что конечная точка CDN связана с путем **cdn/** облачной службы. Можно либо создать папку **cdn** в проекте **WebRole1**, либо использовать переопределение URL-адресов, чтобы разбивать все входящие ссылки на этот путь. В данном учебнике будет использоваться последний способ.

3. На портале Azure откройте вкладку **CDN** и щелкните имя только что созданной конечной точки CDN.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-11-disablequerya.png)

3. Нажмите **Включить строку запроса**, чтобы включать строки запроса в кэш CDN. После этого те же самые ссылки, доступ к которым осуществляется с помощью разных строк запроса, будут кэшироваться как отдельные записи.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-12-disablequeryb.png)

	>[AZURE.NOTE]Хотя включение поддержки строки запроса в этом разделе учебника не обязательно, для удобства это можно сделать как можно раньше, так как распространение любого изменения во все узлы CDN занимает некоторое время, и нежелательно, чтобы содержание без включенных строк запроса засорял кэш CDN (обновление контента CDN будет рассматриваться позднее).

3. Проверьте связь с конечной точкой CDN, чтобы убедиться, что она распространилась в узлы CDN. Может потребоваться подождать около часа, прежде чем конечная точка начнет отвечать на запросы проверки связи.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-13-testcdn.png)

2. Вернитесь в Visual Studio 2013, откройте файл **Web.config** в проекте **WebRole1** и добавьте в тег `<system.webServer>` следующий код:
	<pre class="prettyprint">
&lt;system.webServer>
  <mark>&lt;rewrite>
    &lt;rules>
      &lt;rule name="RewriteIncomingCdnRequest" stopProcessing="true">
        &lt;match url="^cdn/(.*)$"/>
        &lt;action type="Rewrite" url="{R:1}"/>
      &lt;/rule>
    &lt;/rules>
  &lt;/rewrite></mark>
  ...
&lt;/system.webServer>
</pre>

4. Снова опубликуйте облачную службу. Щелкните правой кнопкой мыши проект облачной службы и выберите **Опубликовать**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

1. Когда публикация перейдет в состояние **Завершено**, откройте окно браузера и перейдите по адресу **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css**. В моей настройке это следующий URL-адрес:

		http://az632148.vo.msecnd.net/Content/bootstrap.css

	Он соответствует следующему исходному URL-адресу в конечной точке CDN:

		http://cephalinservice.cloudapp.net/cdn/Content/bootstrap.css

	После переопределения URL-адреса в моем веб-приложении фактический файл, который кэшируется в соответствующем кэше CDN, имеет следующий адрес:

		http://cephalinservice.cloudapp.net/Content/bootstrap.css

	При переходе по адресу **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css** будет предложено скачать файл bootstrap.css, происходящий из опубликованного веб-приложения.

	![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

Аналогичным образом можно получать доступ к любому общедоступному URL-адресу в **http://*&lt;serviceName>*.cloudapp.net/** прямо из конечной точки CDN. Например:

-	к JS-файлу в пути /Script;
-	к любому файлу контента в пути /Content;
-	к любому контроллеру или действию; 
-	если в конечной точке CDN включена строка запроса, то к любому URL-адресу со строкой запроса;

Фактически при использовании вышеприведенной конфигурации можно размещать всю облачную службу из **http://*&lt;cdnName>*.vo.msecnd.net/**. Перейдя в **http://az632148.vo.msecnd.net/**, можно получить результат действия из Home/Index.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Однако это не означает, что следует всегда обслуживать всю облачную службу с помощью Azure CDN. Далее приводятся некоторые предупреждения.

-	Для использования этого подхода весь сайт должен быть общедоступным, поскольку Azure CDN не может обслуживать конфиденциальный контент.
-	Если по какой-либо причине, будь то плановое обслуживание или ошибка пользователя, конечная точка CDN становится автономной, вся облачная служба становится автономной, если нельзя перенаправить клиентов на исходный URL-адрес **http://*&lt;serviceName>*.cloudapp.net/**. 
-	Даже с настраиваемыми параметрами управления кэшем (см. раздел [Настройка параметров кэширования для статических файлов в облачной службе](#caching)) конечная точка CDN не улучшает производительность высокодинамичного контента. При попытке загрузить домашнюю страницу из конечной точки CDN, как показано выше, обратите внимание, что первая загрузка домашней страницы по умолчанию займет по крайней мере 5 секунд, хотя это довольно простая страница. Представьте, как бы выглядело взаимодействие с клиентом, если бы эта страница содержала динамический контент, который должен обновляться каждую минуту. Обслуживание динамического контента из конечной точки CDN требует, чтобы срок годности кэша был коротким,что приводит к частой потере кэша в конечной точке CDN. Это вредит производительности облачной службы, и смысл CDN теряется.

Альтернатива заключается в том, чтобы определить, какое содержимое должно обслуживаться из Azure CDN на индивидуальной основе в облачной службе. В отношении этого вы уже видели, как получать доступ к отдельным файлам контента из конечной точки CDN. Я покажу, как обслуживать определенное действие контроллера посредством конечной точки CDN, в разделе [Обслуживание контента из действий контроллера посредством Azure CDN](#controller).

Можно указать более ограничивающее правило переопределения URL-адреса, чтобы ограничить содержимое, доступное через конечную точку CDN. Например, чтобы ограничить переопределение URL-адреса для папки *\Scripts*, измените правило переопределения выше следующим образом: <pre class="prettyprint"> &lt;rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;&gt; &lt;match url=&quot;^cdn/<mark>Scripts/</mark>(.*)$&quot;/&gt; &lt;action type=&quot;Rewrite&quot; url=&quot;<mark>Scripts/</mark>{R:1}&quot;/&gt; &lt;/rule&gt; </pre>

<a name="caching"></a>
## Настройка параметров кэширования для статических файлов в облачной службе ##

С помощью интеграции Azure CDN в облачной службе можно указать, как статическое содержимое должно кэшироваться в конечной точке CDN. Для этого откройте *Web.config* из проекта веб-роли (например, WebRole1) и добавьте элемент `<staticContent>` для `<system.webServer>`. XML, приведенный ниже, настраивает истечение срока кэша через 3 дня. <pre class="prettyprint"> &lt;system.webServer&gt; <mark>&lt;staticContent&gt; &lt;clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/&gt; &lt;/staticContent&gt;</mark> ... &lt;/system.webServer&gt; </pre>

После этого все статические файлы в облачной службе будут соблюдать то же правило в кэше CDN. Для более детального управления параметрами кэша добавьте файл *Web.config* в папку и добавьте в этом месте свои параметры. Например, добавьте файл *Web.config* в папку *\Content* и замените содержимое следующим XML:

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

Этот параметр приводит к кэшированию всех статических файлов из папки *\Content* на 15 дней.

Дополнительные сведения о настройке элемента `<clientCache>` см. в статье [Кэш клиента &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

В разделе [Обслуживание контента из действий контроллера посредством Azure CDN](#controller) я также покажу, как можно настраивать параметры кэша для результатов действий контроллера в кэше CDN.

<a name="controller"></a>
## Обслуживание контента из действий контроллера посредством Azure CDN ##

При интеграции веб-роли облачной службы с Azure CDN сравнительно легко обслуживать содержимое от действий контроллера, используя Azure CDN. Помимо обслуживания облачной службы непосредственно через Azure CDN (показанного выше), [Мартин Баллиау](https://twitter.com/maartenballiauw) (Maarten Balliauw) в видеоролике [Уменьшение задержки в Интернете с помощью Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN) показывает, как это делать с помощью контроллера MemeGenerator. Я просто воспроизведу это здесь.

Предположим, вы хотите создать в облачной службе мемы на основе образа молодого Чака Норриса (фото [Алана Лайта (Alan Light)](http://www.flickr.com/photos/alan-light/218493788/)):

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Имеется простое действие `Index`, которое позволяет клиентам задавать гиперболы в образе, а затем создает мем, как только клиент отправит их в действие. Поскольку это Чак Норрис, можно ожидать, что страница станет очень популярной по всему миру. Это хороший пример обслуживания полудинамического контента с помощью Azure CDN.

Чтобы настроить это действие контроллера, выполните следующие действия.

1. В папке *\Controllers* создайте новый CS-файл с именем *MemeGeneratorController.cs* и замените содержимое следующим кодом. Не забудьте заменить выделенные фрагменты именем CDN.  
	<pre class="prettyprint">
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Drawing;
using System.IO;
using System.Net;
using System.Web.Hosting;
using System.Web.Mvc;
using System.Web.UI;

пространство имен WebRole1.Controllers
{
    public class MemeGeneratorController : Controller
    {
        static readonly Dictionary&lt;string, Tuple&lt;string ,string>> Memes = new Dictionary&lt;string, Tuple&lt;string, string>>();

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
                Memes.Add(identifier, new Tuple&lt;string, string>(top, bottom));
            }

            return Content("&lt;a href="" + Url.Action("Show", new {id = identifier}) + "">here's your meme&lt;/a>");
        }

        [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
        public ActionResult Show(string id)
        {
            Tuple&lt;string, string> data = null;
            if (!Memes.TryGetValue(id, out data))
            {
                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
            }

            if (Debugger.IsAttached) // Не изменять поведение отладки
            {
                return Redirect(string.Format("/MemeGenerator/Generate?top={0}&amp;bottom={1}", data.Item1, data.Item2));
            }
            else // Получить содержимое из Azure CDN
            {
                return Redirect(string.Format("http://<mark>&lt;yourCdnName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}", data.Item1, data.Item2));
            }
        }

        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
        public ActionResult Generate(string top, string bottom)
        {
            string imageFilePath = HostingEnvironment.MapPath("<mark>~/Content/chuck.bmp</mark>");
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
            // Вычисление фактического размера. При необходимости выполняется сжатие
            while (true)
            {
                size = g.MeasureString(text, font);

                // Если размер подходит, назад
                if (size.Height &lt; i.Height &amp;&amp;
                     size.Width &lt; i.Width) { return font; }

                // Попробовать меньший шрифт (90 % от предыдущего размера)
                Font oldFont = font;
                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                oldFont.Dispose();
            }
        }
    }
}
</pre>

2. Щелкните правой кнопкой мыши действие `Index()` по умолчанию и выберите **Добавить представление**.

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Примите параметры, показанные ниже, и нажмите кнопку **Добавить**.

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Откройте новый файл *Views\MemeGenerator\Index.cshtml* и замените его содержимое следующим простым HTML для отправки гипербол:

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Снова опубликуйте облачную службу и перейдите в браузере по адресу **http://*&lt;serviceName>*.cloudapp.net/MemeGenerator/Index**.

При отправке значений формы в `/MemeGenerator/Index` метод действия `Index_Post` возвращает ссылку на метод действия `Show` с соответствующим идентификатором ввода. Если щелкнуть ссылку, появляется следующий код: <pre class="prettyprint"> [OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)] public ActionResult Show(string id) { Tuple&lt;string, string&gt; data = null; if (!Memes.TryGetValue(id, out data)) { return new HttpStatusCodeResult(HttpStatusCode.NotFound); }

    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(&quot;http://<mark>&lt;cdnName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
    }
} </pre>

Если подключен локальный отладчик, то вы получите обычный интерфейс отладки с локальным перенаправлением. Если отладчик работает в облачной службе, то перенаправление будет выполняться на следующий адрес:

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Он соответствует следующему исходному URL-адресу в конечной точке CDN:

	http://<youCloudServiceName>.cloudapp.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

После правила переопределения URL-адреса, применявшегося ранее, фактический файл, который кэшируется в конечной точке CDN, имеет следующий адрес:

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

В проекте **WebRole1**, созданном в разделе [Развертывание облачной службы с интегрированной конечной точкой CDN](#deploy), откройте сценарий *App_Start\BundleConfig.cs* и посмотрите на вызовы метода `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

Первая инструкция `bundles.Add()` добавляет пакет скриптов в виртуальный каталог `~/bundles/jquery`. Затем откройте файл *Views\Shared_Layout.cshtml*, чтобы просмотреть, как обрабатывается тег пакета скриптов. Вы должны найти следующую строку кода Razor:

    @Scripts.Render("~/bundles/jquery")

При запуске этого кода Razor в веб-роли Azure он будет обрабатывать тег `<script>` для пакета скриптов следующим образом:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Однако, когда этот код запускается в Visual Studio путем нажатия клавиши `F5`, он будет обрабатывать каждый файл скрипта в пакете индивидуально (в вышеприведенном случае в пакете имеется только один файл скрипта):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Это позволяет отлаживать код JavaScript в среде разработки, уменьшая число параллельных клиентских соединений (объединение) и улучшая производительность загрузки файлов (минификация) в рабочей среде. Это отличная возможность для сохранения с использованием интеграции Azure CDN. Более того, поскольку обработанный пакет уже содержит автоматически созданную строку версии, имеет смысл реплицировать эту функциональность, чтобы при всяком обновлении версии jQuery с помощью NuGet она могла бы максимально быстро обновляться на стороне клиента.

Выполните приведенные ниже действия для интеграции объединения и минификации ASP.NET с конечной точкой CDN.

1. Вернитесь в файл *App_Start\BundleConfig.cs* и измените методы `bundles.Add()`, чтобы использовать другой [конструктор Bundle](http://msdn.microsoft.com/library/jj646464.aspx), который задает адрес CDN. Для этого замените определение метода `RegisterBundles` следующим кодом:  
	<pre class="prettyprint">
public static void RegisterBundles(BundleCollection bundles)
{
    <mark>bundles.UseCdn = true;
    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
        .GetName().Version.ToString();
    var cdnUrl = "http://&lt;yourCDNName>.vo.msecnd.net/{0}?v=" + version;</mark>

    bundles.Add(new ScriptBundle("~/bundles/jquery"<mark>, string.Format(cdnUrl, "bundles/jquery")</mark>).Include(
                "~/Scripts/jquery-{version}.js"));

    bundles.Add(new ScriptBundle("~/bundles/jqueryval"<mark>, string.Format(cdnUrl, "bundles/jqueryval")</mark>).Include(
                "~/Scripts/jquery.validate*"));

    // Используйте версию разработки Modernizr как основу для обучения и разработки. Когда вы будете готовы
    // к развертыванию в рабочей среде, используйте инструмент создания по адресу http://modernizr.com, что выбирать только необходимые тесты.
    bundles.Add(new ScriptBundle("~/bundles/modernizr"<mark>, string.Format(cdnUrl, "bundles/modernizer")</mark>).Include(
                "~/Scripts/modernizr-*"));

    bundles.Add(new ScriptBundle("~/bundles/bootstrap"<mark>, string.Format(cdnUrl, "bundles/bootstrap")</mark>).Include(
                "~/Scripts/bootstrap.js",
                "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css"<mark>, string.Format(cdnUrl, "Content/css")</mark>).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
}
</pre>Не забудьте заменить `<yourCDNName>` своим именем Azure CDN.

	Простыми словами, устанавливается `bundles.UseCdn = true` и добавляется тщательно созданный URL-адрес CDN в каждый пакет. Например, первый конструктор в коде

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	тот же самый:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	Этот конструктор указывает, что при локальной отладке с помощью объединения и минификации ASP.NET должна выполняться обработка отдельных файлов скриптов, но с использованием указанного адреса CDN для доступа к соответствующему скрипту. Однако отметим две важных характеристики этого тщательно созданного URL-адреса CDN.
	
	-	Источником этого URL-адреса CDN служит адрес `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, который фактически является виртуальным каталогом пакета сценариев в облачной службе.
	-	Поскольку используется конструктор CDN, тег скрипта CDN для этого пакета больше не содержит автоматически созданную строку версии в обработанном URL-адресе. Необходимо вручную создавать уникальную строку версии каждый раз при изменении пакета скриптов, чтобы обеспечить промах кэша в Azure CDN. В то же время эта уникальная строка версии должна оставаться постоянной в течение всего срока существования развертывания для максимального увеличения попаданий в кэш в Azure CDN после развертывания пакета.
	-	Строка запроса v=<W.X.Y.Z> берется из файла *Properties\AssemblyInfo.cs* в проекте веб-роли. Можно создать рабочий процесс развертывания, включающий увеличение версии сборки при каждой публикации в Azure. Можно также просто изменить файл *Properties\AssemblyInfo.cs* в проекте, задав автоматическое увеличение строки версии при каждом построении с помощью подстановочного знака '*'. Например:
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		Здесь будет работать любая другая стратегия упрощения создания уникальной строки на срок существования развертывания.

3. Повторно опубликуйте облачную службу и откройте домашнюю страницу.
 
4. Просмотрите код HTML для этой страницы. При каждой публикации изменений облачной службы вы должны видеть обработанный URL-адрес CDN с уникальной строкой версии. Например:
	<pre class="prettyprint">
...

&lt;link href="http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>

&lt;script src="http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449">&lt;/script>

...

&lt;script src="http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25449">&lt;/script>

&lt;script src="http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449">&lt;/script>

...</pre>

5. Перейдите в режим отладки облачной службы в Visual Studio, нажав клавишу `F5`.

6. Просмотрите код HTML для этой страницы. Вы по-прежнему будете видеть каждый, индивидуально обработанный файл скрипта, так что можно получить последовательные возможности отладки в Visual Studio.
	<pre class="prettyprint">
...

    &lt;link href="/Content/bootstrap.css" rel="stylesheet"/>
&lt;link href="/Content/site.css" rel="stylesheet"/>

    &lt;script src="/Scripts/modernizr-2.6.2.js">&lt;/script>

...

    &lt;script src="/Scripts/jquery-1.10.2.js">&lt;/script>

    &lt;script src="/Scripts/bootstrap.js">&lt;/script>
&lt;script src="/Scripts/respond.js">&lt;/script>

...    
</pre>

<a name="fallback"></a>
## Резервный механизм для URL-адресов CDN ##

Желательно, чтобы в случае сбоя конечной точки Azure CDN по какой-либо причине веб-страница могла использовать в качестве резервной возможности доступ к исходному веб-серверу для загрузки JavaScript или Bootstrap. Одно дело – потерять изображения на веб-сайте из-за недоступности CDN, и совсем другое – потерять критически важные функциональные возможности страницы, обеспечиваемые сценариями и таблицами стилей.

Класс [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) содержит свойство с именем [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx), которое позволяет настраивать резервный механизм на случай сбоя CDN. Для использования этого свойства выполните приведенные ниже действия.

1. В проекте веб-роли откройте файл *App_Start\BundleConfig.cs*, где в каждом [конструкторе Bundle](http://msdn.microsoft.com/library/jj646464.aspx) добавлялся URL-адрес CDN, и внесите следующие выделенные изменения, чтобы добавить резервный механизм в пакеты по умолчанию:  
	<pre class="prettyprint">
public static void RegisterBundles(BundleCollection bundles)
{
    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
        .GetName().Version.ToString();
    var cdnUrl = "http://cdnurl.vo.msecnd.net/.../{0}?" + version;
    bundles.UseCdn = true;

    bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")) 
				<mark>{ CdnFallbackExpression = "window.jquery" }</mark>
                .Include("~/Scripts/jquery-{version}.js"));

    bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")) 
				<mark>{ CdnFallbackExpression = "$.validator" }</mark>
            	.Include("~/Scripts/jquery.validate*"));

    // Используйте версию разработки Modernizr как основу для обучения и разработки. Когда вы будете готовы
    // к развертыванию в рабочей среде, используйте инструмент создания по адресу http://modernizr.com, что выбирать только необходимые тесты.
    bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")) 
				<mark>{ CdnFallbackExpression = "window.Modernizr" }</mark>
				.Include("~/Scripts/modernizr-*"));

    bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")) 	
				<mark>{ CdnFallbackExpression = "$.fn.modal" }</mark>
        		.Include(
	              		"~/Scripts/bootstrap.js",
	              		"~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
}</pre>Когда выражение `CdnFallbackExpression` не пустое, скрипт внедряется в HTML, чтобы проверить успешность загрузки пакета, и в случае неуспешной загрузки обеспечивает доступ к пакету непосредственно на исходном веб-сервере. В этом свойстве необходимо устанавливать выражение JavaScript, проверяющее, загружен ли соответствующий пакет CDN должным образом. Выражения для проверки каждого пакета отличаются в соответствии с контентом. Для пакетов по умолчанию выше:
	
	-	`window.jquery` задается в jquery-{version}.js
	-	`$.validator` задается в jquery.validate.js;
	-	`window.Modernizr` задается в modernizer-{version}.js;
	-	`$.fn.modal` задается в bootstrap.js.
	
	Возможно, вы заметили, что я не установил CdnFallbackExpression для пакета `~/Cointent/css`. Это объясняется тем, что в настоящее время имеется [ошибка в System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104), которая вставляет тег `<script>` для резервной CSS вместо ожидаемого тега `<link>`.
	
	Однако существует хорошая [резервная форма пакета стилей](https://github.com/EmberConsultingGroup/StyleBundleFallback), предлагаемая [Ember Consulting Group](https://github.com/EmberConsultingGroup).

2. Чтобы использовать этот обходной путь для CSS, создайте в папке *App_Start* новый CS-файл *StyleBundleExtensions.cs* и замените его содержимое [кодом от GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).

4. В *App_Start\StyleFundleExtensions.cs* измените имя пространства имен на имя веб-роли (например, **WebRole1**).

3. Вернитесь в файл `App_Start\BundleConfig.cs` и замените последний оператор `bundles.Add` следующим выделенным кодом:
	<pre class="prettyprint">
bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
    .Include(
          "~/Content/bootstrap.css",
          "~/Content/site.css"));
</pre>Этот новый метод расширения использует ту же идею внедрения скрипта в HTML для поиска в модели DOM имени класса, имени правила и значения правила, соответствующих заданным в пакете CSS, и возвращения на исходный веб-сервер при отсутствии соответствия.

4. Снова опубликуйте облачную службу и откройте домашнюю страницу.
5. Просмотрите код HTML для этой страницы. Вы должны найти внедренные скрипты, аналогичные показанным ниже.    
	<pre class="prettyprint">...

	&lt;link href="http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
<mark>&lt;script>(function() {
                var loadFallback,
                    len = document.styleSheets.length;
                for (var i = 0; i &lt; len; i++) {
                    var sheet = document.styleSheets[i];
                    if (sheet.href.indexOf('http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474') !== -1) {
                        var meta = document.createElement('meta');
                        meta.className = 'sr-only';
                        document.head.appendChild(meta);
                        var value = window.getComputedStyle(meta).getPropertyValue('width');
                        document.head.removeChild(meta);
                        if (value !== '1px') {
                            document.write('&lt;link href="/Content/css" rel="stylesheet" type="text/css" />');
                        }
                    }
                }
                return true;
            }())||document.write('&lt;script src="/Content/css">&lt;/script>');&lt;/script></mark>

    &lt;script src="http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>(window.Modernizr)||document.write('&lt;script src="/bundles/modernizr">&lt;/script>');&lt;/script></mark>

...	

    &lt;script src="http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>(window.jquery)||document.write('&lt;script src="/bundles/jquery">&lt;/script>');&lt;/script></mark>

    &lt;script src="http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>($.fn.modal)||document.write('&lt;script src="/bundles/bootstrap">&lt;/script>');&lt;/script></mark>

...
</pre>Обратите внимание, что внедренный скрипт для пакета CSS по-прежнему содержит ошибочный фрагмент из свойства `CdnFallbackExpression` в следующей строке:

        }())||document.write('<script src="/Content/css"></script>');</script>

	Но поскольку первая часть выражения '||' будет всегда возвращать значение true (в строке прямо над этой), функция document.write() никогда не будет выполняться.

## Дополнительные сведения ##
- [Общие сведения о сети доставки контента (CDN) Azure](cdn-overview.md)
- [Обслуживание содержимого из CDN Azure в вашем веб-приложении](cdn-serve-content-from-cdn-in-your-web-application.md)
- [Использование Azure CDN в службе приложений Azure](../cdn-websites-with-cdn.md)
- [Объединение и минификация ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Использование CDN для Azure](cdn-how-to-use-cdn.md)
 

<!---HONumber=62-->