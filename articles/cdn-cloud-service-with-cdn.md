<properties linkid="cdn-cloud-service-with-cdn" urlDisplayName="Integrate a cloud service with Azure CDN" pageTitle="Integrate a cloud service with Azure CDN" metaKeywords="Azure tutorial, Azure web app tutorial, ASP.NET, CDN, MVC, cloud service" description="A tutorial that teaches you how to deploy a cloud service that serves content from an integrated Azure CDN endpoint" metaCanonical="" services="cdn,cloud-services" documentationCenter=".NET" title="Integrate a cloud service with Azure CDN" authors="cephalin" solutions="" manager="wpickett" editor="tysonn" />

<tags ms.service="cdn" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

<a name="intro"></a>

# Интеграция облачной службы с Azure CDN

***Обновление: 23 июля 2014 г.***

Azure CDN можно интегрировать с облачной службой для обслуживания контента из каталога CDN облачной службы. Такой подход обеспечивает следующие преимущества.

-   Упрощение развертывания и обновления образов, скриптов и таблиц стилей в каталогах проекта облачной службы.
-   Облегчение обновления пакетов NuGet в облачной службе, например, версий jQuery или Bootstrap.
-   Управление веб-приложениями и контентом, обслуживаемым CDN, из одного интерфейса Visual Studio.
-   Единый рабочий процесс развертывания для веб-приложения и контента, обслуживаемого CDN.
-   Интеграция объединения и минификации ASP.NET с Azure CDN.

## Что будет изучаться

Из этого учебника вы узнаете следующее:

-   [Интеграция конечной точки Azure CDN с облачной службой и обслуживание статического контента на веб-страницах из Azure CDN][Интеграция конечной точки Azure CDN с облачной службой и обслуживание статического контента на веб-страницах из Azure CDN]
-   [Настройка параметров кэша для статического контента в облачной службе][Настройка параметров кэша для статического контента в облачной службе]
-   [Обслуживание контента из действий контроллера посредством Azure CDN][Обслуживание контента из действий контроллера посредством Azure CDN]
-   [Обслуживание объединенного в пакет и минифицированного контента посредством Azure CDN с поддержкой интерфейса отладки скриптов в Visual Studio][Обслуживание объединенного в пакет и минифицированного контента посредством Azure CDN с поддержкой интерфейса отладки скриптов в Visual Studio]
-   [Настройка резервирования скриптов и CSS, когда Azure CDN находится в автономном режиме][Настройка резервирования скриптов и CSS, когда Azure CDN находится в автономном режиме]

## Что будет строиться

Будет выполняться развертывание веб-роли облачной службы с помощью шаблона MVC ASP.NET по умолчанию, добавление кода для обслуживания контента из интегрированной Azure CDN, такого как образ, результаты действий контроллера, файлы JavaScript и CSS по умолчанию, а также создание кода для настройки резервного механизма обслуживаемых пакетов в случае, когда CDN находится в автономном режиме.

## Что потребуется

Для работы с этим учебником необходимы следующие компоненты.

-   Активная [учетная запись Microsoft Azure][учетная запись Microsoft Azure].
-   Visual Studio 2013 с [с пакетом Azure SDK][с пакетом Azure SDK].

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Для работы с этим учебником требуется учетная запись Azure.</h5>
  <ul>
    <li>Вы можете <a href="http://azure.microsoft.com/ru-ru/pricing/free-trial/?WT.mc_id=A261C142F">открыть учетную запись Azure бесплатно</a> — вы получаете кредиты, которые можно использовать для опробования платных служб Azure, и даже после израсходования кредитов вы сохраняете учетную запись и возможность использовать бесплатные службы Azure, такие как веб-сайты.</li>
    <li>Вы имеете возможность <a href="http://azure.microsoft.com/ru-ru/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">активировать преимущества подписчика MSDN</a> — ваша подписка MSDN каждый месяц приносит вам кредиты, которые можно использовать для оплаты за службы Azure.</li>
  <ul>
</div>

<a name="deploy"></a>

## Развертывание облачной службы с интегрированной конечной точкой CDN

