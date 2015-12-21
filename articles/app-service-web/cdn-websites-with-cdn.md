<properties 
	pageTitle="Использование Azure CDN в службе приложений Azure" 
	description="В этом учебнике объясняется, как развернуть в службе приложений Azure веб-приложение, которое обслуживает содержимое из интегрированной конечной точки CDN Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="cephalin"/>


# Использование Azure CDN в службе приложений Azure

[Служба приложений](http://go.microsoft.com/fwlink/?LinkId=529714) может интегрироваться с [Azure CDN](/services/cdn/), добавляя возможности глобального масштабирования, присущие [веб-приложениям службы приложений](http://go.microsoft.com/fwlink/?LinkId=529714). Так ваше содержимое веб-приложения обслуживается глобально на узлах серверов рядом с клиентами (обновленный список всех текущих расположений узлов можно найти [здесь](http://msdn.microsoft.com/library/azure/gg680302.aspx)). В таких сценариях, как предоставление статичных изображений, данная интеграция значительно повышает производительность веб-приложений службы приложений Azure и существенно улучшает взаимодействие с пользователями вашего веб-приложения по всему миру.

Интеграция веб-приложений с Azure CDN предоставляет следующие преимущества.

- Интеграция развертывания содержимого (изображений, скриптов и таблиц стилей) в процесс [непрерывного развертывания](web-sites-publish-source-control.md) веб-приложения.
- Облегчение обновления пакетов NuGet в веб-приложении службы приложений Azure, например версий jQuery или Bootstrap. 
- Управление веб-приложениями и содержимым, обслуживаемым CDN, из одного интерфейса Visual Studio.
- Интеграция объединения и минификации ASP.NET с Azure CDN.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Что будет строиться ##

Вы будете развертывать веб-приложение в службе приложений Azure с помощью шаблона MVC ASP.NET по умолчанию в Visual Studio, добавлять код для обслуживания содержимого из интегрированной Azure CDN (например, изображения, результатов действий контроллера, файлов JavaScript и CSS по умолчанию), а также создавать код для настройки резервного механизма обслуживаемых пакетов на случай, когда CDN перейдет в автономный режим.

## Необходимые условия ##

Для работы с этим учебником необходимы следующие компоненты.

-	Активная [учетная запись Microsoft Azure](/account/).
-	Visual Studio 2015 с [пакетом Azure SDK для .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) Если вы используете Visual Studio, действия могут отличаться.

> [AZURE.NOTE]Для выполнения приведенных в этом учебнике инструкций необходима учетная запись Azure. Вы можете [создать ее бесплатно](/pricing/free-trial/?WT.mc_id=A261C142F): при этом вы получаете кредиты, с помощью которых можно проверить в действии платные службы Azure, и даже после их израсходования сохраняете учетную запись и возможность использовать бесплатные службы Azure, такие как веб-сайты. Вы можете [активировать преимущества подписчика Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): ваша подписка на Visual Studio каждый месяц приносит вам кредиты, которые можно использовать для оплаты служб Azure.
>
> Если вы хотите приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

## Развертывание веб-приложения в Azure с интегрированной конечной точкой CDN ##

В этом разделе вы будете развертывать шаблон приложения MVC ASP.NET по умолчанию в Visual Studio 2013 в службе приложений Azure, а интегрируете его с новой конечной точкой CDN. Выполните приведенные далее инструкции.

1. В Visual Studio 2015 создайте новое веб-приложение ASP.NET, последовательно выбрав в строке меню **Файл -> Создать -> Проект > Интернет -> Веб-приложение ASP.NET**. Назначьте ей имя и нажмите кнопку **ОК**.

	![](media/cdn-websites-with-cdn/1-new-project.png)

3. Выберите **MVC** и нажмите кнопку **ОК**.

	![](media/cdn-websites-with-cdn/2-webapp-template.png)

4. Если вы еще не выполнили вход в учетную запись Azure, щелкните значок учетной записи в правом верхнем углу и следуйте инструкциям в диалоговом окне для входа в учетную запись Azure. После этого настройте свое приложение, как показано ниже, а затем щелкните **Создать**, чтобы создать новый план службы приложений для вашего приложения.

	![](media/cdn-websites-with-cdn/3-configure-webapp.png)

5. Настройте новый план службы приложений в диалоговом окне, как показано ниже, и нажмите кнопку **ОК**.

	![](media/cdn-websites-with-cdn/4-app-service-plan.png)

8. Нажмите кнопку **Создать**, чтобы создать веб-приложение.

	![](media/cdn-websites-with-cdn/5-create-website.png)

9. Создав приложение ASP.NET, опубликуйте его в Azure в области действий службы приложений Azure, щелкнув **Опубликовать `<app name>` на этом сайте**. Для завершения процесса нажмите **Опубликовать**.

	![](media/cdn-websites-with-cdn/6-publish-website.png)

	После завершения публикации вы увидите опубликованное веб-приложение в браузере.

1. Чтобы создать конечную точку CDN, войдите на [классический портал Azure](https://manage.windowsazure.com).
2. Последовательно выберите пункты **Создать** > **Службы приложений** > **CDN** > **Быстрое создание**. Выберите **http://*&lt;sitename>*.azurewebsites.net/** и нажмите кнопку **Создать**.

	![](media/cdn-websites-with-cdn/7-create-cdn.png)

	> [AZURE.NOTE]После создания конечной точки CDN классический портал отображает ее URL-адрес и исходный домен, с которым она интегрирована. Однако полное распространение конфигурации новой конечной точки CDN во все расположения узлов CDN может занять некоторое время.

3. На классическом портале откройте вкладку **CDN** и щелкните имя только что созданной конечной точки CDN.

	![](media/cdn-websites-with-cdn/8-select-cdn.png)

3. Нажмите **Включить строку запроса**, чтобы включать строки запроса в кэш CDN. После этого те же самые ссылки, доступ к которым осуществляется с помощью разных строк запроса, будут кэшироваться как отдельные записи.

	![](media/cdn-websites-with-cdn/9-enable-query-string.png)

	>[AZURE.NOTE]Хотя включение поддержки строки запроса в этом разделе учебника не обязательно, для удобства это можно сделать как можно раньше, так как распространение любого изменения во все узлы CDN занимает некоторое время, и нежелательно, чтобы содержание без включенных строк запроса засорял кэш CDN (обновление контента CDN будет рассматриваться позднее).

2. Теперь щелкните адрес конечной точки CDN. Если конечная точка готова, вы увидите свое веб-приложение. Если возникает ошибка **HTTP 404**, конечная точка CDN не готова. Вам нужно подождать около часа, чтобы конфигурация сети CDN распространилась на все пограничные узлы.

	![](media/cdn-websites-with-cdn/11-access-success.png)

1. Далее попытайтесь получить доступ к файлу **~/Content/bootstrap.css** в своем проекте ASP.NET. В окне браузера перейдите по адресу **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css**. В моей настройке это следующий URL-адрес:

		http://az673227.vo.msecnd.net/Content/bootstrap.css

	Он соответствует следующему исходному URL-адресу в конечной точке CDN:

		http://cdnwebapp.azurewebsites.net/Content/bootstrap.css

	При переходе на адрес **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css** вам будет предложено загрузить файл bootstrap.css, происходящий из вашего веб-приложения в Azure.

	![](media/cdn-websites-with-cdn/12-file-access.png)

Аналогичным образом можно получать доступ к любому общедоступному URL-адресу в **http://*&lt;serviceName>*.cloudapp.net/** прямо из конечной точки CDN. Например:

-	к JS-файлу в пути /Script;
-	к любому файлу контента в пути /Content;
-	к любому контроллеру или действию; 
-	если в конечной точке CDN включена строка запроса, то к любому URL-адресу со строкой запроса.
-	ко всему веб-приложению Azure, если все содержимое общедоступно.

Обратите внимание, что не всегда рекомендуется (или вообще не рекомендуется) обслуживать все веб-приложение Azure через Azure CDN. Далее приводятся некоторые предупреждения.

-	Для использования этого подхода весь сайт должен быть общедоступным, поскольку Azure CDN не может обслуживать конфиденциальный контент.
-	Если по какой-либо причине, будь то плановое обслуживание или ошибка пользователя, конечная точка CDN становится автономной, все веб-приложение становится автономным, если только нельзя перенаправить клиентов на исходный URL-адрес **http://*&lt;sitename>*.azurewebsites.net/**. 
-	Даже с настраиваемыми параметрами управления кэшем (см. раздел [Настройка параметров кэширования для статических файлов в веб-приложении](#configure-caching-options-for-static-files-in-your-azure-web-app)) конечная точка CDN не улучшает производительность высокодинамичного содержимого. При попытке загрузить домашнюю страницу из конечной точки CDN, как показано выше, обратите внимание, что первая загрузка домашней страницы по умолчанию займет по крайней мере 5 секунд, хотя это довольно простая страница. Представьте, как бы выглядело взаимодействие с клиентом, если бы эта страница содержала динамический контент, который должен обновляться каждую минуту. Обслуживание динамического контента из конечной точки CDN требует, чтобы срок годности кэша был коротким,что приводит к частой потере кэша в конечной точке CDN. Это вредит производительности веб-приложения Azure, и смысл CDN теряется.

Альтернатива заключается в том, чтобы определить, какое содержимое должно обслуживаться из Azure CDN на индивидуальной основе в веб-приложении Azure. В отношении этого вы уже видели, как получать доступ к отдельным файлам контента из конечной точки CDN. Я покажу, как обслуживать определенное действие контроллера посредством конечной точки CDN, в разделе [Обслуживание контента из действий контроллера посредством Azure CDN](#serve-content-from-controller-actions-through-azure-cdn).

## Настройка параметров кэширования для статических файлов в веб-приложении Azure ##

С помощью интеграции Azure CDN с веб-приложением Azure вы можете указать, как статическое содержимое должно кэшироваться в конечной точке CDN. Для этого откройте файл *Web.config* из проекта ASP.NET (например, **cdnwebapp**) и добавьте элемент `<staticContent>` в `<system.webServer>`. Следующий XML настраивает истечение срока годности кэша через 3 дня.

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

После этого все статические файлы в веб-приложении Azure будут соблюдать то же правило в кэше CDN. Для более детального управления параметрами кэша добавьте файл *Web.config* в папку и добавьте в этом месте свои параметры. Например, добавьте файл *Web.config* в папку *\\Content* и замените содержимое следующим XML:

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

В следующем разделе я также покажу, как можно настраивать параметры кэша для результатов действий контроллера в кэше CDN.

## Обслуживание контента из действий контроллера посредством Azure CDN ##

Если веб-приложения интегрируются с Azure CDN, то становится относительно просто обслуживать содержимое действий контроллера через Azure CDN. Опять же, если вы решили обслуживать все веб-приложение Azure через CDN, вообще не нужно этого делать, так как все действия контроллера уже доступны через CDN. Однако по причинам, которые указывались в разделе [Развертывание веб-приложения Azure с использованием интегрированной конечной точки CDN](#deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint), вы можете отказаться от этого и выбрать действие контроллера, которое нужно обслуживать из сети Azure CDN. [Мартин Баллиау (Maarten Balliauw)](https://twitter.com/maartenballiauw) показывает, как это сделать, на примере интересного контроллера MemeGenerator в разделе [Уменьшение задержки в Интернете с помощью Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). Я просто воспроизведу это здесь.

Предположим, вы хотите создать в веб-приложении мемы на основе образа молодого Чака Норриса (фото [Алана Лайта (Alan Light)](http://www.flickr.com/photos/alan-light/218493788/)):

![](media/cdn-websites-with-cdn/cdn-5-memegenerator.PNG)

Имеется простое действие `Index`, которое позволяет клиентам задавать гиперболы в образе, а затем создает мем, как только клиент отправит их в действие. Поскольку это Чак Норрис, можно ожидать, что страница станет очень популярной по всему миру. Это хороший пример обслуживания полудинамического контента с помощью Azure CDN.

Чтобы настроить это действие контроллера, выполните следующие действия.

1. В папке *\\Controllers* создайте новый CS-файл с именем *MemeGeneratorController.cs* и замените содержимое следующим кодом. Замените путь к файлу на `~/Content/chuck.bmp`, а имя CDN — на `yourCDNName`.


        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;

        namespace cdnwebapp.Controllers
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
                return Redirect(string.Format("http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
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

	![](media/cdn-websites-with-cdn/cdn-6-addview.PNG)

3.  Примите параметры, показанные ниже, и нажмите кнопку **Добавить**.

	![](media/cdn-websites-with-cdn/cdn-7-configureview.PNG)

4. Откройте новый файл *Views\\MemeGenerator\\Index.cshtml* и замените его содержимое следующим простым HTML для отправки гипербол:

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Снова опубликуйте веб-приложение и перейдите в браузере по адресу **http://*&lt;serviceName>*.cloudapp.net/MemeGenerator/Index**.

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
        return Redirect(string.Format("http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
      }
    }

Если подключен локальный отладчик, то вы получите обычный интерфейс отладки с локальным перенаправлением. Если отладчик работает в веб-приложении Azure, то перенаправление будет выполняться сюда:

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Он соответствует следующему исходному URL-адресу в конечной точке CDN:

	http://<yourSiteName>.azurewebsites.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

После правила переопределения URL-адреса, применявшегося ранее, фактический файл, который кэшируется в конечной точке CDN, имеет следующий адрес:

	http://<yourSiteName>.azurewebsites.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Затем можно с помощью атрибута `OutputCacheAttribute` в методе `Generate` указать, как должен кэшироваться результат действия, которое будет выполнять Azure CDN. В следующем коде задается срок годности кэша в 1 час (3 600 секунд).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Аналогично вы можете обслуживать содержимое из любого действия контроллера в веб-приложении Azure через Azure CDN, используя нужный параметр кэширования.

В следующем разделе будет показано, как обслуживать объединенные в пакет и минифицированные скрипты и CSS посредством Azure CDN.

## Интеграция объединения и минификации ASP.NET с Azure CDN. ##

Скрипты и таблицы стилей CSS изменяются нечасто и являются основными кандидатами для кэша Azure CDN. Обслуживание всего веб-приложения посредством Azure CDN представляет простейший способ интеграции объединения и минификации с Azure CDN. А так как вы можете отказаться от такого подхода по причинам, описанным в разделе [Интеграция конечной точки Azure CDN с вашим веб-приложением Azure и обслуживание статического содержимого на веб-страницах из Azure CDN](#deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint), я покажу, как это сделать, сохранив требуемое взаимодействие разработчика для объединения и минификации ASP.NET, в том числе:

-	Отличное взаимодействие в режиме отладки.
-	Упрощенное развертывание.
-	Немедленные обновления клиентов при обновлении версий скриптов или CSS.
-	Резервный механизм на случай сбоя конечной точки CDN.
-	Минимальное изменение кода.

В проекте ASP.NET, созданном при изучении раздела [Интеграция конечной точки Azure CDN с веб-сайтом Azure и обслуживание статического содержимого на веб-страницах из Azure CDN](#deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint), откройте *App\_Start\\BundleConfig.cs* и взгляните на вызовы метода `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

Первая инструкция `bundles.Add()` добавляет пакет скриптов в виртуальный каталог `~/bundles/jquery`. Затем откройте файл *Views\\Shared\_Layout.cshtml*, чтобы просмотреть, как обрабатывается тег пакета скриптов. Вы должны найти следующую строку кода Razor:

    @Scripts.Render("~/bundles/jquery")

При запуске этого кода Razor в веб-приложении Azure он будет обрабатывать тег `<script>` для пакета скриптов следующим образом:

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
          var cdnUrl = "http://<yourCDNName>.vo.msecnd.net/{0}?v=" + version;

          bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                "~/Scripts/jquery-{version}.js"));

          bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                "~/Scripts/jquery.validate*"));

          // Use the development version of Modernizr to develop with and learn from. Then, when you're
          // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
          bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizr")).Include(
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

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	Этот конструктор указывает, что при локальной отладке с помощью объединения и минификации ASP.NET должна выполняться обработка отдельных файлов скриптов, но с использованием указанного адреса CDN для доступа к соответствующему скрипту. Однако отметим две важных характеристики этого тщательно созданного URL-адреса CDN.
	
	- Источником этого URL-адреса CDN служит адрес `http://<yourSiteName>.azurewebsites.net/bundles/jquery?v=<W.X.Y.Z>`, который фактически является виртуальным каталогом пакета скриптов в веб-приложении.
	- Поскольку используется конструктор CDN, тег скрипта CDN для этого пакета больше не содержит автоматически созданную строку версии в обработанном URL-адресе. Необходимо вручную создавать уникальную строку версии каждый раз при изменении пакета скриптов, чтобы обеспечить промах кэша в Azure CDN. В то же время эта уникальная строка версии должна оставаться постоянной в течение всего срока существования развертывания для максимального увеличения попаданий в кэш в Azure CDN после развертывания пакета.
	- Строка запроса v=<W.X.Y.Z> берется из файла *Properties\\AssemblyInfo.cs* в вашем проекте ASP.NET. Можно создать рабочий процесс развертывания, включающий увеличение версии сборки при каждой публикации в Azure. Можно также просто изменить файл *Properties\\AssemblyInfo.cs* в проекте, задав автоматическое увеличение строки версии при каждом построении с помощью подстановочного знака '*'. Например:

			[assembly: AssemblyVersion("1.0.0.*")]
	
	Здесь будет работать любая другая стратегия упрощения создания уникальной строки на срок существования развертывания.

3. Повторно опубликуйте приложение ASP.NET и откройте домашнюю страницу.
 
4. Просмотрите код HTML для этой страницы. При каждой публикации изменений веб-приложения Azure вы должны видеть обработанный URL-адрес CDN с уникальной строкой версии. Например:
	
        ...
        <link href="http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>
        <script src="http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449"></script>
        ...
        <script src="http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25449"></script>
        <script src="http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449"></script>
        ...

5. Перейдите в режим отладки приложения ASP.NET в Visual Studio, нажав клавишу `F5`.

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

## Резервный механизм для URL-адресов CDN ##

Желательно, чтобы в случае сбоя конечной точки Azure CDN по какой-либо причине веб-страница могла использовать в качестве резервной возможности доступ к исходному веб-серверу для загрузки JavaScript или Bootstrap. Одно дело — потерять изображения в веб-приложении из-за недоступности CDN, и совсем другое — потерять критически важные функциональные возможности страницы, обеспечиваемые скриптами и таблицами стилей.

Класс [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) содержит свойство с именем [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx), которое позволяет настраивать резервный механизм на случай сбоя CDN. Для использования этого свойства выполните приведенные ниже действия.

1. В проекте ASP.NET откройте файл *App\_Start\\BundleConfig.cs*, где в каждом [конструкторе Bundle](http://msdn.microsoft.com/library/jj646464.aspx) вы добавили URL-адрес CDN, и добавьте код `CdnFallbackExpression` в четырех местах, чтобы добавить резервный механизм в пакеты по умолчанию.  
	
        public static void RegisterBundles(BundleCollection bundles)
        {
          var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
            .GetName().Version.ToString();
          var cdnUrl = "http://cdnurl.vo.msecnd.net/.../{0}?" + version;
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
	
	- `window.jquery` задается в jquery-{version}.js
	- `$.validator` задается в jquery.validate.js;
	- `window.Modernizr` задается в modernizer-{version}.js;
	- `$.fn.modal` задается в bootstrap.js.
	
	Возможно, вы заметили, что я не установил CdnFallbackExpression для пакета `~/Cointent/css`. Это объясняется тем, что в настоящее время имеется [ошибка в System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104), которая вставляет тег `<script>` для резервной CSS вместо ожидаемого тега `<link>`.
	
	Однако существует хорошая [резервная форма пакета стилей](https://github.com/EmberConsultingGroup/StyleBundleFallback), предлагаемая [Ember Consulting Group](https://github.com/EmberConsultingGroup).

2. Чтобы использовать этот обходной путь для CSS, создайте в папке *App\_Start* своего проекта ASP.NET новый CS-файл с именем *StyleBundleExtensions.cs* и замените его содержимое [кодом от GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).

4. В файле *App\_Start\\StyleFundleExtensions.cs* переименуйте пространство имен для своего приложения ASP.NET (например, **cdnwebapp**).

3. Вернитесь к файлу `App_Start\BundleConfig.cs` и замените последнюю инструкцию `bundles.Add` следующим кодом:

        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
          .IncludeFallback("~/Content/css", "sr-only", "width", "1px")
          .Include(
            "~/Content/bootstrap.css",
            "~/Content/site.css"));

	Этот новый метод расширения использует ту же идею внедрения скрипта в HTML для поиска в модели DOM имени класса, имени правила и значения правила, соответствующих заданным в пакете CSS, и возвращения на исходный веб-сервер при отсутствии соответствия.

4. Повторите публикацию в свое веб-приложение Azure и откройте домашнюю страницу.
5. Просмотрите код HTML для этой страницы. Вы должны найти внедренные скрипты, аналогичные показанным ниже.    
	
	``` ... <link href="http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
<script>(function() { var loadFallback, len = document.styleSheets.length; for (var i = 0; i < len; i++) { var sheet = document.styleSheets[i]; if (sheet.href.indexOf('http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474') !== -1) { var meta = document.createElement('meta'); meta.className = 'sr-only'; document.head.appendChild(meta); var value = window.getComputedStyle(meta).getPropertyValue('width'); document.head.removeChild(meta); if (value !== '1px') { document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />'); } } } return true; }())||document.write('<script src="/Content/css"><\\/script>');</script>

	<script src="http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474"></script>
 	<script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
	... 
	<script src="http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25474"></script>
	<script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>

 	<script src="http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474"></script>
 	<script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
	...
	```

	Note that injected script for the CSS bundle still contains the errant remnant from the `CdnFallbackExpression` property in the line:

		}())||document.write('<script src="/Content/css"><\/script>');</script>

	But since the first part of the || expression will always return true (in the line directly above that), the document.write() function will never run.

6. Чтобы проверить, правильно ли работает сценарий резервного действия, вернитесь в панель мониторинга своей конечной точки CDN и щелкните **Отключить конечную точку**.

	![](media/cdn-websites-with-cdn/13-test-fallback.png)

7. Обновите окно браузера для веб-приложения Azure. Теперь вы увидите, что все сценарии и таблицы стилей загружены правильно.

## Дополнительные сведения 
- [Общие сведения о сети доставки контента (CDN) Azure](../cdn-overview.md)
- [Обслуживание содержимого из CDN Azure в вашем веб-приложении](../cdn-serve-content-from-cdn-in-your-web-application.md)
- [Интеграция облачной службы с Azure CDN](../cdn-cloud-service-with-cdn.md)
- [Объединение и минификация ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Использование CDN для Azure](../cdn-how-to-use-cdn.md)

## Изменения
* Указания по изменениям при переходе от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Руководство по смене старого портала на новый портал см. в разделе [Справочник по навигации на предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715).
 

<!---HONumber=AcomDC_1210_2015-->