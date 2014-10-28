<properties linkid="dev-net-transform-extend-site" urlDisplayName="Service Bus Topics" pageTitle="Transform and extend your site" metaKeywords="none" description="TBD" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="cephalin" writer="cephalin" editor="mollybos" manager="wpickett" title="Transform and extend your site"/>

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Преобразования и расширения веб-сайта

С помощью объявлений [преобразования документов XML][преобразования документов XML] (XDT) можно преобразовать файл [ApplicationHost.config][ApplicationHost.config] на веб-сайтах Windows Azure. Объявления XDT также можно использовать для добавления частных расширений сайта для реализации пользовательских действий администрирования веб-сайта. Данная статья содержит пример расширения сайта диспетчера PHP, позволяющего управлять параметрами PHP через веб-интерфейс.

<!-- MINI TOC -->

-   [Преобразование конфигурации веб-сайта в файле ApplicationHost.config][Преобразование конфигурации веб-сайта в файле ApplicationHost.config]
-   [Расширение возможностей сайта][Расширение возможностей сайта]

    -   [Обзор частных расширений сайта][Обзор частных расширений сайта]
    -   [Пример расширения сайта: диспетчер PHP][Пример расширения сайта: диспетчер PHP]

        -   [Веб-приложение диспетчера PHP][Веб-приложение диспетчера PHP]
        -   [Файл applicationHost.xdt][Файл applicationHost.xdt]
    -   [Развертывание расширения сайта][Развертывание расширения сайта]

## <span id="transform"></span></a>Преобразование конфигурации веб-сайта в файле ApplicationHost.config

Платформа веб-сайтов Azure позволяет гибко контролировать конфигурацию веб-сайтов. Хотя стандартный файл конфигурации IIS ApplicationHost.config недоступен для прямого редактирования на веб-сайтах Windows Azure, платформа поддерживает декларативную модель преобразования ApplicationHost.config, основанную на преобразовании документа XML (XDT).

Чтобы использовать эту возможность преобразования, создайте файл ApplicationHost.xdt с содержимым XDT и поместите его в корневой каталог сайта. Затем на странице **Настройка** портала Windows Azure установите для параметра приложения `WEBSITE_PRIVATE_EXTENSIONS` значение 1 (может потребоваться перезапустить сайт).

В следующем примере applicationHost.xdt показано, как добавить новую пользовательскую переменную среды для веб-сайта, использующего PHP 5.4.

    <?xml version="1.0"?> 
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform"> 
        <system.webServer> 
                <fastCgi>
                    <application>
                        <environmentVariables>
                                <environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />  
                        </environmentVariables>
                    </application>
                </fastCgi> 
        </system.webServer> 
    </configuration> 

Файл журнала с состоянием преобразования и соответствующими сведениями находится в папке LogFiles\\Transform корневого каталога FTP.

Дополнительные примеры см. по адресу [][]<https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions></a>.

**Примечание.**
Элементы из списка модулей в файле `system.webServer` нельзя удалить или переупорядочить, однако можно добавлять элементы в этот список.

## <span id="extend"></span></a>Расширение возможностей сайта

### <span id="overview"></span></a>Обзор частных расширений сайта

Веб-сайты Azure поддерживают расширения сайта, позволяющие расширить функциональные возможности действий администрирования сайта. Фактически некоторые возможности платформы веб-сайтов Azure и реализуются в виде предварительно установленных расширений сайта. Хотя предварительно установленные расширения платформы нельзя изменить, можно создавать и настраивать частные расширения для собственных веб-сайтов. Эта функциональная возможность основана на объявлениях XDT. Ниже перечислены основные шаги по созданию частного расширения сайта.

1.  **Содержание** расширения сайта: создание любого приложения, поддерживаемого веб-сайтами Azure.
2.  **Декларирование** расширения сайта: создайте файл ApplicationHost.xdt.
3.  **Развертывание** расширения сайта: разместите содержимое в папке SiteExtensions в разделе `root`.
4.  **Включение** расширения сайта: установите параметр приложения `WEBSITE_PRIVATE_EXTENSIONS` в значение 1.

Внутренние ссылки для веб-приложения должны указывать на путь, который задан относительно пути приложения, указанного в файле ApplicationHost.xdt. При внесении любых изменений в файл ApplicationHost.xdt требуется перезапуск веб-сайта.

**Примечание**. Дополнительные сведения об этих ключевых элементах см. по адресу [][]<https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions></a>. Приводится подробный пример, иллюстрирующий действия по созданию и активации частного расширения сайта. Исходный код для следующего примера диспетчера PHP можно загрузить по адресу [][1]<https://github.com/projectkudu/PHPManager></a>.

### <span id="SiteSample"></span></a>Пример расширения сайта: диспетчер PHP

Диспетчер PHP является расширением сайта, позволяющим администраторам сайтов легко просматривать и настраивать параметры PHP с помощью веб-интерфейса вместо непосредственного редактирования файлов INI PHP. Общими файлами конфигурации PHP являются php.ini, расположенный в папке "Program Files", и .user.ini, расположенный в корневой папке веб-сайта. Поскольку файл php.ini нельзя напрямую редактировать на платформе веб-сайтов Azure, расширение диспетчера PHP использует для внесения изменений файл .user.ini.

#### <span id="PHPwebapp"></span></a>Веб-приложение диспетчера PHP

Ниже приведена главная страница веб-сайта диспетчера PHP.

![TransformSitePHPUI][TransformSitePHPUI]

