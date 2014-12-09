<properties urlDisplayName="Integrate a cloud service with Azure CDN" pageTitle="Интеграция облачной службы с Azure CDN" metaKeywords="Azure tutorial, Azure web app tutorial, ASP.NET, CDN, MVC, cloud service" description="A tutorial that teaches you how to deploy a cloud service that serves content from an integrated Azure CDN endpoint" metaCanonical="" services="cdn,cloud-services" documentationCenter=".NET" title="Integrate a cloud service with Azure CDN" authors="cephalin" solutions="" manager="wpickett" editor="tysonn" />

<tags ms.service="cdn" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/02/2014" ms.author="cephalin" />

<a name="intro"></a>
# Интеграция облачной службы с Azure CDN #

Облачную службу можно интегрировать с Azure CDN, обслуживающий любое содержимое по пути облачной службы `~/CDN`. Такой подход обеспечивает следующие преимущества:

- Упрощение развертывания и обновления образов, скриптов и таблиц стилей в каталогах проекта облачной службы.
- Облегчение обновления пакетов NuGet в облачной службе, например, версий jQuery или Bootstrap. 
- Управление веб-приложениями и контентом, обслуживаемым CDN, из одного интерфейса Visual Studio.
- Единый рабочий процесс развертывания для веб-приложения и контента, обслуживаемого CDN.
- Интеграция объединения и минификации ASP.NET с Azure CDN.

## Новые знания ##

Из этого учебника вы узнаете следующее:

