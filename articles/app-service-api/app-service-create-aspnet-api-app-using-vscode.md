<properties
   pageTitle="Создание приложения API ASP.NET 5 в Visual Studio Code"
   description="В этом учебнике показано, как создать приложение API ASP.NET 5 с помощью Visual Studio Code."
   services="app-service\api"
   documentationCenter=".net"
   authors="erikre"
   manager="wpickett"
   editor="jimbe"/>

<tags
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="erikre"/>

# Создание приложения API ASP.NET 5 в Visual Studio Code

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## Обзор

В этом учебнике показано, как создать приложение API ASP.NET 5 с помощью [Visual Studio Code](http://code.visualstudio.com//Docs/whyvscode). ASP.NET 5 является существенно переработанной версией ASP.NET. ASP.NET 5 — это новая кросс-платформенная платформа с открытым кодом, предназначенная для создания современных облачных веб-приложений с использованием .NET. Дополнительную информацию см. в статье [Введение в ASP.NET 5](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html). Информацию о приложениях API см. в разделе [Что такое приложения API?](app-service-api-apps-why-best-platform.md).

> [AZURE.NOTE]Для работы с этим учебником необходимо использовать учетную запись Microsoft Azure. Если у вас нет учетной записи, можно [зарегистрироваться для использования бесплатной пробной версии](/pricing/free-trial/) или [активировать преимущества для подписчиков MSDN](/pricing/member-offers/msdn-benefits-details/). Можно также бесплатно попробовать [образцы приложений службы приложений](http://tryappservice.azure.com).

## Предварительные требования  

* Установите и настройте [Visual Studio Code](http://code.visualstudio.com/Docs/setup).
* Установите [Node.js](http://nodejs.org/download/).<br\>
	[Node](http://nodejs.org/) — это платформа для создания быстрых масштабируемых серверных приложений с помощью JavaScript. Node — это среда выполнения (Node), а [npm](http://www.npmjs.com/) — диспетчер пакетов для модулей Node. Для формирования шаблонов приложения API ASP.NET 5 в этом учебнике будет использоваться npm.

## Установка ASP.NET 5 и DNX
ASP.NET 5 и DNX представляют собой простой стек .NET для сборки современных облачных и веб-приложений, работающих в OS X, Linux и Windows. Он был создан с нуля, чтобы предоставить платформу для разработки, оптимизированную для приложений, которые можно разворачивать в облачной или локальной среде. Он состоит из модульных компонентов с минимальными служебными данными, что позволяет сохранить гибкость при построении решений.

> [AZURE.NOTE]ASP.NET 5 и DNX (среда выполнения .NET) в OS X и Linux находятся на стадии ранней бета-версии или предварительной версии.

Этот учебник позволяет приступить к сборке приложений с помощью последних версий ASP.NET 5 и DNX для разработчиков. Если вам нужна более стабильная, выпущенная и испытанная версия, перейдите по ссылке [http://www.asp.net/vnext](http://www.asp.net/vnext). Приведенные ниже указания относятся только к Windows. Более подробные указания по установке для OS X, Linux и Windows см. в разделе [Установка ASP.NET 5 и DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

1. Чтобы установить диспетчер версий .NET (DNVM) в Windows, выполните следующую команду в окне командной строки:

	<pre class="prettyprint">
	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"
	</pre> 
	Она скачает сценарий DNVM и поместит его в ваш профиль пользователя.

2. Возможно, после ввода приведенной выше команды необходимо будет выйти из системы, чтобы изменение переменной среды PATH вступило в силу.
3. Проверьте расположение DNVM, выполнив в командной строке следующее: 

	<pre class="prettyprint">
where dnvm
	</pre>
	В окне командной строки будет отображаться путь следующего вида:

	![dnvm location](./media/app-service-create-aspnet-api-app-using-vscode/00-where-dnvm.png)

4. Теперь, установив DNVM, необходимо с его помощью скачать DNX для запуска приложений. Выполните следующую команду в окне командной строки:

	<pre class="prettyprint">
dnvm upgrade
</pre>

5. Проверьте установленный DNVM и просмотрите активную среду выполнения, введя следующее в окне командной строки:

	<pre class="prettyprint">
dnvm list
	</pre>
	В окне командной строки будет показана подробная информация об активной среде выполнения:

	![dnvm location](./media/app-service-create-aspnet-api-app-using-vscode/00b-dnvm-list.png)

Более подробные указания по установке для OS X, Linux и Windows см. в разделе [Установка ASP.NET 5 и DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

## Создание приложения API 

В этом разделе показано, как сформировать шаблон нового приложения API ASP.NET. Диспетчер пакетов Node (npm) будет использоваться для установки [Yeoman](http://yeoman.io/), [Grunt](http://gruntjs.com/) и [Bower](http://bower.io/).

1. После установки Visual Studio Code и Node.js откройте командную строку с правами администратора и перейдите в папку, в которой хотите расположить все проекты ASP.NET, используемые VSCode.
2. Введите следующую команду в окне командной строки, чтобы установить Yeoman и вспомогательные инструменты:

	<pre class="prettyprint">
npm install -g yo grunt-cli generator-aspnet bower
</pre>

3. Введите следующую команду в окне командной строки, чтобы создать папку проекта и сформировать шаблон приложения:

	<pre class="prettyprint">
yo aspnet
</pre>

4. Следуйте указаниям генератора, прокрутив список и выбрав тип **Веб-приложение API**.

	![Генератор Yoman-ASP.NET 5](./media/app-service-create-aspnet-api-app-using-vscode/01-yo-aspnet.png)

5. Задайте имя нового приложения API ASP.NET — **ContactsList**. Это имя будет использоваться в коде, приведенном далее в этом учебнике. <br>
	Yoman создаст новую папку с именем **ContactsList** и файлы, необходимые для нового приложения.
6. Откройте **Visual Studio Code**.<br>
	VSCode можно открыть из командного окна, введя **code**.
7. В меню **Файл** выберите **Открыть папку** и выберите папку, где находится приложение API ASP.NET.

	![Диалоговое окно "Выбор папки"](./media/app-service-create-aspnet-api-app-using-vscode/02-open-folder.png)

	VSCode загрузит проект и отобразит его в окне **Произвольное тестирование**.

	![Отображение проекта Contact в VSCode](./media/app-service-create-aspnet-api-app-using-vscode/03-vscode-contacts-project.png)

8. В **VSCode** в меню **Вид** выберите **Палитра команд**.
9. В **палитре команд** введите следующие команды:

	<pre class="prettyprint">
dnx:dnu restore - (ContactsList)
	</pre>
	Начав ввод, вы увидите полную командную строку в списке.

	![Команда Restore](./media/app-service-create-aspnet-api-app-using-vscode/04-dnu-restore.png)

	Команда Restore устанавливает пакеты NuGet, необходимые для запуска приложения. По завершении в командном окно отобразится сообщение **Восстановление завершено**.

## Изменение приложения API

Теперь изменим приложение **ContactsList**, добавив классы **Contact** и **ContactsController**.

1. Наведите курсор на имя проекта **ContactsList** и добавьте новую папку с именем *Models* с помощью значка новой папки.

	![Новая папка Models](./media/app-service-create-aspnet-api-app-using-vscode/07-new-folder.png)

2. Щелкните правой кнопкой мыши папку **Models**, чтобы добавить новый файл класса *Contact.cs* со следующим кодом:

	<pre class="prettyprint">
namespace ContactsList.Models
{
    public class Contact
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string EmailAddress { get; set; }
    }
}
</pre>

3. Щелкните правой кнопкой мыши папку **Controllers** и добавьте файл *ContactsController.cs* следующего вида:

	<pre class="prettyprint">
using System.Collections.Generic;
using Microsoft.AspNet.Mvc;
using ContactsList.Models;

namespace ContactsList.Controllers
{
    [Route("api/[controller]")]
    public class ContactsController : Controller
    {
        // GET: api/Contacts
        [HttpGet]
	        public IEnumerable&lt;Contact&gt; Get()
        {
            return new Contact[]{
                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
            };
        }
    }
}
</pre>

4. Убедитесь, что все файлы сохранены, выбрав **Файл** > **Сохранить все**.
5. В **палитре команд** введите следующую команду, чтобы запустить приложение локально:

	<pre class="prettyprint">
dnx: kestrel - (ContactsList, Microsoft.AspNet.Hosting --server Kestrel --server.urls http://localhost:5001
	</pre>
	В окне командной строки отобразится состояние *Запущено*. Если в командном окне не отображается состояние *Запущено*, проверьте нижний левый угол VSCode н наличие ошибок в проекте.

5. Откройте браузер и перейдите по следующему URL-адресу:

	**http://localhost:5001/api/Contacts**

	Затем вы сможете просмотреть содержимое *Contacts.json*. При просмотре файла вы увидите следующее содержимое:

	![Возвращенное содержимое JSON](./media/app-service-create-aspnet-api-app-using-vscode/08-contacts-json.png)

## Изменение метаданных приложения API
Метаданные, которые позволяют развертывать проект API ASP.NET как приложение API, содержатся в файле *apiapp.json* в корневой папке проекта.

1. В VSCode щелкните правой кнопкой мыши папку *wwwroot* и выберите параметр **Создать файл**.
2. Назовите новый файл *apiapp.json*.<br\> Убедитесь, что *apiapp.json* находится в папке *wwwroot*.
3. Добавьте в файл *apiapp.json* следующее:

	<pre class="prettyprint">
{
    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
    "id": "ContactsList",
    "namespace": "microsoft.com",
    "gateway": "2015-01-14",
    "version": "1.0.0",
    "title": "ContactsList",
    "summary": "",
    "author": "",
    "endpoints": null
}
</pre>

В файле *apiapp.json* можно указать конечную точку для JSON динамического определения Swagger API, но в этом учебнике будет использоваться статический файл определения API. Пример, в котором используется динамическое создание Swagger, см. в разделе [Настройка проекта веб-API как приложения API](app-service-dotnet-create-api-app-visual-studio.md).

## Добавление статического определения Swagger API
Чтобы предоставить статический файл определения Swagger 2.0 API, необходимо создать папку в корневом каталоге документов и поместить в нее файл определения API.

1. В VSCode создайте в папке *wwwroot* папку *metadata*.
2. Щелкните правой кнопкой мыши новую папку *metadata* и добавьте новый файл *apiDefinition.swagger.json*. 
3. Добавьте следующий синтаксис JSON в новый файл:

	<pre class="prettyprint">
{
  "swagger": "2.0",
  "info": {
    "version": "v1",
    "title": "ContactsList"
  },
  "host": "СЛЕДУЕТ ЗАМЕНИТЬ ЭТУ СТРОКУ НА URL-АДРЕС УЗЛА",
  "schemes": [
    "https"
  ],
  "paths": {
    "/api/Contacts": {
      "get": {
        "tags": [
          "Contacts"
        ],
        "operationId": "Contacts_Get",
        "consumes": [],
        "produces": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml"
        ],
        "responses": {
          "200": {
            "description": "OK",
            "schema": {
              "type": "array",
              "items": {
                "$ref": "#/definitions/Contact"
              }
            }
          }
        },
        "deprecated": false
      },
      "post": {
        "tags": [
          "Contacts"
        ],
        "operationId": "Contacts_Post",
        "consumes": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml",
          "application/x-www-form-urlencoded"
        ],
        "produces": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml"
        ],
        "parameters": [
          {
            "name": "contact",
            "in": "body",
            "required": true,
            "schema": {
              "$ref": "#/definitions/Contact"
            }
          }
        ],
        "responses": {
          "200": {
            "description": "OK",
            "schema": {
              "$ref": "#/definitions/Object"
            }
          }
        },
        "deprecated": false
      }
    }
  },
  "definitions": {
    "Contact": {
      "type": "object",
      "properties": {
        "Id": {
          "format": "int32",
          "type": "integer"
        },
        "Name": {
          "type": "string"
        },
        "EmailAddress": {
          "type": "string"
        }
      }
    },
    "Object": {
      "type": "object",
      "properties": {}
    }
  }
}
</pre>

Далее в этом учебнике вы замените строку заполнителя URL-адреса узла URL-адресом узла Azure, который создадите и скопируете позже.

## Создание приложения API на портале предварительной версии Azure

> [AZURE.NOTE]Для работы с этим учебником необходимо использовать учетную запись Microsoft Azure. Если у вас нет учетной записи, можно [зарегистрироваться для использования бесплатной пробной версии](/pricing/free-trial/) или [активировать преимущества для подписчиков MSDN](/pricing/member-offers/msdn-benefits-details/). Можно также бесплатно попробовать [образцы приложений службы приложений](http://tryappservice.azure.com).

1. Выполните вход на [портал предварительной версии Azure](https://portal.azure.com).

2. Щелкните команду **СОЗДАТЬ** в верхнем левом углу портала.

3. Выберите элементы **Интернет + мобильные устройства > Приложение API**.

	![Новое приложение API Azure](./media/app-service-create-aspnet-api-app-using-vscode/09-azure-newapiapp.png)

4. Введите значение в поле **Имя**, например ContactsList.

5. Выберите план службы приложений или создайте новый. При создании нового плана выберите ценовую категорию, расположение и другие параметры.

	![Колонка нового приложения API Azure](./media/app-service-create-aspnet-api-app-using-vscode/10-azure-newappblade.png)

6. Щелкните **Создать**.

	![Колонка приложения API](./media/app-service-create-aspnet-api-app-using-vscode/11-azure-apiappblade.png)

	Если оставить установленным флажок **Закрепить на начальной панели** при создании приложения, то вы сможете легко найти приложение, щелкнув **Главная** или **Обзор**. Если этот флажок снят, щелкните **Уведомления** слева, чтобы узнать состояние создания приложения API, а затем щелкните уведомление, чтобы перейти к колонке нового приложения API.

7. Щелкните элементы **Параметры > Параметры приложения**.

8. Установите для уровня доступа значение **Общедоступный (анонимный)**.

9. Щелкните **Сохранить**.

	![Параметры приложения Azure](./media/app-service-create-aspnet-api-app-using-vscode/12-azure-appsettings.png)

## Включение публикации Git из нового приложения API

Git является распределенной системой управления версиями, которую можно использовать для развертывания вашего веб-сайта Azure. Код, созданный для приложения API, будет храниться в локальном репозитории Git, а для развертывания кода в Azure он будет передаваться в удаленный репозиторий. Данный метод развертывания характерен для веб-приложений службы приложений, которые можно использовать в приложении API, поскольку приложения API основаны на веб-приложениях: приложение API в службе приложений Azure является веб-приложением с дополнительными возможностями для размещения веб-служб.

Управление функциями, связанными с приложениями API, на портале осуществляется с помощью колонки **Приложения API**, а управление функциями, которые используются совместно с веб-приложениями, — с помощью колонки **Узел приложения API**. Поэтому, выполняя действия из этого раздела, перейдите к колонке **Узел приложения API** для настройки функции развертывания Git.

1. В колонке приложения API щелкните элемент **Узел приложения API**.

	![Узел приложений API Azure](./media/app-service-create-aspnet-api-app-using-vscode/13-azure-apiapphost.png)

2. Найдите раздел **Развертывание** колонки **Приложения API** и щелкните элемент **Настройка непрерывного развертывания**. Возможно, понадобится прокрутить вниз, чтобы увидеть эту часть колонки.

	![Узел приложений API Azure](./media/app-service-create-aspnet-api-app-using-vscode/14-azure-deployment.png)

3. Щелкните **Выбор источника > Локальный репозиторий Git**.

5. Нажмите кнопку **ОК**.

	![Локальный репозиторий Git для Azure](./media/app-service-create-aspnet-api-app-using-vscode/15-azure-localrepository.png)

6. Если ранее вы не настроили учетные данные развертывания для публикации приложения API или другого приложения службы приложений, сделайте это сейчас, как показано ниже.

	* Щелкните элемент **Настройка учетных данных развертывания**.

	* Укажите имя пользователя и пароль. Этот пароль потребуется позднее при настройке Git.

	* Щелкните **Сохранить**.

	![Учетные данные развертывания Azure](./media/app-service-create-aspnet-api-app-using-vscode/16-azure-credentials.png)

7. В колонке **Узел приложения API** щелкните **Параметры > Свойства**. В разделе "URL-адрес GIT" отображается URL-адрес удаленного репозитория Git, в который выполняется развертывание.

8. Скопируйте **URL-адрес GIT** для дальнейшего использования в этом учебнике.

	![URL-адрес Git для Azure](./media/app-service-create-aspnet-api-app-using-vscode/17-azure-giturl.png)

9. Кроме того, в колонке **ПРИЛОЖЕНИЕ API** скопируйте **URL-адрес**, который будет использоваться для обновления значения host в файле *apiDefinition.swagger.json*.

	![URL-адрес Azure](./media/app-service-create-aspnet-api-app-using-vscode/18-azure-url.png)

10. В VSCode откройте файл *apiDefinition.swagger.json* и замените значение узла "СЛЕДУЕТ ЗАМЕНИТЬ ЭТУ СТРОКУ НА URL-АДРЕС УЗЛА" **URL-адресом**, скопированным на предыдущем этапе.
11. Также удалите https:// из начала значения host.
12. Сохраните изменения в файле *apiDefinition.swagger.json*.

## Публикация приложения API в службе приложений Azure

В этом разделе создается локальный репозиторий Git и выполняется принудительная отправка данных из этого репозитория в Azure для развертывания образца приложения в приложение API, работающее в службе приложений Azure.

1. В VSCode на панели навигации слева выберите параметр Git.
2. Если Git еще не установлен, установите его, щелкнув одну из указанных ссылок ([Chocolatey](https://chocolatey.org/packages/git) или [git scm.com](http://git-scm.com/downloads)). Если вы не знакомы с Git, выберите **git-scm.com** и выберите параметр, чтобы использовать Git с GitBash из командной строки Windows. 
3. После установки Git перезапустите VSCode и выберите параметр Git на левой панели.
4. В VSCode выберите **Инициализировать репозиторий Git**, чтобы задействовать систему управления версиями Git в рабочей области. 

	![Инициализация Git](./media/app-service-create-aspnet-api-app-using-vscode/19-initgit.png)

5. 
6. Добавьте сообщение о фиксации и установите флажок **Фиксировать все**.

	!["Фиксировать все" в Git](./media/app-service-create-aspnet-api-app-using-vscode/20-git-commit.png)

6. Найдите и откройте **GitBash**. В качестве альтернативы можно использовать командную строку Windows.
7. В **GitBash** измените папки на свою папку проекта VSCode, например

	<pre class="prettyprint">
cd c:\VSCodeProjects\ContactsList
</pre>

7. Создайте внешнюю ссылку для публикации обновлений в ранее созданное веб-приложение (узел приложений API), используя URL-адрес Git (заканчивающийся на .git), скопированный ранее:

	<pre class="prettyprint">
git remote add azure [URL-адрес удаленного репозитория]
</pre>

8. Отправьте обновления в Azure с помощью следующей команды:

	<pre class="prettyprint">
git push azure master
	</pre>
	Появится запрос на ввод ранее заданного пароля. **Примечание. Ваш пароль не будет отображаться.**

	Вывод этой команды завершается сообщением об успешном развертывании:

	<pre class="prettyprint">
	удаленный: успешное развертывание.
	По адресу https://user@testsite.scm.azurewebsites.net/testsite.git
[new branch]      master -> master
</pre>

> [AZURE.NOTE]При внесении изменений в приложение его можно опубликовать повторно, установив флажок **Фиксировать все** в VSCode, а затем введя команду **git push azure master** в **GitBash**.

## Просмотр определения API на портале предварительной версии Azure
Теперь, после завершения развертывания API в приложение API, можно увидеть определение API на портале предварительной версии Azure. Для начала вы перезапустите шлюз, который позволяет Azure определить, что изменилось определение API приложения API. Шлюз является веб-приложением, которое выполняет администрирование API и авторизацию для приложений API в группе ресурсов.

1. На портале предварительной версии Azure перейдите к колонке **ПРИЛОЖЕНИЕ API** для приложения API, которое было создано ранее, и щелкните ссылку **Шлюз**.
2. В колонке **ШЛЮЗ** щелкните **Перезапуск**. Теперь можно закрыть эту колонку.
3. В колонке **ПРИЛОЖЕНИЕ API** щелкните **Перезапуск**. 
4. В колонке **ПРИЛОЖЕНИЕ API** щелкните **Определение API**.<br> В колонке "Определение API" показаны два метода. Если вы сразу не увидели методы GET и POST, подождите несколько секунд, чтобы среда Azure обновила приложение. В колонке **ПРИЛОЖЕНИЕ API** щелкните **Определение API**.

## Запуск приложения в Azure
На портале предварительной версии Azure перейдите к колонке **Узел приложений API** для своего приложения API и нажмите кнопку **Обзор**. Затем добавьте **api/Contacts** в конец своего URL-адреса, чтобы просмотреть информацию о контактах.


## Заключение
В этом учебнике вы узнали, как создать приложение API в Visual Studio Code. Дополнительную информацию о Visual Studio Code см. в разделе [Visual Studio Code](https://code.visualstudio.com/Docs/). Информацию о приложениях API см. в разделе [Что такое приложения API?](app-service-api-apps-why-best-platform.md).
 

<!---HONumber=62-->