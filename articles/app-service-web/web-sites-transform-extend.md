---
title: "Дополнительные настройки и расширения веб-приложений службы приложений Azure"
description: "Используйте объявления преобразования XML-документов (XDT), чтобы преобразовать файл ApplicationHost.config в веб-приложении службы приложений Azure и добавить частные расширения. Это позволит выполнять дополнительные действия по администрированию."
author: cephalin
writer: cephalin
editor: mollybos
manager: wpickett
services: app-service
documentationcenter: 
ms.assetid: b441a286-ef38-4abc-b102-cdb249baf5bc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: abd55399ef780df945a2d643940a71425d085692


---
# <a name="azure-app-service-web-app-advanced-config-and-extensions"></a>Дополнительные настройки и расширения веб-приложений службы приложений Azure
С помощью объявлений [преобразования документов XML](http://msdn.microsoft.com/library/dd465326.aspx) (XDT) вы можете преобразовать файл [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) в веб-приложении в службе приложений Azure. Объявления XDT можно также использовать для добавления частных расширений, которые обеспечивают пользовательские действия администрирования веб-приложений. Статья содержит пример расширения для веб-приложения диспетчера PHP, позволяющего управлять параметрами PHP через веб-интерфейс.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="a-idtransformaadvanced-configuration-through-applicationhostconfig"></a><a id="transform"></a>Дополнительные настройки посредством ApplicationHost.config
Платформа службы приложений представляет собой гибкое решение для управления настройками веб-приложения. Хотя стандартный файл конфигурации IIS ApplicationHost.config недоступен для непосредственного редактирования в службе приложений, платформа поддерживает декларативную модель преобразования ApplicationHost.config, основанную на преобразовании XML-документа (XDT).

Чтобы использовать эту возможность преобразования, создайте файл ApplicationHost.xdt с содержимым XDT и поместите его в корневой каталог сайта (d:\home\site) в [Kudu Console](https://github.com/projectkudu/kudu/wiki/Kudu-console). Возможно, вам понадобится перезапустить веб-приложение, чтобы изменения вступили в силу.

В приведенном ниже примере с файлом applicationHost.xdt показано, как добавить новую пользовательскую переменную среды для веб-приложения, использующего PHP 5.4.

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


Файл журнала с состоянием преобразования и соответствующими сведениями находится в папке LogFiles\Transform корневого каталога FTP.

Дополнительные примеры см. на странице [https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples).

**Примечание.**<br />
Элементы из списка модулей в `system.webServer` нельзя удалить или переупорядочить, однако можно добавлять элементы в этот список.

## <a name="a-idextenda-extend-your-web-app"></a><a id="extend"></a> Расширения для веб-приложения
### <a name="a-idoverviewa-overview-of-private-web-app-extensions"></a><a id="overview"></a> Обзор частных расширений для веб-приложения
Служба приложений дает возможность использовать расширения веб-приложений для выполнения дополнительных действий по администрированию. Некоторые функции платформы службы приложений реализованы в виде предустановленных расширений. Предварительно установленные расширения платформы невозможно изменить, но вы можете создать и настроить частные расширения для своего веб-приложения. Эта функциональная возможность основана на объявлениях XDT. Вот основные шаги по созданию частного расширения веб-приложения.

1. **Содержимое**расширения веб-приложения: создайте приложение, поддерживаемое службой приложений Azure.
2. **Объявление**расширения веб-приложения: создайте файл ApplicationHost.xdt.
3. **Развертывание** расширения веб-приложения: разместите содержимое в папке SiteExtensions в каталоге `root`.

Внутренние ссылки веб-приложения должны указывать на путь, связанный с указанным в файле ApplicationHost.xdt путем приложения. При внесении любых изменений в файл ApplicationHost.xdt требуется перезапуск веб-приложения.

**Примечание**. Дополнительные сведения об этих ключевых элементах см. на странице [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

Ниже приводится подробный пример создания и активации частного расширения веб-приложения. Исходный код для следующего примера диспетчера PHP можно загрузить по адресу [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager).

### <a name="a-idsitesamplea-web-app-extension-example-php-manager"></a><a id="SiteSample"></a> Пример расширения сайта веб-приложения: диспетчер PHP
Диспетчер PHP является расширением веб-приложения, позволяющим администраторам сайтов без усилий просматривать и настраивать параметры PHP с помощью веб-интерфейса вместо непосредственного редактирования INI-файлов PHP. К общими файлам конфигурации PHP относится файл php.ini, расположенный в папке с программными файлами, и файл .user.ini, расположенный в корневой папке веб-приложения. Поскольку файл php.ini нельзя напрямую редактировать в службе приложений Azure, расширение диспетчера PHP использует для внесения изменений файл .user.ini.

#### <a name="a-idphpwebappa-the-php-manager-web-application"></a><a id="PHPwebapp"></a> Веб-приложение диспетчера PHP
Вот так выглядит домашняя страница развертывания диспетчера PHP.

![TransformSitePHPUI][TransformSitePHPUI]

Как видно, расширение веб-приложения похоже на обычное веб-приложение, при этом в его корневую папку помещен дополнительный файл ApplicationHost.xdt (подробные сведения о файле ApplicationHost.xdt доступны в следующем разделе этой статьи).

Расширение диспетчера PHP было создано с помощью шаблона веб-приложения Visual Studio ASP.NET 4 MVC. В следующем представлении из обозревателя решений показана структура расширения диспетчера PHP.

![TransformSiteSolEx][TransformSiteSolEx]

Для файлового ввода-вывода требуется единственная специальная логика — указать расположение каталога wwwroot в веб-приложении. Как показано в следующем примере кода, переменная среды HOME задает путь к корневому каталогу веб-приложения, а путь к каталогу wwwroot можно сформировать, добавив site\wwwroot.

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

При использовании расширений веб-приложения следует обратить внимание на обработку внутренних ссылок.  Если в HTML-файлах присутствуют ссылки, задающие абсолютные пути к внутренним ссылкам веб-приложения, необходимо убедиться, что в начало этих ссылок добавляется имя расширения в качестве корневого каталога. Это необходимо, поскольку теперь корневой каталог для расширения имеет вид "/`[your-extension-name]`/" вместо обычного "/", поэтому необходимо соответствующим образом обновить все внутренние ссылки. Например, предположим, что в коде присутствует следующая ссылка:

`"<a href="/Home/Settings">PHP Settings</a>"`

Если ссылка является частью расширения веб-приложения, ссылка должна выглядеть следующим образом:

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

Это требование можно обойти двумя способами: либо использовать в веб-приложении только относительные пути, либо в случае с приложениями ASP.NET использовать метод `@Html.ActionLink` , который автоматически создает правильные ссылки.

#### <a name="a-idxdta-the-applicationhostxdt-file"></a><a id="XDT"></a> Файл applicationHost.xdt
Код расширения веб-приложения находится в каталоге %HOME%\SiteExtensions\[имя_вашего_расширения]. Мы назовем его корневым каталогом расширения.  

Чтобы зарегистрировать расширение веб-приложения в файле applicationHost.config, необходимо поместить файл с именем ApplicationHost.xdt в корневой каталог расширения. Содержимое файла ApplicationHost.xdt должно выглядеть следующим образом.

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

Это аналогично добавлению пути нового приложения в список сайтов `system.applicationHost` на сайте SCM. Сайт SCM является конечной точкой администрирования сайта и имеет специальные учетные данные. У него есть следующий URL-адрес `https://[your-site-name].scm.azurewebsites.net`.  

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

### <a name="a-iddeploya-web-app-extension-deployment"></a><a id="deploy"></a> Развертывание расширения веб-приложения
Чтобы установить расширение веб-приложения, скопируйте все файлы веб-приложения через протокол FTP в папку веб-приложения `\SiteExtensions\[your-extension-name]`. Речь идет о веб-приложении, для которого необходимо установить расширение.  Не забудьте скопировать в эту папку и файл ApplicationHost.xdt. Перезапустите веб-приложение, чтобы включить расширение.

Расширение веб-приложения должно стать доступным по следующему адресу:

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

Обратите внимание, что этот URL-адрес похож на URL-адрес веб-приложения. Он отличается только использованием протокола HTTPS и наличием расширения SCM.

Вы можете отключить все частные расширения (кроме предустановленных) для веб-приложения. Это может потребоваться во время разработки или исследования. Для этого нужно добавить параметр приложения с ключом `WEBSITE_PRIVATE_EXTENSIONS` и значением `0`.

> [!NOTE]
> Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](https://azure.microsoft.com/try/app-service/), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.
> 
> 

## <a name="whats-changed"></a>Изменения
* Руководство по переходу от веб-сайтов к службе приложений см. в статье [Служба приложений Azure и существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png




<!--HONumber=Jan17_HO3-->