В этом разделе будет выполняться развертывание шаблона приложения MVC ASP.NET по умолчанию в Visual Studio 2013 в веб-роли облачной службы, а затем интеграция его с новой конечной точкой CDN. Выполните приведенные далее инструкции.

1.  В Visual Studio 2013 создайте новую облачную службу Azure из строки меню, последовательно выбрав пункты **Файл \> Создать \> Проект \> Облако \> Облачная служба Microsoft Azure**. Назначьте ей имя и нажмите кнопку **ОК**.

    ![][]

2.  Выберите **веб-роль ASP.NET** и нажмите кнопку со значком **\>**. Щелкните ОК.

    ![][1]

3.  Выберите **MVC** и нажмите кнопку **ОК**.

    ![][2]

4.  Теперь опубликуйте эту веб-роль в облачной службе Azure. Щелкните правой кнопкой мыши проект облачной службы и выберите **Опубликовать**.

    ![][3]

5.  Если вход в Microsoft Azure еще не выполнен, нажмите кнопку **Войти**.

    ![][4]

6.  На странице входа войдите с учетной записью Майкрософт, которая использовалась для активации учетной записи Azure.
7.  После входа нажмите кнопку **Далее**.

    ![][5]

8.  Если облачная служба или учетная запись хранения еще не создана, Visual Studio поможет создать их обе. В диалоговом окне **создания облачной службы и учетной записи** введите нужное имя службы. Затем нажмите кнопку **Создать**.

    ![][6]

    > [WACOM.NOTE] Обратите внимание, что в качестве региона для этой облачной службы я использую **Восточную Азию**. Моя цель заключается в том, чтобы облачная служба (и контент в учетной записи хранения) находились достаточно далеко от клиента (в данном случае мой компьютер находится в США) для последующего тестирования CDN.

9.  На странице параметров публикации проверьте конфигурацию и нажмите **Опубликовать**.

    ![][7]

    > [WACOM.NOTE] Процесс публикации облачных служб занимает много времени. Параметр включения веб-развертывания для всех ролей может значительно ускорить отладку облачной службы, обеспечивая быстрые (но временные) обновления веб-ролей. Дополнительные сведения об этом параметре см. в статье [Публикация облачной службы с помощью инструментов Azure][Публикация облачной службы с помощью инструментов Azure].

    Когда **журнал изменений Microsoft Azure** покажет, что публикация имеет состояние **Завершено**, будет создаваться конечная точка CDN, интегрированная с этой облачной службой.