Как вы видите, расширение сайта похоже на обычное веб-приложение, однако в корневую папку веб-сайта помещен дополнительный файл ApplicationHost.xdt (более подробные сведения о файле ApplicationHost.xdt доступны в следующем разделе этой статьи).

Расширение диспетчера PHP было создано с помощью шаблона веб-приложения Visual Studio ASP.NET 4 MVC. Следующее представление из обозревателя решений показывает структуру расширения сайта менеджера PHP.

![TransformSiteSolEx][TransformSiteSolEx]

Чтобы указать, где на сайте находится каталог wwwroot, необходимо использовать специальную логическую схему для файлового ввода-вывода. Как показано в следующем примере кода, переменная среды "HOME" задает корневой путь сайта, а путь wwwroot можно сформировать путем добавления "site\\wwwroot":

    /// <summary>
    /// Gives the location of the .user.ini file, even if one doesn't exist yet
    /// </summary>
    private static string GetUserSettingsFilePath()
    {
            var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
            if (rootPath == null)
            {
                rootPath = System.IO.Path.GetTempPath(); // For testing purposes
            };
            var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
            return userSettingsFile;
    } 

После получения пути к каталогу можно использовать обычные операции файлового ввода-вывода для чтения и записи файлов.

При использовании расширений сайта следует обратить внимание на обработку внутренних ссылок. Если в файлах HTML присутствуют ссылки, задающие абсолютные пути к внутренним ссылкам на веб-сайте, необходимо убедиться, что в начало этих ссылок добавляется имя расширения в качестве корневого каталога сайта. Это необходимо, поскольку теперь корневой каталог сайта для расширения имеет вид "/`[your-extension-name]`/" вместо обычного "/", поэтому необходимо соответствующим образом обновить все внутренние ссылки. Например, предположим, что в коде присутствует следующая ссылка:

`"<a href="/Home/Settings">PHP Settings</a>"`

Когда ссылка является частью расширения сайта, она должна иметь следующую форму:

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

Это требование можно обойти, используя в рамках веб-сайта только относительные пути, а для веб-сайтов ASP.NET используется метод `@Html.ActionLink`, создающий соответствующие ссылки.

#### <span id="XDT"></span></a>Файл applicationHost.xdt

Код расширения сайта находится в каталоге %HOME%\\SiteExtensions\\[имя-вашего-расширения]. Мы назовем его корневым каталогом расширения.

Чтобы зарегистрировать расширение сайта в файле applicationHost.config, необходимо поместить файл с именем ApplicationHost.xdt в корневой каталог расширения. Содержимое файла ApplicationHost.xdt должно иметь следующий вид:

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.applicationHost>
                <sites>
                    <site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
                        <!-- NOTE: Add your extension name in the application paths below -->
                        <application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
                        <application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
                            <virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
                        </application>
                    </site>
                </sites>
        </system.applicationHost>
    </configuration>

Имя, задаваемое в качестве имени расширения, должно совпадать с именем корневого каталога расширения.

Это аналогично добавлению пути нового приложения в список сайтов `system.applicationHost` на сайте SCM. Сайт SCM является конечной точкой администрирования сайта и имеет специальные учетные данные. У него есть следующий URL `https://[your-site-name].scm.azurewebsites.net`.

    <system.applicationHost>
        ...
        <site name="~1[your-website]" id="1716402716">
                <bindings>
                    <binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
                    <binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
                </bindings>
                <traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
                <detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
                <logFile logSiteId="false" />
                <application path="/" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
                </application>
                <!-- Note the custom changes that go here -->
                <application path="/[your-extension-name]" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
                </application>
            </site>
    </sites>
      ...
    </system.applicationHost>

### <span id="deploy"></span></a>Развертывание расширения сайта

Чтобы установить расширение сайта, можно использовать FTP для копирования всех файлов веб-приложения в папку `\SiteExtensions\[your-extension-name]` на сайте, где требуется установить расширение. Не забудьте скопировать в эту папку и файл ApplicationHost.xdt.

Теперь на портале веб-сайтов Windows Azure перейдите на вкладку **Настройка** для веб-сайта, имеющего ваше расширение. В разделе **Параметры приложения** добавьте ключ `WEBSITE_PRIVATE_EXTENSIONS` и присвойте ему значение `1`.

![TransformSiteappSettings][TransformSiteappSettings]

Наконец, перезапустите веб-сайт на портале Windows Azure для активации расширения.

![TransformSiteRestart][TransformSiteRestart]

Вы сможете увидеть свое расширение сайта по следующему адресу:

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

Обратите внимание, что этот URL-адрес похож на URL-адрес для сайта и отличается только использованием протокола HTTPS и наличием ".scm".

<!-- IMAGES -->

  [преобразования документов XML]: http://msdn.microsoft.com/ru-ru/library/dd465326.aspx
  [ApplicationHost.config]: http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig
  [Преобразование конфигурации веб-сайта в файле ApplicationHost.config]: #transform
  [Расширение возможностей сайта]: #extend
  [Обзор частных расширений сайта]: #overview
  [Пример расширения сайта: диспетчер PHP]: #SiteSample
  [Веб-приложение диспетчера PHP]: #PHPwebapp
  [Файл applicationHost.xdt]: #XDT
  [Развертывание расширения сайта]: #deploy
  []: https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions
  [1]: https://github.com/projectkudu/PHPManager
  [TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
  [TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
  [TransformSiteappSettings]: ./media/web-sites-transform-extend/TransformSiteappSettings.png
  [TransformSiteRestart]: ./media/web-sites-transform-extend/TransformSiteRestart.png
