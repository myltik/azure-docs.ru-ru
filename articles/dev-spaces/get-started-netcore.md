---
title: Создание среды разработки Kubernetes в облаке с помощью .NET Core и VS Code | Документация Майкрософт
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: a57118feb85a010e38d73b758ebfb84d1cc463fa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361258"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core"></a>Начало работы в Azure Dev Spaces с .NET Core

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Теперь вы готовы создать среду разработки на основе Kubernetes в Azure.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Установка Azure CLI
Для Azure Dev Spaces требуется минимальная настройка локального компьютера. Большая часть конфигурации среды разработки хранится в облаке и доступна для других пользователей. Начните со скачивания и запуска [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

> [!IMPORTANT]
> Если у вас уже установлен интерфейс Azure CLI, убедитесь, что вы используете версию 2.0.32 или выше.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

Пока вы ждете создания кластера, вы можете начать писать код.

## <a name="create-an-aspnet-core-web-app"></a>Создание веб-приложения ASP.NET Core
Если у вас установлен [.NET Core](https://www.microsoft.com/net), вы можете быстро создать веб-приложение ASP.NET Core в папке с именем `webfrontend`.
    
```cmd
dotnet new mvc --name webfrontend
```

Можно также **скачать пример кода из GitHub**, перейдя по ссылке https://github.com/Azure/dev-spaces и выбрав **Clone or Download** (Клонировать или скачать), чтобы скачать репозиторий GitHub в локальную среду. Код для этого руководства находится в папке `samples/dotnetcore/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

## <a name="update-a-content-file"></a>Обновление файла содержимого
Azure Dev Spaces — это не просто среда выполнения кода в Kubernetes. Она позволяет быстро и итеративно видеть, как изменения вашего кода вступают в силу в среде Kubernetes в облаке.

1. Найдите файл `./Views/Home/Index.cshtml` и внесите изменения в HTML. Например, измените строку 70 `<h2>Application uses</h2>` строкой примерно такого содержания: `<h2>Hello k8s in Azure!</h2>`
1. Сохраните файл. Через несколько мгновений в окне терминала вы увидите сообщение о том, что файл в запущенном контейнере обновлен.
1. Вернитесь в браузер и обновите страницу. Должна отобразиться веб-страница с обновленным HTML.

Что произошло? Изменения файлов содержимого, таких как HTML и CSS, не требуют перекомпиляции в веб-приложении .NET Core, поэтому активная команда `azds up` автоматически синхронизирует любые измененные файлы содержимого в запущенный контейнер в Azure, чтобы можно было сразу же видеть изменения содержимого.

## <a name="update-a-code-file"></a>Обновление файла кода
Для обновления файлов кода требуется немного больше работы, так как приложение .NET Core должно перестроить и создать обновленные двоичные файлы приложений.

1. В окне терминала нажмите клавишу `Ctrl+C` (чтобы остановить `azds up`).
1. Откройте файл кода с именем `Controllers/HomeController.cs` и измените сообщение, которое будет отображаться на странице About (Сведения): `ViewData["Message"] = "Your application description page.";`
1. Сохраните файл.
1. Запустите `azds up` в окне терминала. 

Эта команда перестроит образ контейнера и повторно развернет диаграмму Helm. Чтобы увидеть, как изменения вашего кода вступили в силу в работающем приложении, перейдите в меню "Сведения" в веб-приложении.


Но есть еще один *более быстрый метод* разработки кода, который вы рассмотрите в следующем разделе. 

## <a name="debug-a-container-in-kubernetes"></a>Отладка контейнера в Kubernetes

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]


### <a name="select-the-azds-debug-configuration"></a>Выбор конфигурации отладки AZDS
1. Чтобы открыть представление отладки, щелкните значок "Отладка" на **панели действия** сбоку VS Code.
1. Выберите **.NET Core Launch (AZDS)** (Запуск .NET Core (AZDS)) как активную конфигурацию отладки.

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> Если вы не видите никаких команд Azure Dev Spaces на палитре команд, убедитесь, что вы установили расширение VS Code для Azure Dev Spaces. Убедитесь, что рабочая область, которую вы открыли в VS Code, — это папка, содержащая файл azds.yaml.


### <a name="debug-the-container-in-kubernetes"></a>Отладка контейнера в Kubernetes
Нажмите клавишу **F5**, чтобы отладить свой код в Kubernetes.

Как и команда `up`, код синхронизируется со средой разработки, а контейнер создается и развертывается в Kubernetes. На этот раз, конечно, отладчик подключен к удаленному контейнеру.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

Установите точку останова в файле кода на стороне сервера, например в функции `Index()` в исходном файле `Controllers/HomeController.cs`. Обновление страницы браузера инициирует срабатывание точки останова.

У вас есть полный доступ к отладочной информации, как если бы код выполнялся локально, например к стеку вызовов, локальным переменным, информации об исключениях и т. д.

### <a name="edit-code-and-refresh"></a>Изменение и обновление кода
С помощью активного отладчика отредактируйте код. Например, измените сообщение страницы About (Сведения) в `Controllers/HomeController.cs`. 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

Сохраните файл и в области **действий отладки** нажмите кнопку **Обновить**. 

![](media/get-started-netcore/debug-action-refresh.png)

Вместо того, чтобы перестраивать и повторно развертывать новый образ контейнера при каждой правке кода, что часто занимает много времени, Azure Dev Spaces пошагово перекомпилирует код в существующем контейнере, чтобы ускорить цикл редактирования и отладки.

В браузере обновите веб-приложение и перейдите на страницу About (Сведения). Вы должны увидеть настраиваемое сообщение в пользовательском интерфейсе.

**Теперь у вас есть метод быстрой итерации кода и отладки непосредственно в Kubernetes**. Далее вы узнаете, как создать и вызвать второй контейнер.

## <a name="call-a-service-running-in-a-separate-container"></a>Вызов службы, запущенной в отдельном контейнере

В этом разделе создается вторая служба `mywebapi`, которую будет вызывать `webfrontend`. Каждая служба будет выполняться в отдельных контейнерах. Затем вы выполните отладку в обоих контейнерах.

![Несколько контейнеров](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Скачайте пример кода для *mywebapi*.
Ради экономии времени давайте загрузим образец кода из репозитория GitHub. Перейдите к https://github.com/Azure/dev-spaces и выберите **Clone or download** (Клонировать или скачать), чтобы скачать репозиторий GitHub. Код для этого раздела находится в папке `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Запуск *mywebapi*
1. Откройте папку `mywebapi` в *отдельном окне VS Code*.
1. Нажмите клавишу F5 и подождите, пока выполнится сборка и развертывание службы. Когда все будет готово, откроется панель отладки VS Code.
1. Запишите URL-адрес конечной точки. Он будет иметь приблизительно такой вид: http://localhost:\<portnumber\>. **Подсказка. В строке состояния VS Code отобразится URL-адрес, который можно щелкнуть.** Кажется, что контейнер выполняется локально, но фактически он выполняется в нашей среде разработки в Azure. В адресе указано localhost, потому что для `mywebapi` не определены общедоступные конечные точки и доступ осуществляется только в пределах экземпляра Kubernetes. Для вашего удобства и упрощения взаимодействия с закрытой службой на локальном компьютере служба Azure Dev Spaces создает временный туннель SSH для контейнера, запущенного в Azure.
1. Когда `mywebapi` будет готово, откройте в браузере адрес localhost. Добавьте `/api/values` в URL-адрес, чтобы вызвать API GET по умолчанию для `ValuesController`. 
1. Если все шаги были успешными, вы должны увидеть ответ от службы `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Запрос из *webfrontend* к *mywebapi*
Давайте теперь напишем код в `webfrontend`, который отправляет запрос к `mywebapi`.
1. Перейдите в окно VS Code для `webfrontend`.
1. *Замените* код метода About:

    ```csharp
    public async Task<IActionResult> About()
    {
        ViewData["Message"] = "Hello from webfrontend";
        
        // Use HeaderPropagatingHttpClient instead of HttpClient so we can propagate
        // headers in the incoming request to any outgoing requests
        using (var client = new HeaderPropagatingHttpClient(this.Request))
        {
            // Call *mywebapi*, and display its response in the page
            var response = await client.GetAsync("http://mywebapi/api/values/1");
            ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
        }

        return View();
    }
    ```

Обратите внимание на то, как используется обнаружение службы DNS Kubernetes для обращения к службе в качестве `http://mywebapi`. **Код в вашей среде разработки выполняется так же, как он будет выполняться в рабочей среде**.

В приведенном выше примере кода также используется класс `HeaderPropagatingHttpClient`. Этот вспомогательный класс был добавлен в папку кода при выполнении `azds prep`. `HeaderPropagatingHttpClient` получен из известного класса `HttpClient` и добавляет функции для распространения определенных заголовков из существующего объекта ASP.NET HttpRequest в исходящий объект HttpRequestMessage. Позже вы увидите, как использование этого производного класса повышает эффективность разработки в сценариях командной работы.


### <a name="debug-across-multiple-services"></a>Отладка в нескольких службах
1. На этом этапе служба `mywebapi` по-прежнему должна выполняться с подключенным отладчиком. Если это не так, нажмите клавишу F5 в проекте `mywebapi`.
1. Установите точку останова в методе `Get(int id)`, который обрабатывает запросы GET `api/values/{id}`.
1. В проекте `webfrontend` установите точку останова до отправки запроса GET к `mywebapi/api/values`.
1. Нажмите клавишу F5 в проекте `webfrontend`.
1. Вызовите веб-приложение и пошагово выполните код в обеих службах.
1. В веб-приложении на странице About (Сведения) будет отображаться объединенное сообщение от двух служб: "Hello from webfrontend and Hello from mywebapi".


Все готово! Теперь у вас есть многоконтейнерное приложение, где каждый контейнер можно разрабатывать и развертывать отдельно.

## <a name="learn-about-team-development"></a>Сведения о коллективной разработке

[!INCLUDE[](includes/team-development-1.md)]

Давайте посмотрим на это в действии. Перейдите в окно VS Code для `mywebapi` и измените код в методе `string Get(int id)`, например:

    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]