10. Чтобы создать конечную точку CDN, войдите на [портал управления Azure][портал управления Azure].
11. Последовательно выберите пункты **Создать** \> **Службы приложений** \> **CDN** \> **Быстрое создание**. Выберите **http://*\<имя\_службы\>*.cloudapp.net/cdn/** и нажмите кнопку **Создать**.

    ![][8]

    > [WACOM.NOTE] После создания конечной точки CDN портал Azure покажет ее URL-адрес и исходный домен, с которым она интегрирована. Однако полное распространение конфигурации новой конечной точки CDN во все расположения узлов CDN может занимать некоторое время.

    Обратите внимание, что конечная точка CDN связана с путем **cdn/** облачной службы. Можно либо создать папку **cdn** в проекте **WebRole1**, либо использовать переопределение URL-адресов, чтобы разбивать все входящие ссылки на этот путь. В данном учебнике будет использоваться последний способ.

12. Вернитесь на портал Azure, перейдите на вкладку **CDN** и щелкните имя только что созданной конечной точки CDN.

    ![][9]

13. Нажмите **Включить строку запроса**, чтобы включать строки запроса в кэш CDN. После этого одна и та же ссылка, доступ к которой осуществляется разными строками запроса, будет кэшироваться в виде отдельных записей.

    ![][10]

    > [WACOM.NOTE] Хотя включение строки запроса в этом разделе учебника не обязательно, для удобства это можно сделать как можно раньше, поскольку распространение любого изменения во все узлы CDN занимает некоторое время, и нежелательно, чтобы контент без включенных строк запроса засорял кэш CDN (обновление контента CDN будет рассматриваться позднее).

14. Проверьте связь с конечной точкой CDN, чтобы убедиться, что она распространилась в узлы CDN. Может потребоваться подождать около часа, прежде чем конечная точка начнет отвечать на запросы проверки связи.

    ![][11]

15. Вернитесь в Visual Studio 2013, откройте файл **Web.config** в проекте **WebRole1** и добавьте в тег `<system.webServer>` следующий код:
	<pre class="prettyprint">
	&lt;system.webServer&gt;
	  <mark>&lt;rewrite&gt;
	    &lt;rules&gt;
	      &lt;rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;&gt;
	        &lt;match url=&quot;^cdn/(.*)$&quot;/&gt;
	        &lt;action type=&quot;Rewrite&quot; url=&quot;{R:1}&quot;/&gt;
	      &lt;/rule&gt;
	    &lt;/rules&gt;
	  &lt;/rewrite&gt;</mark>
      ...
	&lt;/system.webServer&gt;
	</pre>

16. Снова опубликуйте облачную службу. Щелкните правой кнопкой мыши проект облачной службы и выберите **Опубликовать**.

    ![][3]

17. Когда публикация перейдет в состояние **Завершено**, откройте окно браузера и перейдите по адресу **http://*\<Имя\_cdn\>*.vo.msecnd.net/Content/bootstrap.css**. В моей настройке это следующий URL-адрес:

        http://az632148.vo.msecnd.net/Content/bootstrap.css

    Он соответствует следующему исходному URL-адресу в конечной точке CDN:

        http://cephalinservice.cloudapp.net/cdn/Content/bootstrap.css

    После переопределения URL-адреса в моем веб-приложении фактический файл, который кэшируется в соответствующем кэше CDN, имеет следующий адрес:

        http://cephalinservice.cloudapp.net/Content/bootstrap.css

    При переходе по адресу **http://*\<Имя\_cdn\>*.vo.msecnd.net/Content/bootstrap.css** будет предложено загрузить файл bootstrap.css, происходящий из опубликованного веб-приложения.

    ![][12]

Аналогичным образом можно получать доступ к любому общедоступному URL-адресу в **http://*\<Имя\_службы\>*.cloudapp.net/** прямо из конечной точки CDN. Например:

-   к JS-файлу в пути /Script;
-   к любому файлу контента в пути /Content;
-   к любому контроллеру или действию;
-   если в конечной точке CDN включена строка запроса, то к любому URL-адресу со строкой запроса.

Фактически при использовании вышеприведенной конфигурации можно размещать всю облачную службу из **http://*\<Имя\_cdn\>*.vo.msecnd.net/**. Если я перейду по адресу **<http://az632148.vo.msecnd.net/>**, то получу результат действия с домашней страницы или страницы индексов.

![][13]

Однако это не означает, что следует всегда обслуживать всю облачную службу посредством Azure CDN. Далее приводятся некоторые предупреждения.

-   Для использования этого подхода весь сайт должен быть общедоступным, поскольку Azure CDN не может обслуживать конфиденциальный контент.
-   Если по какой-либо причине, будь то плановое обслуживание или ошибка пользователя, конечная точка CDN становится автономной, вся облачная служба становится автономной, если нельзя перенаправить клиентов на исходный URL-адрес **http://*\<Имя\_службы\>*.cloudapp.net/**.
-   Даже с настраиваемыми параметрами управления кэшем (см. раздел [Настройка параметров кэширования для статических файлов в облачной службе][Настройка параметров кэша для статического контента в облачной службе]) конечная точка CDN не улучшает производительность высокодинамичного контента. При попытке загрузить домашнюю страницу из конечной точки CDN, как показано выше, обратите внимание, что первая загрузка домашней страницы по умолчанию займет по крайней мере 5 секунд, хотя это довольно простая страница. Представьте, как бы выглядело взаимодействие с клиентом, если бы эта страница содержала динамический контент, который должен обновляться каждую минуту. Обслуживание динамического контента из конечной точки CDN требует, чтобы срок годности кэша был коротким,что приводит к частой потере кэша в конечной точке CDN. Это вредит производительности облачной службы, и смысл CDN теряется.

Альтернатива заключается в том, чтобы определить, какой контент должен обслуживаться из Azure CDN на индивидуальной основе в облачной службе. В отношении этого вы уже видели, как получать доступ к отдельным файлам контента из конечной точки CDN. Я покажу, как обслуживать определенное действие контроллера посредством конечной точки CDN, в разделе [Обслуживание контента из действий контроллера посредством Azure CDN][Обслуживание контента из действий контроллера посредством Azure CDN].

Можно указать более ограничивающее правило переопределения URL-адреса, чтобы ограничить контент, доступный через конечную точку CDN. Например, чтобы ограничить переопределение URL-адреса папкой *\\Scripts*, измените приведенное выше правило переопределения следующим образом:
<pre class="prettyprint">
&lt;rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;&gt;
  &lt;match url=&quot;^cdn/<mark>Scripts/</mark>(.*)$&quot;/&gt;
  &lt;action type=&quot;Rewrite&quot; url=&quot;<mark>Scripts/</mark>{R:1}&quot;/&gt;
&lt;/rule&gt;
</pre>

<a name="caching"></a>

## Настройка параметров кэширования для статических файлов в облачной службе

С помощью интеграции Azure CDN в облачной службе можно указать, как статический контент должен кэшироваться в конечной точке CDN. Для этого откройте файл *Web.config* из проекта веб-роли (например, WebRole1) и добавьте элемент `<staticContent>` в `<system.webServer>`. Следующий XML настраивает истечение срока годности кэша через 3 дня.
<pre class="prettyprint">
&lt;system.webServer&gt;
  <mark>&lt;staticContent&gt;
    &lt;clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/&gt;
  &lt;/staticContent&gt;</mark>
  ...
&lt;/system.webServer&gt;
</pre>

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

Дополнительные сведения о настройке элемента `<clientCache>` см. в статье [Кэш клиента \<Кэш\_клиента\>][Кэш клиента \<Кэш\_клиента\>].

В разделе [Обслуживание контента из действий контроллера посредством Azure CDN][Обслуживание контента из действий контроллера посредством Azure CDN] я также покажу, как можно настраивать параметры кэша для результатов действий контроллера в кэше CDN.

<a name="controller"></a>

## Обслуживание контента из действий контроллера посредством Azure CDN

При интеграции веб-роли облачной службы с Azure CDN сравнительно легко обслуживать контент от действий контроллера посредством Azure CDN. Помимо обслуживания облачной службы непосредственно через Azure CDN (показанного выше), [Мартин Баллиау (Maarten Balliauw)][Мартин Баллиау (Maarten Balliauw)] в видеоролике [Уменьшение задержки в интернете с помощью Microsoft Azure CDN][Уменьшение задержки в интернете с помощью Microsoft Azure CDN] показывает, как это делать с помощью контроллера MemeGenerator. Я просто воспроизведу это здесь.

Предположим, вы хотите создать в облачной службе мемы на основе образа молодого Чака Норриса (фото [Алана Лайта (Alan Light)][Алана Лайта (Alan Light)]):

![][14]

Имеется простое действие `Index`, которое позволяет клиентам задавать гиперболы в образе, а затем создает мем, как только клиент отправит их в действие. Поскольку это Чак Норрис, можно ожидать, что страница станет очень популярной по всему миру. Это хороший пример обслуживания полудинамического контента с помощью Azure CDN.

Чтобы настроить это действие контроллера, выполните следующие действия.

1.  В папке *\\Controllers* создайте новый CS-файл с именем *MemeGeneratorController.cs* и замените содержимое следующим кодом. Не забудьте заменить выделенные фрагменты именем CDN.
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
	
	namespace WebRole1.Controllers
	{
	    public class MemeGeneratorController : Controller
	    {
	        static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();

	        public ActionResult Index()
	        {
	            return View();
	        }
	
	        [HttpPost, ActionName(&quot;Index&quot;)]
        	public ActionResult Index_Post(string top, string bottom)
	        {
	            var identifier = Guid.NewGuid().ToString();
	            if (!Memes.ContainsKey(identifier))
	            {
	                Memes.Add(identifier, new Tuple&lt;string, string&gt;(top, bottom));
	            }
	
	            return Content(&quot;&lt;a href=\&quot;&quot; + Url.Action(&quot;Show&quot;, new {id = identifier}) + &quot;\&quot;&gt;here&#39;s your meme&lt;/a&gt;&quot;);
	        }


	        [OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Show(string id)
	        {
	            Tuple<string, string> data = null;
	            if (!Memes.TryGetValue(id, out data))
	            {
	                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
	            }
	
	            if (Debugger.IsAttached) // Preserve the debug experience
	            {
	                return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	            else // Get content from Azure CDN
	            {
	                return Redirect(string.Format(&quot;http://<mark>&lt;cdnName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
	            }
	        }

	        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Generate(string top, string bottom)
	        {
	            string imageFilePath = HostingEnvironment.MapPath(&quot;~/Content/chuck.bmp&quot;);
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
	            return File(ms.ToArray(), &quot;image/png&quot;);
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
	</pre>

2.  Щелкните правой кнопкой мыши действие `Index()` по умолчанию и выберите **Добавить представление**.

    ![][15]

3.  Примите параметры, показанные ниже, и нажмите кнопку **Добавить**.

    ![][16]

4.  Откройте новый файл *Views\\MemeGenerator\\Index.cshtml* и замените его содержимое следующим простым HTML для отправки гипербол:

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5.  Снова опубликуйте облачную службу и перейдите в браузере по адресу **<http://>*\<Имя\_службы\>*.cloudapp.net/MemeGenerator/Index**.

При отправке значений формы в `/MemeGenerator/Index` метод действия `Index_Post` возвращает ссылку на метод действия `Show` с соответствующим идентификатором ввода. Если щелкнуть ссылку, появляется следующий код:
<pre class="prettyprint">
[OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
public ActionResult Show(string id)
{
    Tuple<string, string> data = null;
    if (!Memes.TryGetValue(id, out data))
    {
        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
    }

    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(&quot;http://<mark>&lt;cdnName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

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

## Интеграция объединения и минификации с Azure CDN

Скрипты и таблицы стилей CSS изменяются нечасто и являются основными кандидатами для кэша Azure CDN. Обслуживание всей веб-роли посредством Azure CDN представляет простейший способ интеграции объединения и минификации с Azure CDN. Однако поскольку вы можете не хотеть это делать, я покажу, как это сделать, сохраняя желаемый интерфейс разработчика объединения и минификации ASP.NET со следующими характеристиками.

-   Отличное взаимодействие в режиме отладки.
-   Упрощенное развертывание.
-   Немедленные обновления клиентов при обновлении версий скриптов или CSS.
-   Резервный механизм на случай сбоя конечной точки CDN.
-   Минимальное изменение кода.

В проекте **WebRole1**, созданном в разделе [TODO][TODO], откройте файл *App\_Start\\BundleConfig.cs* и взгляните на вызовы метода `bundles.Add()`.

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

Однако когда этот код запускается в Visual Studio путем нажатия клавиши `F5`, он будет обрабатывать каждый файл скрипта в пакете индивидуально (в вышеприведенном случае в пакете имеется только один файл скрипта):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Это позволяет отлаживать код JavaScript в среде разработки, уменьшая число параллельных клиентских соединений (объединение) и улучшая производительность загрузки файлов (минификация) в рабочей среде. Это отличная возможность для сохранения с использованием интеграции Azure CDN. Более того, поскольку обработанный пакет уже содержит автоматически созданную строку версии, имеет смысл реплицировать эту функциональность, чтобы при всяком обновлении версии jQuery с помощью NuGet она могла бы максимально быстро обновляться на стороне клиента.

Выполните приведенные ниже действия для интеграции объединения и минификации ASP.NET с конечной точкой CDN.

1.  Вернитесь в файл *App\_Start\\BundleConfig.cs* и измените методы `bundles.Add()`, чтобы использовать другой [конструктор Bundle][конструктор Bundle], который задает адрес CDN. Для этого замените определение метода `RegisterBundles` следующим кодом:
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    <mark>bundles.UseCdn = true;
	    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://&lt;yourCDNName&gt;.vo.msecnd.net/{0}?v=&quot; + version;</mark>
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jquery&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;<mark>, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)</mark>).Include(
	                &quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;<mark>, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)</mark>).Include(
	                &quot;~/Scripts/bootstrap.js&quot;,
	                &quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;<mark>, string.Format(cdnUrl, &quot;Content/css&quot;)</mark>).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}
	</pre>
    Не забудьте заменить `<yourCDNName>` своим именем CDN.

    Простыми словами, устанавливается `bundles.UseCdn = true` и добавляется тщательно созданный URL-адрес CDN в каждый пакет. Например, первый конструктор в коде

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

    тот же самый:

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

    Этот конструктор указывает, что при локальной отладке с помощью объединения и минификации ASP.NET должна выполняться обработка отдельных файлов скриптов, но с использованием указанного адреса CDN для доступа к соответствующему скрипту. Однако отметим две важных характеристики этого тщательно созданного URL-адреса CDN.

    -   Источником этого URL-адреса CDN служит адрес `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, который фактически является виртуальным каталогом пакета скриптов в облачной службе.
    -   Поскольку используется конструктор CDN, тег скрипта CDN для этого пакета больше не содержит автоматически созданную строку версии в обработанном URL-адресе. Необходимо вручную создавать уникальную строку версии каждый раз при изменении пакета скриптов, чтобы обеспечить промах кэша в Azure CDN. В то же время эта уникальная строка версии должна оставаться постоянной в течение всего срока существования развертывания для максимального увеличения попаданий в кэш в Azure CDN после развертывания пакета.
    -   Строка запроса v=<w.x.y.z> берется из файла *Properties\\AssemblyInfo.cs* в проекте веб-роли. Можно создать рабочий процесс развертывания, включающий увеличение версии сборки при каждой публикации в Azure. Можно также просто изменить файл *Properties\\AssemblyInfo.cs* в проекте, задав автоматическое увеличение строки версии при каждом построении с помощью подстановочного знака '\*'. Например:

            [assembly: AssemblyVersion("1.0.0.*")]

        Здесь будет работать любая другая стратегия упрощения создания уникальной строки на срок существования развертывания.

2.  Повторно опубликуйте облачную службу и откройте домашнюю страницу.

3.  Просмотрите код HTML для этой страницы. При каждой публикации изменений облачной службы вы должны видеть обработанный URL-адрес CDN с уникальной строкой версии. Например:
	<pre class="prettyprint">
	...

    &lt;link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/&gt;

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...</pre>
4.  Перейдите в режим отладки облачной службы в Visual Studio, нажав клавишу `F5`.

5.  Просмотрите код HTML для этой страницы. Вы по-прежнему будете видеть каждый индивидуально обработанный файл скрипта, так что можно получить последовательные возможности отладки в Visual Studio.
	<pre class="prettyprint">
	...
	
	    &lt;link href=&quot;/Content/bootstrap.css&quot; rel=&quot;stylesheet&quot;/&gt;
	&lt;link href=&quot;/Content/site.css&quot; rel=&quot;stylesheet&quot;/&gt;
	
	    &lt;script src=&quot;/Scripts/modernizr-2.6.2.js&quot;&gt;&lt;/script&gt;
	
	...
	
	    &lt;script src=&quot;/Scripts/jquery-1.10.2.js&quot;&gt;&lt;/script&gt;
	
	    &lt;script src=&quot;/Scripts/bootstrap.js&quot;&gt;&lt;/script&gt;
	&lt;script src=&quot;/Scripts/respond.js&quot;&gt;&lt;/script&gt;
	
	...    
	</pre>

<a name="fallback"></a>

## Резервный механизм для URL-адресов CDN

Желательно, чтобы в случае сбоя конечной точки Azure CDN по какой-либо причине веб-страница могла использовать в качестве резервной возможности доступ к исходному веб-серверу для загрузки JavaScript или Bootstrap. Одно дело потерять изображения на веб-сайте из-за недоступности CDN, и совсем другое — потерять критически важные функциональные возможности страницы, обеспечиваемые скриптами и таблицами стилей.

Класс [Bundle][Bundle] содержит свойство с именем [CdnFallbackExpression][CdnFallbackExpression], которое позволяет настраивать резервный механизм на случай сбоя CDN. Для использования этого свойства выполните приведенные ниже действия.

1.  В проекте веб-роли откройте файл *App\_Start\\BundleConfig.cs*, где в каждом [конструкторе Bundle][конструктор Bundle] добавлялся URL-адрес CDN, и внесите следующие выделенные изменения, чтобы добавить резервный механизм в пакеты по умолчанию:
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://cdnurl.vo.msecnd.net/.../{0}?&quot; + version;
	    bundles.UseCdn = true;
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;, string.Format(cdnUrl, &quot;bundles/jquery&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.jquery&quot; }</mark>
	                .Include(&quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;$.validator&quot; }</mark>
	            	.Include(&quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.Modernizr&quot; }</mark>
					.Include(&quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)) 	
					<mark>{ CdnFallbackExpression = &quot;$.fn.modal&quot; }</mark>
	        		.Include(
		              		&quot;~/Scripts/bootstrap.js&quot;,
		              		&quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;, string.Format(cdnUrl, &quot;Content/css&quot;)).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}</pre>
    Когда выражение `CdnFallbackExpression` не пустое, скрипт внедряется в HTML, чтобы проверить успешность загрузки пакета, и в случае неуспешной загрузки обеспечивает доступ к пакету непосредственно на исходном веб-сервере. В этом свойстве необходимо устанавливать выражение JavaScript, проверяющее, загружен ли соответствующий пакет CDN должным образом. Выражения для проверки каждого пакета отличаются в соответствии с контентом. Для пакетов по умолчанию выше:

    -   `window.jquery` задается в jquery-{version}.js;
    -   `$.validator` задается в jquery.validate.js;
    -   `window.Modernizr` задается в modernizer-{version}.js;
    -   `$.fn.modal` задается в bootstrap.js.

    Возможно, вы заметили, что я не установил CdnFallbackExpression для пакета `~/Cointent/css`. Это объясняется тем, что в настоящее время имеется [ошибка в System.Web.Optimization][ошибка в System.Web.Optimization], которая вставляет тег `<script>` для резервной CSS вместо ожидаемого тега `<link>`.

    Однако существует хорошая [резервная форма пакета стилей][резервная форма пакета стилей], предлагаемая [Ember Consulting Group][Ember Consulting Group].

2.  Чтобы использовать этот обходной путь, создайте новый CS-файл с именем *StyleBundleExtensions.cs* в папке *App\_Start* проекта веб-роли и замените его содержимое [кодом от GitHub][кодом от GitHub].

3.  В *App\_Start\\StyleFundleExtensions.cs* измените имя пространства имен на имя веб-роли (например, **WebRole1**).

4.  Вернитесь в `App_Start\BundleConfig.cs` и измените последний оператор `bundles.Add` следующим выделенным кодом:

	<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>
    Этот новый метод расширения использует ту же идею внедрения скрипта в HTML для поиска в модели DOM имени класса, имени правила и значения правила, соответствующих заданным в пакете CSS, и возвращения на исходный веб-сервер при отсутствии соответствия.

5.  Снова опубликуйте облачную службу и откройте домашнюю страницу.
6.  Просмотрите код HTML для этой страницы. Вы должны найти внедренные скрипты, аналогичные показанным ниже.
	<pre class="prettyprint">...
	
		&lt;link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/&gt;
	<mark>&lt;script&gt;(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i &lt; len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf(&#39;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&#39;) !== -1) {
	                        var meta = document.createElement(&#39;meta&#39;);
	                        meta.className = &#39;sr-only&#39;;
	                        document.head.appendChild(meta);
	                        var value = window.getComputedStyle(meta).getPropertyValue(&#39;width&#39;);
	                        document.head.removeChild(meta);
	                        if (value !== &#39;1px&#39;) {
	                            document.write(&#39;&lt;link href=&quot;/Content/css&quot; rel=&quot;stylesheet&quot; type=&quot;text/css&quot; /&gt;&#39;);
	                        }
	                    }
	                }
	                return true;
	            }())||document.write(&#39;&lt;script src=&quot;/Content/css&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.Modernizr)||document.write(&#39;&lt;script src=&quot;/bundles/modernizr&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...	
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.jquery)||document.write(&#39;&lt;script src=&quot;/bundles/jquery&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;($.fn.modal)||document.write(&#39;&lt;script src=&quot;/bundles/bootstrap&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...
	</pre>
    Обратите внимание, что внедренный скрипт для пакета CSS по-прежнему содержит ошибочный фрагмент из свойства `CdnFallbackExpression` в следующей строке:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

    Но поскольку первая часть выражения '||' будет всегда возвращать значение true (в строке прямо над этой), функция document.write() никогда не будет выполняться.

# Дополнительные сведения

-   [Обзор сети доставки содержимого Azure (CDN)][Обзор сети доставки содержимого Azure (CDN)]
-   [Обслуживание содержимого из Azure CDN в приложении][Обслуживание содержимого из Azure CDN в приложении]
-   [Объединение и минификация ASP.NET][Объединение и минификация ASP.NET]
-   [Использование CDN в Azure][Использование CDN в Azure]

  [Интеграция конечной точки Azure CDN с облачной службой и обслуживание статического контента на веб-страницах из Azure CDN]: #deploy
  [Настройка параметров кэша для статического контента в облачной службе]: #caching
  [Обслуживание контента из действий контроллера посредством Azure CDN]: #controller
  [Обслуживание объединенного в пакет и минифицированного контента посредством Azure CDN с поддержкой интерфейса отладки скриптов в Visual Studio]: #bundling
  [Настройка резервирования скриптов и CSS, когда Azure CDN находится в автономном режиме]: #fallback
  [учетная запись Microsoft Azure]: http://azure.microsoft.com/ru-ru/account/
  [с пакетом Azure SDK]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [открыть учетную запись Azure бесплатно]: http://azure.microsoft.com/ru-ru/pricing/free-trial/?WT.mc_id=A261C142F
  [активировать преимущества подписчика MSDN]: http://azure.microsoft.com/ru-ru/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  []: media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG
  [1]: media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG
  [2]: media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG
  [3]: media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png
  [4]: media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png
  [5]: media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png
  [6]: media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png
  [7]: media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png
  [Публикация облачной службы с помощью инструментов Azure]: http://msdn.microsoft.com/ru-ru/library/ff683672.aspx
  [портал управления Azure]: http://manage.windowsazure.com/
  [8]: media/cdn-cloud-service-with-cdn/cdn-cs-10-createcdn.png
  [9]: media/cdn-cloud-service-with-cdn/cdn-cs-11-disablequerya.png
  [10]: media/cdn-cloud-service-with-cdn/cdn-cs-12-disablequeryb.png
  [11]: media/cdn-cloud-service-with-cdn/cdn-cs-13-testcdn.png
  [12]: media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG
  [13]: media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG
  [Кэш клиента \<Кэш\_клиента\>]: http://www.iis.net/configreference/system.webserver/staticcontent/clientcache
  [Мартин Баллиау (Maarten Balliauw)]: https://twitter.com/maartenballiauw
  [Уменьшение задержки в интернете с помощью Microsoft Azure CDN]: http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN
  [Алана Лайта (Alan Light)]: http://www.flickr.com/photos/alan-light/218493788/
  [14]: media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG
  [15]: media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG
  [16]: media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG
  [TODO]: #integrate
  [конструктор Bundle]: http://msdn.microsoft.com/ru-ru/library/jj646464.aspx
  [Bundle]: http://msdn.microsoft.com/ru-ru/library/system.web.optimization.bundle.aspx
  [CdnFallbackExpression]: http://msdn.microsoft.com/ru-ru/library/system.web.optimization.bundle.cdnfallbackexpression.aspx
  [ошибка в System.Web.Optimization]: https://aspnetoptimization.codeplex.com/workitem/104
  [резервная форма пакета стилей]: https://github.com/EmberConsultingGroup/StyleBundleFallback
  [Ember Consulting Group]: https://github.com/EmberConsultingGroup
  [кодом от GitHub]: https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs
  [Обзор сети доставки содержимого Azure (CDN)]: http://msdn.microsoft.com/library/azure/ff919703.aspx
  [Обслуживание содержимого из Azure CDN в приложении]: http://azure.microsoft.com/ru-ru/Documentation/Articles/cdn-serve-content-from-cdn-in-your-web-application/
  [Объединение и минификация ASP.NET]: http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification
  [Использование CDN в Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/cdn-how-to-use/
