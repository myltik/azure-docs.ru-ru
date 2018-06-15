---
title: Сквозная аутентификация и авторизация в Службе приложений Azure в Linux | Документация Майкрософт
description: Сведения об использовании проверки подлинности и авторизации в службе приложений для защиты приложений службы приложений, включая доступ к удаленным API.
keywords: app service, azure app service, authN, authZ, secure, security, multi-tiered, azure active directory, azure ad
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/26/2018
ms.author: cephalin
ms.openlocfilehash: a468c5d0f73cc182927f26ea9b7a85e2c5afb7c8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33766365"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service-on-linux"></a>Руководство по сквозной аутентификации и авторизации в Службе приложений Azure в Linux

[Служба приложений на платформе Linux](app-service-linux-intro.md) — это высокомасштабируемая служба размещения с самостоятельной установкой исправлений на основе операционной системы Linux. Кроме того, служба приложений имеет встроенную поддержку [проверки подлинности и авторизации пользователя](../app-service-authentication-overview.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json). В этом руководстве показано, как защитить ваши приложения с помощью проверки подлинности и авторизации в службе приложений. В качестве примера в нем используется приложение ASP.NET Core с интерфейсом Angular.js. При проверке подлинности и авторизации в службе приложений поддерживаются все языковые среды выполнения. Вы узнаете, как применить их к предпочитаемому языку, следуя руководству.

