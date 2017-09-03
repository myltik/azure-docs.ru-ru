---
title: "Создание приложения Ruby с помощью веб-приложений на платформе Linux | Документация Майкрософт"
description: "Узнайте, как создать приложения Ruby с помощью веб-приложений на платформе Linux."
keywords: "служба приложений azure, linux, oss, ruby"
services: app-service
documentationcenter: 
author: wesmc7777
manager: erikre
editor: 
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: wesmc;rachelap
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 17f3f1a2122c508501134a0c43ab6abce412fb44
ms.contentlocale: ru-ru
ms.lasthandoff: 08/24/2017

---
# <a name="create-a-ruby-app-with-web-apps-on-linux"></a>Создание приложения Ruby с помощью веб-приложений на платформе Linux 

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

[Веб-приложения Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости. В этом кратком руководстве показано, как создать базовое приложение Ruby on Rails и развернуть его в Azure в качестве веб-приложения на платформе Linux.

![Приложение Hello World](./media/app-service-linux-ruby-get-started/hello-world-updated.png)

## <a name="prerequisites"></a>Предварительные требования

* [Ruby 2.4.1 или более поздней версии](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller).
* [Git](https://git-scm.com/downloads).
* [Активная подписка Azure](https://azure.microsoft.com/pricing/free-trial/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Скачивание примера приложения

В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер.

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="run-the-application-locally"></a>Локальный запуск приложения

Запустите сервер Rails для работы приложения. Перейдите в каталог *hello-world* и запустите сервер, выполнив команду `rails server`.

```bash
cd hello-world\bin
rails server
```
    
С помощью веб-браузера перейдите к `http://localhost:3000`, чтобы протестировать приложение локально.    

![Приложение Hello World](./media/app-service-linux-ruby-get-started/hello-world.png)

## <a name="modify-app-to-display-welcome-message"></a>Изменение приложения для отображения приветственного сообщения

Измените приложение, чтобы оно отображало приветственное сообщение. Затем измените контроллер приложения, чтобы он возвращал сообщение в формате HTML браузеру. 

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

![Приложение Hello World настроено](./media/app-service-linux-ruby-get-started/hello-world-configured.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ruby-web-app-on-azure"></a>Создание веб-приложения Ruby в Azure

Выполните команду [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create), чтобы создать план службы приложений для веб-приложения. 
 
```azurecli-interactive
  az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --is-linux
```

Далее выполните команду [az webapp create](https://docs.microsoft.com/cli/azure/webapp), чтобы создать веб-приложение, использующее созданный план службы. Обратите внимание, что для среды выполнения установлено значение `ruby|2.3`. Не забудьте указать уникальное имя приложения вместо `<app name>`.

```azurecli-interactive
  az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --runtime "ruby|2.3" --deployment-local-git
```

После создания веб-приложения страница **обзора** будет доступна для просмотра. Перейдите на эту страницу. Отобразится следующая страница заставки:

![Страница заставки](./media/app-service-linux-ruby-get-started/splash-page.png)


## <a name="deploy-your-application"></a>Развертывание приложения

Используйте Git для развертывания приложения Ruby в Azure. Для веб-приложения уже настроено развертывание Git. Выполните команду [az webapp deployment](https://docs.microsoft.com/cli/azure/webapp/deployment), чтобы извлечь URL-адрес развертывания.  

```bash
az webapp deployment source show --name <app name> --resource-group myResourceGroup
```

Обратите внимание, что URL-адрес Git имеет следующий вид в соответствии с именем вашего веб-приложения:

```bash
https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
```

[!INCLUDE [Clean-up section](../../includes/configure-deployment-user-no-h.md)]

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

После завершения развертывания перезапустите веб-приложение, чтобы изменения вступили в силу, выполнив команду [az webapp restart](https://docs.microsoft.com/cli/azure/webapp#restart), как показано ниже.

```azurecli-interactive 
az webapp restart --name <app name> --resource-group myResourceGroup
```

Перейдите на свой сайт и проверьте результаты.

```bash
http://<your web app name>.azurewebsites.net
```
![Обновленное веб-приложение](./media/app-service-linux-ruby-get-started/hello-world-updated.png)

> [!NOTE]
> Во время перезапуска приложения попытка просмотреть сайт приведет к ошибке с кодом состояния HTTP `Error 503 Server unavailable`. На полный перезапуск может потребоваться несколько минут.
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>Дальнейшие действия

[Вопросы и ответы о веб-приложении службы приложений Azure на платформе Linux](https://docs.microsoft.com/azure/app-service-web/app-service-linux-faq.md)
