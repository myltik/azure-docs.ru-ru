---
title: "Создание приложения Ruby и его развертывание в службе приложений Azure на платформе Linux | Документация Майкрософт"
description: "Узнайте, как создать приложения Ruby с помощью службы приложений на платформе Linux."
keywords: "служба приложений azure, linux, oss, ruby"
services: app-service
documentationcenter: 
author: SyntaxC4
manager: cfowler
editor: 
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/10/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: a54ef1ae40ba6ea9ad604a29c67e41228c0d5946
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2017
---
# <a name="create-a-ruby-app-in-app-service-on-linux"></a>Создание приложения Ruby в службе приложений на платформе Linux

[Служба приложений на платформе Linux](app-service-linux-intro.md) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости. В этом кратком руководстве показано, как создать базовое приложение Ruby on Rails и развернуть его в Azure в качестве веб-приложения на платформе Linux.

![Приложение Hello World](./media/quickstart-ruby/hello-world-updated.png)

## <a name="prerequisites"></a>Предварительные требования

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Установите Ruby начиная с версии 2.4.1</a>.
* <a href="https://git-scm.com/" target="_blank">установите Git</a>;

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Скачивание примера приложения

В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер.

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Локальный запуск приложения

Запустите сервер Rails для работы приложения. Перейдите в каталог *hello-world* и запустите сервер, выполнив команду `rails server`.

```bash
cd hello-world\bin
rails server
```

С помощью веб-браузера перейдите к `http://localhost:3000`, чтобы протестировать приложение локально.

![Приложение Hello World](./media/quickstart-ruby/hello-world.png)

## <a name="modify-app-to-display-welcome-message"></a>Изменение приложения для отображения приветственного сообщения

Измените приложение, чтобы оно отображало приветственное сообщение. Во-первых, вам нужно настроить маршрут, включив в файл *~/workspace/ruby-docs-hello-world/config/routes.rb* маршрут с именем `hello`.

  ```ruby
  Rails.application.routes.draw do
      #For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
      root 'application#hello'
  end
  ```

Затем измените контроллер приложения, чтобы он возвращал сообщение в формате HTML браузеру. 

Откройте файл *~/workspace/hello-world/app/controllers/application_controller.rb* для редактирования. Измените класс `ApplicationController`, как показано в следующем примере кода:

  ```ruby
  class ApplicationController > ActionController :: base
    protect_from_forgery with: :exception
    def hello
      render html: "Hello, world from Azure Web App on Linux!"
    end
  end
  ```

Теперь приложение настроено. С помощью веб-браузера перейдите к `http://localhost:3000`, чтобы проверить корневую целевую страницу.

![Приложение Hello World настроено](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

## <a name="create-a-ruby-web-app-on-azure"></a>Создание веб-приложения Ruby в Azure

Группа ресурсов используется для размещения ресурсов, требуемых веб-приложением. Чтобы создать группу ресурсов, используйте команду [az group create]().

```azurecli-interactive
az group create --location westeurope --name myResourceGroup
```

Выполните команду [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create), чтобы создать план службы приложений для веб-приложения.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --is-linux
```

Далее выполните команду [az webapp create](https://docs.microsoft.com/cli/azure/webapp), чтобы создать веб-приложение, использующее созданный план службы. Обратите внимание, что для среды выполнения установлено значение `ruby|2.3`. Не забудьте указать уникальное имя приложения вместо `<app name>`.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> \
--runtime "ruby|2.3" --deployment-local-git
```

Выходные данные команды включают сведения о созданном веб-приложении, а также содержат URL-адрес развертывания. Вы увидите что-то похожее на пример ниже. Скопируйте URL-адрес для дальнейшего использования в этом руководстве.

```bash
https://<deployment user name>@<app name>.scm.azurewebsites.net/<app name>.git
```

После создания веб-приложения страница **обзора** будет доступна для просмотра. Перейдите на эту страницу. Отобразится следующая страница заставки:

![Страница заставки](./media/quickstart-ruby/splash-page.png)


## <a name="deploy-your-application"></a>Развертывание приложения

Выполните следующие команды, чтобы развернуть локальное приложение на веб-сайте Azure.

```bash
git remote add azure <Git deployment URL from above>
git add -A
git commit -m "Initial deployment commit"
git push azure master
```

Убедитесь, что операции удаленного развертывания успешно выполнены. Выходные данные команд будут выглядеть следующим образом:

```bash
remote: Using sass-rails 5.0.6
remote: Updating files in vendor/cache
remote: Bundle gems are installed into ./vendor/bundle
remote: Updating files in vendor/cache
remote: ~site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
  579ccb....2ca5f31  master -> master
myuser@ubuntu1234:~workspace/<app name>$
```

После завершения развертывания перезапустите веб-приложение, чтобы изменения вступили в силу, выполнив команду [az webapp restart](https://docs.microsoft.com/cli/azure/webapp#az_webapp_restart), как показано ниже.

```azurecli-interactive
az webapp restart --name <app name> --resource-group myResourceGroup
```

Перейдите на свой сайт и проверьте результаты.

```bash
http://<app name>.azurewebsites.net
```

![Обновленное веб-приложение](./media/quickstart-ruby/hello-world-updated.png)

> [!NOTE]
> Во время перезапуска приложения попытка просмотреть сайт приведет к ошибке с кодом состояния HTTP `Error 503 Server unavailable`. На полный перезапуск может потребоваться несколько минут.
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Дальнейшие действия

[Служба приложений Azure на платформе Linux: вопросы и ответы](https://docs.microsoft.com/azure/app-service-web/app-service-linux-faq.md)