В этом руководстве используется пример приложения, чтобы показать вам, как защитить автономное приложение (в разделе [Включение проверки подлинности и авторизации в серверном приложении](#enable-authentication-and-authorization-for-back-end-app)).

![Простая проверка подлинности и авторизация](./media/tutorial-auth-aad/simple-auth.png)

В руководстве также показано, как защитить многоуровневое приложение, получив доступ к защищенному серверному API от имени прошедшего проверку подлинности пользователя, как [в коде сервера](#call-api-securely-from-server-code), так и [в коде браузера](#call-api-securely-from-browser-code).

![Расширенная проверка подлинности и авторизация](./media/tutorial-auth-aad/advanced-auth.png)

Это только некоторые из возможных сценариев проверки подлинности и авторизации в службе приложений. 

Ниже приведен полный список сведений, которые вы узнаете из этого руководства:

> [!div class="checklist"]
> * Включение встроенной проверки подлинности и авторизации.
> * Защита приложений от непроверенных запросов.
> * Использование Azure Active Directory в качестве поставщика удостоверений.
> * Доступ к удаленному приложению от имени выполнившего вход пользователя.
> * Безопасные вызовы между службами с помощью проверки подлинности с использованием токена.
> * Использование маркера доступа из серверного кода.
> * Использование маркера доступа из клиентского кода (браузер).

Шаги, описанные в этом руководстве, можно выполнить для macOS, Linux и Windows.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством:

* [Установка Git](https://git-scm.com/).
* [установите .NET Core 2.0](https://www.microsoft.com/net/core/).

## <a name="create-local-net-core-app"></a>Создание локального приложения .NET Core

На этом шаге вы настроите локальный проект .NET Core. Используйте один и тот же проект для развертывания серверного приложения API и интерфейсного веб-приложения.

### <a name="clone-and-run-the-sample-application"></a>Клонирование и запуск примера приложения

Затем выполните следующие команды, чтобы клонировать репозиторий с примером и запустить его.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

Перейдите к `http://localhost:5000` и попробуйте добавить, изменить и удалить объекты списка дел. 

![Выполняющийся локально API ASP.NET Core](./media/tutorial-auth-aad/local-run.png)

Чтобы остановить ASP.NET Core в любое время, нажмите клавиши `Ctrl+C` в окне терминала.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-apps-to-azure"></a>Развертывание приложений в Azure

На этом шаге разверните проект в двух приложениях службы приложений. Одно из которых интерфейсное приложение, а второе — серверное.

### <a name="create-azure-resources"></a>Создание ресурсов Azure

В Cloud Shell введите следующие команды, чтобы создать два веб-приложения: Замените _&lt;front\_end\_app\_name>_ и _&lt;back\_end\_app\_name>_ на два глобально уникальных имени приложений (допустимые символы — `a-z`, `0-9` и `-`). Дополнительные сведения о каждой команде см. в статье [Создание веб-приложения .NET Core в службе приложений на платформе Linux](quickstart-dotnetcore.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku B1 --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front_end_app_name> --runtime "dotnetcore|2.0" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back_end_app_name> --runtime "dotnetcore|2.0" --deployment-local-git --query deploymentLocalGitUrl
```

> [!NOTE]
> Сохраните URL-адреса удаленных элементов Git вашего интерфейсного и серверного приложения, которые отображаются в выходных данных `az webapp create`.
>

### <a name="configure-cors"></a>Настройка CORS

Этот шаг не связан с проверкой подлинности и авторизацией. Тем не менее, это нужно для [вызова серверного API из кода внешнего браузера](#call-api-securely-from-browser-code), чтобы браузер разрешал междоменные вызовы API из приложения Angular.js. В службе приложений на платформе Linux отсутствуют встроенные функции CORS, которые есть в ее [аналоге для Windows](../app-service-web-tutorial-rest-api.md#add-cors-functionality). Поэтому для работы серверного приложения их нужно добавить вручную.

В локальном репозитории откройте файл _Startup.cs_. Добавьте в метод `ConfigureServices(IServiceCollection services)` следующую строку кода:

```csharp
services.AddCors();
```

В начало метода `Configure(IApplicationBuilder app)` добавьте следующую строку кода, заменив *\<front_end_app_name>*:

```csharp
app.UseCors(builder =>
    builder.WithOrigins("http://<front_end_app_name>.azurewebsites.net"));
```

Сохраните изменения. В _окне терминала на локальном компьютере_ выполните следующие команды, чтобы зафиксировать изменения в репозитории Git.

```bash
git add .
git commit -m "add CORS to back end"
```

> [!NOTE]
> Не беспокойтесь о том, что этот код совместно используется во серверном и интерфейсном и во приложении. Это никак не повлияет на работу механизма CORS во внешнем приложении.
> 

### <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

В окне терминала на локальном компьютере выполните следующие команды Git, чтобы выполнить развертывание в серверное приложение. Замените _&lt;deploymentLocalGitUrl-of-back-end-app>_ URL-адресом удаленного репозитория Git, который вы сохранили при [создании ресурсов Azure](#create-azure-resources). При появлении запроса на ввод учетных данных в диспетчере учетных данных Git введите [учетные данные развертывания](../app-service-deployment-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) (а не используемые для входа на портал Azure).

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

В окне терминала на локальном компьютере выполните следующие команды Git, чтобы выполнить развертывание аналогичного кода в интерфейсное приложение. Замените _&lt;deploymentLocalGitUrl-of-front-end-app>_ URL-адресом удаленного репозитория Git, который вы сохранили при [создании ресурсов Azure](#create-azure-resources).

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-azure-web-apps"></a>Переход к веб-приложению Azure

Перейдите по следующим URL-адресам в браузере и посмотрите, как работают два приложения.

```
http://<back_end_app_name>.azurewebsites.net
http://<front_end_app_name>.azurewebsites.net
```

![API ASP.NET Core, выполняющийся в службе приложений Azure](./media/tutorial-auth-aad/azure-run.png)

> [!NOTE]
> Если приложение перезапускается, возможно, вы заметили, что новые данные были удалены. Это сделано намеренно, так как пример приложения ASP.NET Core использует базу данных в памяти.
>
>

## <a name="call-back-end-api-from-front-end"></a>Вызов серверного API из внешнего интерфейса

На этом шаге укажите код сервера интерфейсного приложения для доступа к серверному API. Позже вы включите доступ с проверкой подлинности от интерфейсной части к серверной.

### <a name="modify-front-end-code"></a>Изменение интерфейсного кода

В локальном репозитории откройте _Controllers/TodoController.cs_. В начале класса `TodoController` добавьте следующие строки и замените _&lt;back\_end\_app\_name>_ именем вашего серверного приложения:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back_end_app_name>.azurewebsites.net";
```

Найдите метод `GetAll()` и замените код внутри фигурных скобок:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo").Result;
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

Первая строка осуществляет вызов `GET /api/Todo` к приложению серверного API.

Затем найдите метод `GetById(long id)` и замените код внутри фигурных скобок:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return Content(data, "application/json");
```

Первая строка осуществляет вызов `GET /api/Todo/{id}` к приложению серверного API.

Затем найдите метод `Create([FromBody] TodoItem item)` и замените код внутри фигурных скобок:

```cs
var response = _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", item).Result;
var data = response.Content.ReadAsStringAsync().Result;
return Content(data, "application/json");
```

Первая строка осуществляет вызов `POST /api/Todo` к приложению серверного API.

Затем найдите метод `Update(long id, [FromBody] TodoItem item)` и замените код внутри фигурных скобок:

```cs
var res = _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", item).Result;
return new NoContentResult();
```

Первая строка осуществляет вызов `PUT /api/Todo/{id}` к приложению серверного API.

Затем найдите метод `Delete(long id)` и замените код внутри фигурных скобок:

```cs
var res = _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return new NoContentResult();
```

Первая строка осуществляет вызов `DELETE /api/Todo/{id}` к приложению серверного API.

Сохраните все изменения. В окне терминала на локальном компьютере выполните следующие команды Git, чтобы выполнить развертывание аналогичного кода в интерфейсное приложение.

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>Проверка изменений

Перейдите к `http://<front_end_app_name>.azurewebsites.net` и добавьте несколько элементов, например `from front end 1` и `from front end 2`.

Перейдите к `http://<back_end_app_name>.azurewebsites.net`, чтобы просмотреть элементы, добавленные из интерфейсного приложения. Кроме того, добавьте несколько элементов, например `from back end 1` и `from back end 2`, а затем обновите интерфейсное приложение, чтобы проверить изменения.

![API ASP.NET Core, выполняющийся в службе приложений Azure](./media/tutorial-auth-aad/remote-api-call-run.png)

## <a name="configure-auth"></a>Настройка проверки подлинности

На этом шаге включите проверку подлинности и авторизацию для двух приложений. Кроме того, настройте создание маркера доступа в интерфейсном приложении, который вы можете использовать для осуществления прошедших проверку подлинности вызовов серверного приложения.

Azure Active Directory используется в качестве поставщика удостоверений. Дополнительные сведения см. в статье [Настройка приложения службы приложений для использования входа с помощью Azure Active Directory](../app-service-mobile-how-to-configure-active-directory-authentication.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Включение проверки подлинности и авторизации в серверном приложении

На [портале Azure](https://portal.azure.com) откройте страницу управления вашим серверным приложением, щелкнув в меню слева **Группы ресурсов** > **myAuthResourceGroup** > _\<back\_end\_app\_name>_.

![API ASP.NET Core, выполняющийся в службе приложений Azure](./media/tutorial-auth-aad/portal-navigate-back-end.png)

В левом меню вашего серверного приложения щелкните **Проверка подлинности/авторизация**, а затем включите проверку подлинности службы приложений, нажав **Включено**.

В раскрывающемся списке **Предпринимаемое действие, если проверка подлинности для запроса не выполнена** выберите **Войти с использованием Azure Active Directory**.

В разделе **Поставщики проверки подлинности** щелкните **Azure Active Directory**. 

![API ASP.NET Core, выполняющийся в службе приложений Azure](./media/tutorial-auth-aad/configure-auth-back-end.png)

Щелкните **Экспресс**, затем примите настройки по умолчанию, чтобы создать приложение AD, и нажмите кнопку**OK**.

На странице **Проверка подлинности/авторизация** нажмите кнопку **Сохранить**. 

После того, как вы увидите уведомление с сообщением `Successfully saved the Auth Settings for <back_end_app_name> App`, обновите страницу.

Щелкните **Azure Active Directory** еще раз, а затем щелкните **Управление приложением**.

На странице управления приложением AD скопируйте в блокнот **идентификатор приложения**. Это значение понадобится позже.

![API ASP.NET Core, выполняющийся в службе приложений Azure](./media/tutorial-auth-aad/get-application-id-back-end.png)

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Включение проверки подлинности и авторизации в интерфейсном приложении

Выполните те же действия для внешнего приложения, но пропустите последний шаг. Для интерфейсного приложения **идентификатор приложения** не требуется. Не закрывайте страницу **Параметры Azure Active Directory**.

При необходимости перейдите по ссылке `http://<front_end_app_name>.azurewebsites.net`. Теперь она должна направить вас на страницу входа. После выполнения входа вы по-прежнему не сможете получить доступ к данным из серверного приложения, потому что вам все еще нужно сделать три вещи:

- Предоставить доступ из интерфейсной части к серверной.
- Настроить службу приложений, чтобы вернуть доступный токен.
- Использовать токен в коде.

> [!TIP]
> Если вы столкнулись с ошибками и перенастроили параметры проверки подлинности или авторизации в своем приложении, токены в хранилище токенов не могут быть восстановлены из новых параметров. Чтобы убедиться, что ваши токены восстановлены, вам нужно выйти из приложения и войти в него. Для этого проще всего использовать браузер в режиме защищенного просмотра, затем закрыть и снова открыть браузер в режиме защищенного просмотра после изменения параметров в своих приложениях.

### <a name="grant-front-end-app-access-to-back-end"></a>Предоставление интерфейсному приложению доступа к серверной части

Теперь, когда вы включили проверку подлинности и авторизацию для обоих ваших приложений, каждое из них поддерживается приложением AD. На этом шаге предоставьте разрешения интерфейсному приложению для доступа к серверной части от имени пользователя. (Технически вы предоставляете интерфейсному _приложению AD_ разрешения на доступ к серверному _приложению AD_ от имени пользователя).

На этом этапе у вас должна быть открыта страница **параметров Azure Active Directory** внешнего приложения. Если нет, вернитесь на эту страницу. 

Щелкните **Управление разрешениями** > **Добавить** > **Выбор API**.

![API ASP.NET Core, выполняющийся в службе приложений Azure](./media/tutorial-auth-aad/add-api-access-front-end.png)

На странице **Выбор API** введите имя приложения AD вашего серверного приложения, которое по умолчанию совпадает с именем серверного приложения. Выберите его в списке и нажмите кнопку**Выбрать**.

Установите флажок рядом с параметром **Access _&lt;AD\_application\_name>_** (Доступ к <имя_приложения_AD>). Последовательно щелкните **Выбрать** > **Готово**.

![API ASP.NET Core, выполняющийся в службе приложений Azure](./media/tutorial-auth-aad/select-permission-front-end.png)

### <a name="configure-app-service-to-return-a-usable-access-token"></a>Настройка службы приложений для возвращения используемых маркеров доступа

Теперь интерфейсное приложение имеет необходимые разрешения. На этом шаге настройте проверку подлинности и авторизацию в службе приложений, чтобы получить доступный маркер доступа для доступа к серверной части. Для этого шага вам понадобится идентификатор серверного приложения, который вы скопировали на шаге [Включение проверки подлинности и авторизации в серверном приложении](#enable-authentication-and-authorization-for-back-end-app).

Войдите в [обозреватель ресурсов Azure](https://resources.azure.com). В верхней части страницы щелкните **Чтение и запись**, чтобы внести изменения в обозревателе ресурсов Azure.

![API ASP.NET Core, выполняющийся в службе приложений Azure](./media/tutorial-auth-aad/resources-enable-write.png)

В левой части браузера щелкните **subscriptions** > **_&lt;ваша\_подписка>_** > **resourceGroups** > **myAuthResourceGroup** > **providers** > **Microsoft.Web** > **sites** > **_\<имя\_\_интерфейсного\_приложения>_** > **config** > **authsettings**.

В представлении **authsettings** щелкните **Изменить**. Установите значение `additionalLoginParams` в следующую строку JSON, используя код приложения, который вы скопировали. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back_end_application_id>"],
```

![API ASP.NET Core, выполняющийся в службе приложений Azure](./media/tutorial-auth-aad/additional-login-params-front-end.png)

Сохраните настройки, щелкнув **PUT**.

Теперь приложение настроено. Теперь интерфейсная часть готова к доступу к серверной части с помощью соответствующего маркера доступа.

Сведения о том, как настроить это в других поставщиках, см. в разделе об [обновлении маркеров доступа](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#refresh-access-tokens).

## <a name="call-api-securely-from-server-code"></a>Безопасный вызов API из кода сервера

На этом шаге активируйте ранее измененный код сервера, чтобы выполнять аутентифицированные вызовы серверного API.

Теперь ваше интерфейсное приложение имеет требуемое разрешение, а также добавляет идентификатор серверного приложения к параметрам входа. Таким образом, оно может получить маркер доступа для проверки подлинности в серверном приложении. Служба приложений поставляет этот токен в код сервера, внедряя заголовок `X-MS-TOKEN-AAD-ACCESS-TOKEN` в каждый аутентифицированный запрос (см. раздел об [извлечении токена в коде приложения](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#retrieve-tokens-in-app-code)).

> [!NOTE]
> Эти заголовки внедряются для всех поддерживаемых языков. Доступ к ним осуществляется с помощью стандартного шаблона для каждого соответствующего языка.

В локальном репозитории снова откройте _Controllers/TodoController.cs_. В конструкторе `TodoController(TodoContext context)` добавьте следующий код.

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["x-ms-token-aad-access_token"]);
}
```

Этот код добавляет стандартный HTTP-заголовок `Authorization: Bearer <access_token>` ко всем удаленным вызовам API. В конвейере выполните запрос ASP.NET Core MVC. `OnActionExecuting` выполняется непосредственно перед соответствующим методом действия (например, `GetAll()`), поэтому каждый исходящий вызовов API теперь представляет маркер доступа.

Сохраните все изменения. В окне терминала на локальном компьютере выполните следующие команды Git, чтобы выполнить развертывание аналогичного кода в интерфейсное приложение.

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Войдите в `http://<front_end_app_name>.azurewebsites.net` еще раз. На странице соглашения об использовании пользовательских данных нажмите кнопку **Принять**.

Теперь вы можете создавать, читать, обновлять и удалять данные из серверного приложения прямо в приложении. Единственное отличие теперь в том, что оба приложения защищены проверкой подлинности и авторизацией службы приложений, включая вызовы между службами.

Поздравляем! Код сервера теперь получает доступ к данным серверной части от имени пользователя, прошедшего проверку подлинности.

## <a name="call-api-securely-from-browser-code"></a>Безопасный вызов API из кода браузера

На этом шаге укажите внешний API для интерфейсного приложения Angular.js. Таким образом, вы узнаете, как получить маркер доступа и осуществлять с его помощью вызовы API к серверному приложению.

Код сервера имеет доступ к заголовкам запроса, код клиента может получить доступ к `GET /.auth/me`, чтобы получить те же токены доступа (см. раздел[Получение токенов в коде приложения](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#retrieve-tokens-in-app-code)).

> [!TIP]
> В этом разделе используются стандартные методы HTTP, чтобы продемонстрировать безопасные вызовы HTTP. Тем не менее, вы можете использовать [библиотеку проверки подлинности Active Directory (ADAL) для JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js), чтобы упростить шаблон приложения Angular.js.
>

### <a name="point-angularjs-app-to-back-end-api"></a>Указание серверного API для приложения Angular.js

В локальном репозитории откройте _wwwroot/index.html_.

В строке 51 задайте для переменной `apiEndpoint` URL-адрес серверного приложения (`http://<back_end_app_name>.azurewebsites.net`). Замените _\<имя\_приложения\_серверной\_части>_ именем своего приложения в службе приложений.

Откройте локальный репозиторий _wwwroot/app/scripts/todoListSvc.js_ и убедитесь, что `apiEndpoint` указан для всех вызовов API. Теперь приложение Angular.js вызывает серверные API-интерфейсы. 

### <a name="add-access-token-to-api-calls"></a>Добавление маркера доступа в вызовы API

В _wwwroot/app/scripts/todoListSvc.js_ над списком вызовов API (над строкой `getItems : function(){`) добавьте следующую функцию в список:

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

Эта функция вызывается для установки заголовка `Authorization` по умолчанию с маркером доступа. Он будет вызываться на следующем шаге.

В локальном репозитории _wwwroot/app/scripts/app.js_ найдите следующий код:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

Замените весь блок кода следующим кодом.

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

При новом изменении добавляется сопоставление `revolve`, которое вызывает `/.auth/me` и устанавливает маркер доступа. Это гарантирует, что у вас есть маркер доступа до создания экземпляра контроллера `todoListCtrl`. Таким образом, все вызовы API с помощью контроллера содержат токен.

### <a name="deploy-updates-and-test"></a>Развертывание и тестирование

Сохраните все изменения. В окне терминала на локальном компьютере выполните следующие команды Git, чтобы выполнить развертывание аналогичного кода в интерфейсное приложение.

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Перейдите к `http://<front_end_app_name>.azurewebsites.net` еще раз. Теперь вы можете создавать, читать, обновлять и удалять данные с серверного приложения прямо в приложении Angular.js.

Поздравляем! Код клиента теперь получает доступ к данным серверной части от имени пользователя, прошедшего проверку подлинности.

## <a name="clean-up-resources"></a>Очистка ресурсов

На предыдущем шаге вы создали ресурсы Azure в группе ресурсов. Если эти ресурсы вам не понадобятся в будущем, вы можете удалить группу ресурсов, выполнив следующую команду в Cloud Shell:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

Ее выполнение может занять до минуты.

<a name="next"></a>
## <a name="next-steps"></a>Дополнительная информация

Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Включение встроенной проверки подлинности и авторизации.
> * Защита приложений от непроверенных запросов.
> * Использование Azure Active Directory в качестве поставщика удостоверений.
> * Доступ к удаленному приложению от имени выполнившего вход пользователя.
> * Безопасные вызовы между службами с помощью проверки подлинности с использованием токена.
> * Использование маркера доступа из серверного кода.
> * Использование маркера доступа из клиентского кода (браузер).

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена с веб-приложением.

> [!div class="nextstepaction"]
> [Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