-	[Интеграция конечной точки Azure CDN с облачной службой и обслуживание статического контента на веб-страницах из Azure CDN](#deploy)
-	[Настройка параметров кэша для статического контента в облачной службе](#caching)
-	[Обслуживание контента из действий контроллера посредством Azure CDN](#controller)
-	[Обслуживание объединенного в пакет и минифицированного контента посредством Azure CDN с поддержкой интерфейса отладки скриптов в Visual Studio](#bundling)
-	[Настройка резервирования скриптов и CSS, когда Azure CDN находится в автономном режиме](#fallback) 

## Что будет строиться ##

Будет выполняться развертывание веб-роли облачной службы с помощью шаблона MVC ASP.NET по умолчанию, добавление кода для обслуживания контента из интегрированной Azure CDN, такого как образ, результаты действий контроллера, файлы JavaScript и CSS по умолчанию, а также создание кода для настройки резервного механизма обслуживаемых пакетов в случае, когда CDN находится в автономном режиме.

## Необходимые условия ##

Для работы с этим учебником необходимо выполнить следующие условия.

-	Активная учетная запись [Microsoft Azure](http://azure.microsoft.com/ru-ru/account/).
-	Visual Studio 2013 с [пакетом SDK для Azure](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409).

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Для работы с этим учебником требуется учетная запись Azure.</h5>
  <ul>
    <li>Вы можете <a href="http://azure.microsoft.com/ru-ru/pricing/free-trial/?WT.mc_id=A261C142F">открыть учетную запись Azure бесплатно</a> - вы получаете кредиты, которые можно использовать для опробования платных служб Azure, и даже после израсходования кредитов вы сохраняете учетную запись и возможность использовать бесплатные службы Azure, например веб-сайты.</li>
    <li>Вы имеете возможность <a href="http://azure.microsoft.com/ru-ru/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">активировать преимущества подписчика MSDN</a> - ваша подписка MSDN каждый месяц приносит вам кредиты, которые можно использовать для оплаты служб Azure.</li>
  <ul>
</div>

<a name="deploy"></a>
## Развертывание облачной службы с интегрированной конечной точкой CDN ##

В этом разделе будет выполняться развертывание шаблона приложения MVC ASP.NET по умолчанию в Visual Studio 2013 в веб-роли облачной службы, а затем его интеграция с новой конечной точкой CDN. Следуйте приведенным далее указаниям.

1. Из строки меню в Visual Studio 2013 создайте новую облачную службу Azure, последовательно выбрав **Файл > Создать > Проект > Облако > Облачная служба Microsoft Azure**. Назначьте ей имя и нажмите кнопку **ОК**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. Выберите **веб-роль ASP.NET** и нажмите кнопку со значком **>**. Нажмите кнопку "ОК".

	![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. Выберите **MVC** и нажмите кнопку **ОК**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. Теперь опубликуйте эту веб-роль в облачной службе Azure. Щелкните правой кнопкой мыши проект облачной службы и выберите **Опубликовать**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. Если вход в Microsoft Azure еще не выполнен, нажмите кнопку **Войти**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. На странице входа войдите с учетной записью Майкрософт, которая использовалась для активации учетной записи Azure.
7. После входа нажмите кнопку **Далее**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. Если облачная служба или учетная запись хранения еще не создана, Visual Studio поможет создать их. В диалоговом окне **создания облачной службы и учетной записи** введите нужное имя службы. Затем щелкните **Создать**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. На странице параметров публикации проверьте конфигурацию и щелкните **Опубликовать**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

	>[WACOM.NOTE] Процесс публикации облачных служб занимает много времени. Параметр включения веб-развертывания для всех ролей может значительно ускорить отладку облачной службы, обеспечивая быстрые (но временные) обновления веб-ролей. Дополнительную информацию об этом параметре см. в статье [Публикация облачной службы с помощью инструментов Azure](http://msdn.microsoft.com/ru-ru/library/ff683672.aspx).

	Когда **журнал изменений Microsoft Azure** покажет, что публикация имеет состояние **Завершено**, будет создаваться конечная точка CDN, интегрированная с этой облачной службой. 

1. Чтобы создать конечную точку CDN, войдите на [портал управления Azure](http://manage.windowsazure.com/). 
2. Щелкните **Создать** > **Службы приложений** > **CDN** > **Быстро создать**. Выберите **http://*<<имя_службы>*.cloudapp.net/cdn/** и щелкните **Создать**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-10-createcdn.png)

	>[WACOM.NOTE] После создания конечной точки CDN портал Azure покажет ее URL-адрес и исходный домен, с которым она интегрирована. Однако полное распространение конфигурации новой конечной точки CDN во все расположения узлов CDN может занять некоторое время. 

	Обратите внимание, что конечная точка CDN связана с путем **cdn/** вашей облачной службы. Можно либо создать папку **cdn** в проекте **WebRole1**, либо использовать переопределение URL-адресов, чтобы разбивать все входящие ссылки на этот путь. В данном учебнике будет использоваться последний способ.

3. На портале Azure откройте вкладку **CDN** и щелкните имя только что созданной конечной точки CDN.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-11-disablequerya.png)

3. Щелкните **Включить поддержку строк запросов**, чтобы включать строки запроса в кэш CDN. После этого те же самые ссылки, доступ к которым осуществляется с помощью разных строк запроса, будут кэшироваться как отдельные записи.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-12-disablequeryb.png)

	>[WACOM.NOTE] Хотя включение поддержки строки запроса в этом разделе учебника не обязательно, для удобства это можно сделать как можно раньше, так как распространение любого изменения во все узлы CDN занимает некоторое время, и нежелательно, чтобы содержание без включенных строк запроса засорял кэш CDN (обновление контента CDN будет рассматриваться позднее).

3. Проверьте связь с конечной точкой CDN, чтобы убедиться, что она распространилась в узлы CDN. Может потребоваться подождать около часа, прежде чем конечная точка начнет отвечать на запросы проверки связи.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-13-testcdn.png)

2. Вернитесь в Visual Studio 2013, откройте файл **Web.config** в проекте **WebRole1** и добавьте в тег `<system.webServer>` следующий код:  
	<pre class="prettyprint">
	<system.webServer>
	  <mark><rewrite>
	    <rules>
	      <rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;>
	        <match url=&quot;^cdn/(.*)$&quot;/>
	        <action type=&quot;Rewrite&quot; url=&quot;{R:1}&quot;/>
	      </rule>
	    </rules>
	  </rewrite></mark>
      ...
	</system.webServer>
	</pre>

4. Снова опубликуйте облачную службу. Щелкните правой кнопкой мыши проект облачной службы и выберите **Опубликовать**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

1. Когда публикация перейдет в состояние **Завершено**, откройте окно браузера и перейдите по адресу **http://*<<имя_cdn>*.vo.msecnd.net/Content/bootstrap.css**. В моей настройке это следующий URL-адрес:

		http://az632148.vo.msecnd.net/Content/bootstrap.css

	Он соответствует следующему исходному URL-адресу в конечной точке CDN:

		http://cephalinservice.cloudapp.net/cdn/Content/bootstrap.css

	После переопределения URL-адреса в моем веб-приложении фактический файл, который кэшируется в соответствующем кэше CDN, имеет следующий адрес:

		http://cephalinservice.cloudapp.net/Content/bootstrap.css

	При переходе по адресу **http://*<<имя_cdn>*.vo.msecnd.net/Content/bootstrap.css** будет предложено скачать файл bootstrap.css, полученный из опубликованного веб-приложения. 

	![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

Аналогичным образом можно обращаться к любому общедоступному URL-адресу в **http://*<<имя_службы>*.cloudapp.net/** прямо из конечной точки CDN. Например: 

-	к JS-файлу в пути /Script;
-	к любому файлу контента в пути /Content;
-	к любому контроллеру или действию; 
-	если в конечной точке CDN включена строка запроса, то к любому URL-адресу со строкой запроса.

Фактически при использовании вышеприведенной конфигурации вы можете разместить всю облачную службу из **http://*<<имя_cdn>*.vo.msecnd.net/**. Если я перейду по адресу **http://az632148.vo.msecnd.net/**, то получу результат действия с домашней страницы или страницы индексов.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Однако это не означает, что следует всегда обслуживать всю облачную службу с помощью Azure CDN. Вот некоторые предупреждения:

-	Для использования этого подхода весь сайт должен быть общедоступным, поскольку Azure CDN не может обслуживать конфиденциальный контент.
-	Если по какой-либо причине, будь то плановое обслуживание или ошибка пользователя, конечная точка CDN становится автономной, вся облачная служба становится автономной, если нельзя перенаправить клиентов на исходный URL-адрес **http://*<<имя_службы>*.cloudapp.net/**. 
-	Даже с настраиваемыми параметрами управления кэшем (см. раздел [Настройка параметров кэширования для статических файлов в облачной службе](#caching)) конечная точка CDN не улучшает производительность высокодинамичного содержимого. При попытке загрузить домашнюю страницу из конечной точки CDN, как показано выше, обратите внимание, что первая загрузка домашней страницы по умолчанию займет по крайней мере 5 секунд, хотя это довольно простая страница. Представьте, как бы выглядело взаимодействие с клиентом, если бы эта страница содержала динамический контент, который должен обновляться каждую минуту. Обслуживание динамического контента из конечной точки CDN требует, чтобы срок годности кэша был коротким,что приводит к частой потере кэша в конечной точке CDN. Это вредит производительности облачной службы, и смысл CDN теряется.

Альтернатива заключается в том, чтобы определить, какое содержимое должно обслуживаться из Azure CDN на индивидуальной основе в облачной службе. В отношении этого вы уже видели, как получать доступ к отдельным файлам контента из конечной точки CDN. Я покажу, как обслуживать определенное действие контроллера с помощью конечной точки CDN, в разделе [Обслуживание содержимого из действий контроллера с помощью Azure CDN](#controller).

Можно указать более ограничивающее правило переопределения URL-адреса, чтобы ограничить содержимое, доступное через конечную точку CDN. Например, чтобы ограничить переопределение URL-адреса папкой *\Scripts*, измените приведенное выше правило переопределения следующим образом:   
<pre class="prettyprint">
<rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;>
  <match url=&quot;^cdn/<mark>Scripts/</mark>(.*)$&quot;/>
  <action type=&quot;Rewrite&quot; url=&quot;<mark>Scripts/</mark>{R:1}&quot;/>
</rule>
</pre>

<a name="caching"></a>
## Настройка параметров кэширования для статических файлов в облачной службе ##

С помощью интеграции Azure CDN в облачной службе можно указать, как статическое содержимое должно кэшироваться в конечной точке CDN. Для этого откройте файл *Web.config* из проекта веб-роли (например, WebRole1) и добавьте элемент `<staticContent>` в `<system.webServer>`. Следующий XML настраивает истечение срока годности кэша через 3 дня.  
<pre class="prettyprint">
<system.webServer>
  <mark><staticContent>
    <clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/>
  </staticContent></mark>
  ...
</system.webServer>
</pre>

После этого все статические файлы в облачной службе будут соблюдать то же правило в кэше CDN. Для более детального управления параметрами кэша добавьте файл *Web.config* в папку и добавьте в этом месте свои параметры. Например, добавьте файл *Web.config* в папку *\Content* и замените содержимое следующим кодом XML:

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

Этот параметр приводит к сохранению в кэше всех статических файлов из папки *\Content* на 15 дней.

Дополнительную информацию о настройке элемента `<clientCache>` см. в разделе [Клиент кэша: <<clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

В разделе [Обслуживание содержимого из действий контроллера с помощью Azure CDN](#controller) я также покажу, как можно настраивать параметры кэша для результатов действий контроллера в кэше CDN.

<a name="controller"></a>
## Обслуживание контента из действий контроллера посредством Azure CDN ##

При интеграции веб-роли облачной службы с Azure CDN сравнительно легко обслуживать содержимое от действий контроллера, используя Azure CDN. Помимо обслуживания облачной службы непосредственно через Azure CDN (показанного выше), [Мартин Баллиау (Maarten Balliauw)](https://twitter.com/maartenballiauw) в видеоролике [Уменьшение задержки в интернете с помощью Microsoft Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN) показывает, как это делать с помощью контроллера MemeGenerator. Я просто воспроизведу это здесь.

Предположим, вы хотите создать в облачной службе мемы на основе образа молодого Чака Норриса (фото [Алана Лайта (Alan Light)](http://www.flickr.com/photos/alan-light/218493788/)), например:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Есть простое действие `Index`, которое позволяет клиентам задавать гиперболы в образе, а затем создает мем, как только клиент поместит их в действие. Поскольку это Чак Норрис, можно ожидать, что страница станет очень популярной по всему миру. Это хороший пример обслуживания полудинамического содержимого с помощью Azure CDN. 

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
	
	namespace WebRole1.Controllers
	{
	    public class MemeGeneratorController : Controller
	    {
	        static readonly Dictionary<string, Tuple<string ,string&gt;> Memes = new Dictionary<string, Tuple<string, string&gt;>();

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
	                Memes.Add(identifier, new Tuple<string, string>(top, bottom));
	            }
	
	            return Content(&quot;<a href=\&quot;&quot; + Url.Action(&quot;Show&quot;, new {id = identifier}) + &quot;\&quot;>here&#39;s your meme</a>&quot;);
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
	                return Redirect(string.Format(&quot;http://<mark><yourCdnName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	        }

	        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Generate(string top, string bottom)
	        {
	            string imageFilePath = HostingEnvironment.MapPath(&quot;<mark>~/Content/chuck.bmp</mark>&quot;);
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

5. Снова опубликуйте облачную службу и перейдите в браузере по адресу **http://*<<имя_службы>*.cloudapp.net/MemeGenerator/Index**. 

При отправке формы значения в `/MemeGenerator/Index` метод действия `Index_Post` возвращает ссылку на метод действия `Show` с соответствующими входным идентификатором. Если щелкнуть ссылку, появляется следующий код:  
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
        return Redirect(string.Format(&quot;http://<mark><cdnName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

Если подключен локальный отладчик, то вы получите обычный интерфейс отладки с локальным перенаправлением. Если отладчик работает в облачной службе, то перенаправление будет выполняться сюда:

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Он соответствует следующему исходному URL-адресу в конечной точке CDN:

	http://<youCloudServiceName>.cloudapp.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

После правила переопределения URL-адреса, применявшегося ранее, фактический файл, который кэшируется в конечной точке CDN, имеет следующий адрес:

	http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Затем с помощью атрибута `OutputCacheAttribute` в методе `Generate` вы можете указать, как должен кэшироваться результат действия, которое будет выполнять Azure CDN. В следующем коде задается срок действия кэша в 1 час (3600 секунд).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Аналогично можно обслуживать контент из любого действия контроллера в облачной службе посредством Azure CDN с использованием нужного параметра кэширования.

В следующем разделе будет показано, как обслуживать объединенные в пакет и минифицированные скрипты и CSS посредством Azure CDN. 

<a name="bundling"></a>
## Интеграция объединения и минификации ASP.NET с Azure CDN. ##

Сценарии и таблицы стилей CSS изменяются нечасто и являются основными кандидатами для кэша Azure CDN. Обслуживание всей веб-роли посредством Azure CDN представляет простейший способ интеграции объединения и минификации с Azure CDN. Однако, поскольку вам это может быть не нужно, я покажу, как это сделать, сохраняя желаемый интерфейс разработчика объединения и минификации ASP.NET со следующими характеристиками.

-	Отличное взаимодействие в режиме отладки.
-	Упрощенное развертывание.
-	Немедленные обновления клиентов при обновлении версий скриптов или CSS.
-	Резервный механизм на случай сбоя конечной точки CDN.
-	Минимальное изменение кода.

В проекте **WebRole1**, созданном при изучении раздела [Интеграция конечной точки Azure CDN с веб-сайтом Azure и обслуживание статического содержимого на веб-страницах из Azure CDN](#deploy), откройте *App_Start\BundleConfig.cs* и взгляните на вызовы метода `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

Первая инструкция `bundles.Add()` добавляет пакет сценариев в виртуальный каталог `~/bundles/jquery`. Затем откройте файл *Views\Shared_Layout.cshtml*, чтобы посмотреть, как обрабатывается тег пакета сценариев. Вы должны найти следующую строку кода Razor:

    @Scripts.Render("~/bundles/jquery")

При запуске этого кода Razor в веб-роли Azure он будет обрабатывать тег `<script>` для пакета сценариев следующим образом: 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Однако когда этот код запускается в Visual Studio по нажатию клавиши `F5`, он будет обрабатывать каждый файл сценария в пакете отдельно (в приведенном случае в пакете имеется только один файл сценария):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Это позволяет отлаживать код JavaScript в среде разработки, уменьшая число параллельных клиентских соединений (объединение) и повышая скорость скачивания файлов (минификация) в рабочей среде. Это отличная возможность для сохранения с использованием интеграции Azure CDN. Более того, так как обработанный пакет уже содержит автоматически созданную строку версии, имеет смысл реплицировать эту функциональность, чтобы при всяком обновлении версии jQuery с помощью NuGet она могла бы максимально быстро обновляться на стороне клиента.

Выполните приведенные ниже действия для интеграции объединения и минификации ASP.NET с конечной точкой CDN.

1. Вернитесь в файл *App_Start\BundleConfig.cs* и измените методы `bundles.Add()`, чтобы использовать другой [конструктор Bundle](http://msdn.microsoft.com/ru-ru/library/jj646464.aspx), который задает адрес CDN. Для этого замените определение метода `RegisterBundles` следующим кодом:  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    <mark>bundles.UseCdn = true;
	    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://<yourCDNName>.vo.msecnd.net/{0}?v=&quot; + version;</mark>
	
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

	Не забудьте заменить `<yourCDNName>` своим именем Azure CDN.

	Простыми словами, вы задаете `bundles.UseCdn = true` и добавляете тщательно созданный URL-адрес CDN в каждый пакет. Например, первый конструктор в коде:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	тот же самый: 

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	Этот конструктор указывает, что при локальной отладке с помощью объединения и минификации ASP.NET должна выполняться обработка отдельных файлов сценариев, но с использованием указанного адреса CDN для доступа к соответствующему сценарию. Однако отметим две важных характеристики этого тщательно созданного URL-адреса CDN:
	
	-	Источником этого URL-адреса CDN служит адрес `http://<ваша_облачная_служба>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, который фактически является виртуальным каталогом пакета сценариев в вашей облачной службе.
	-	Так как используется конструктор CDN, тег сценария CDN для этого пакета больше не содержит автоматически созданную строку версии в обработанном URL-адресе. Необходимо вручную создавать уникальную строку версии каждый раз при изменении пакета скриптов, чтобы обеспечить промах кэша в Azure CDN. В то же время эта уникальная строка версии должна оставаться постоянной в течение всего срока существования развертывания, чтобы максимально увеличить попадания в кэше в Azure CDN после развертывания пакета.
	-	Строка запроса v=<W.X.Y.Z> берется из файла *Properties\AssemblyInfo.cs* в вашем проекте веб-роли. Можно создать рабочий процесс развертывания, включающий увеличение версии сборки при каждой публикации в Azure. Можно также просто изменить файл *Properties\AssemblyInfo.cs* в проекте, задав автоматическое увеличение строки версии при каждом построении с помощью подстановочного знака '*'. Например: 
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		Здесь будет работать любая другая стратегия упрощения создания уникальной строки на срок существования развертывания.

3. Повторно опубликуйте облачную службу и откройте домашнюю страницу.
 
4. Просмотрите код HTML для этой страницы. При каждой публикации изменений облачной службы вы должны видеть обработанный URL-адрес CDN с уникальной строкой версии. Например:   
	<pre class="prettyprint">
	...

    <link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/>

    <script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;></script>

	...

    <script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;></script>

    <script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;></script>

	...</pre>

5. Перейдите в режим отладки облачной службы в Visual Studio, нажав клавишу `F5`. 

6. Просмотрите код HTML для этой страницы. Вы по-прежнему будете видеть каждый индивидуально обработанный файл скрипта, так что можно получить последовательные возможности отладки в Visual Studio.  
	<pre class="prettyprint">
	...
	
	    <link href=&quot;/Content/bootstrap.css&quot; rel=&quot;stylesheet&quot;/>
	<link href=&quot;/Content/site.css&quot; rel=&quot;stylesheet&quot;/>
	
	    <script src=&quot;/Scripts/modernizr-2.6.2.js&quot;></script>
	
	...
	
	    <script src=&quot;/Scripts/jquery-1.10.2.js&quot;></script>
	
	    <script src=&quot;/Scripts/bootstrap.js&quot;></script>
	<script src=&quot;/Scripts/respond.js&quot;></script>
	
	...    
	</pre>

<a name="fallback"></a>
## Резервный механизм для URL-адресов CDN ##

Желательно, чтобы в случае сбоя конечной точки Azure CDN по какой-либо причине веб-страница могла использовать в качестве резервной возможности доступ к исходному веб-серверу для загрузки JavaScript или Bootstrap. Одно дело потерять изображения на веб-сайте из-за недоступности CDN, и совсем другое - потерять критически важные функциональные возможности страницы, обеспечиваемые сценариями и таблицами стилей.

Класс [Bundle](http://msdn.microsoft.com/ru-ru/library/system.web.optimization.bundle.aspx) содержит свойство с именем [CdnFallbackExpression](http://msdn.microsoft.com/ru-ru/library/system.web.optimization.bundle.cdnfallbackexpression.aspx), которое позволяет настраивать резервный механизм на случай сбоя CDN. Вот что нужно сделать, чтобы использовать это свойство:

1. В проекте веб-роли откройте файл *App_Start\BundleConfig.cs*, где в каждом [конструкторе Bundle](http://msdn.microsoft.com/ru-ru/library/jj646464.aspx) добавлялся URL-адрес CDN, и внесите следующие выделенные изменения, чтобы добавить резервный механизм в пакеты по умолчанию:  
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

	Когда выражение `CdnFallbackExpression` не равно NULL, сценарий внедряется в HTML, чтобы проверить успешность загрузки пакета, и в случае неуспешной загрузки обеспечивает доступ к пакету непосредственно на исходном веб-сервере. В этом свойстве необходимо устанавливать выражение JavaScript, проверяющее, загружен ли соответствующий пакет CDN должным образом. Выражения для проверки каждого пакета отличаются в соответствии с контентом. Для пакетов по умолчанию выше:
	
	-	`window.jquery` определяется в jquery-{версия}.js;
	-	`$.validator` определяется в jquery.validate.js;
	-	`window.Modernizr` определяется в modernizer-{версия}.js;
	-	`$.fn.modal` определяется в bootstrap.js.
	
	Возможно, вы заметили, что я не задал CdnFallbackExpression для пакета `~/Cointent/css`. Это объясняется тем, что сейчас есть [ошибка в System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104), которая вставляет тег `<script>` для резервной CSS вместо ожидаемого тега `<link>`.
	
	Однако существует хорошая [резервная форма пакета стилей](https://github.com/EmberConsultingGroup/StyleBundleFallback), предлагаемая [Ember Consulting Group](https://github.com/EmberConsultingGroup). 

2. Чтобы использовать этот обходной путь для CSS, создайте новый CS-файл *StyleBundleExtensions.cs* в папке *App_Start* своего проекта веб-роли и замените его содержимое [кодом от GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs). 

4. В *App_Start\StyleFundleExtensions.cs* измените имя пространства имен на имя веб-роли (например, **WebRole1**). 

3. Вернитесь в `App_Start\BundleConfig.cs` и измените последнюю инструкцию `bundles.Add` следующим выделенным кодом:  
	<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>

	Этот новый метод расширения использует ту же идею внедрения скрипта в HTML для поиска в модели DOM имени класса, имени правила и значения правила, соответствующих заданным в пакете CSS, и возвращения на исходный веб-сервер при отсутствии соответствия.

4. Снова опубликуйте облачную службу и откройте домашнюю страницу. 
5. Просмотрите код HTML для этой страницы. Вы должны найти внедренные скрипты, аналогичные показанным ниже.    
	<pre class="prettyprint">...
	
		<link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/>
	<mark><script&gt;(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i < len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf(&#39;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&#39;) !== -1) {
	                        var meta = document.createElement(&#39;meta&#39;);
	                        meta.className = &#39;sr-only&#39;;
	                        document.head.appendChild(meta);
	                        var value = window.getComputedStyle(meta).getPropertyValue(&#39;width&#39;);
	                        document.head.removeChild(meta);
	                        if (value !== &#39;1px&#39;) {
	                            document.write(&#39;<link href=&quot;/Content/css&quot; rel=&quot;stylesheet&quot; type=&quot;text/css&quot; />&#39;);
	                        }
	                    }
	                }
	                return true;
	            }())||document.write(&#39;<script src=&quot;/Content/css&quot;><\/script>&#39;);</script></mark>
	
	    <script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;></script>
	<mark><script>(window.Modernizr)||document.write(&#39;<script src=&quot;/bundles/modernizr&quot;><\/script>&#39;);</script></mark>
	
	...	
	
	    <script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;></script>
	<mark><script>(window.jquery)||document.write(&#39;<script src=&quot;/bundles/jquery&quot;><\/script>&#39;);</script></mark>
	
	    <script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;></script>
	<mark><script>($.fn.modal)||document.write(&#39;<script src=&quot;/bundles/bootstrap&quot;><\/script>&#39;);</script></mark>
	
	...
	</pre>

	Обратите внимание, что внедренный сценарий для пакета CSS по-прежнему содержит ошибочный фрагмент из свойства `CdnFallbackExpression` в следующей строке:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	Но поскольку первая часть выражения '||' будет всегда возвращать значение true (в строке прямо над этой), функция document.write() никогда не будет выполняться.

# Дополнительные сведения #
- [Общие сведения о сети доставки контента (CDN) Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Обслуживание содержимого из CDN Azure в вашем веб-приложении](http://azure.microsoft.com/ru-ru/Documentation/Articles/cdn-serve-content-from-cdn-in-your-web-application/)
- [Интеграция веб-сайта Azure с Azure CDN](http://azure.microsoft.com/ru-ru/documentation/articles/cdn-websites-with-cdn/)
- [Объединение и минификация ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Использование CDN для Azure](http://azure.microsoft.com/ru-ru/documentation/articles/cdn-how-to-use/)
