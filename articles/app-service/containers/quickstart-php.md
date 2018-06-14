---
title: Создание и развертывание веб-приложения PHP в службе приложений Azure на платформе Linux | Документация Майкрософт
description: Разверните первое приложение PHP Hello World в службе приложений на платформе Linux в считанные минуты.
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/30/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 1cbd29b480c80c93f750b97d47ccb3c09c984942
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30230652"
---
# <a name="create-a-php-web-app-in-app-service-on-linux"></a>Создание веб-приложения PHP в службе приложений на платформе Linux

> [!NOTE]
> В этой статье мы развернем приложение в службе приложений на платформе Linux. Сведения о развертывании в службе приложений на платформе _Windows_ см. в статье [Создание веб-приложения PHP в Azure](../app-service-web-get-started-php.md).
>

[Служба приложений на платформе Linux](app-service-linux-intro.md) — это высокомасштабируемая служба размещения с самостоятельной установкой исправлений на основе операционной системы Linux. Из этого краткого руководства вы узнаете, как развернуть приложение PHP в службе приложений Azure на платформе Linux. Создайте веб-приложение со встроенным образом при помощи [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) в Cloud Shell и разверните пример кода PHP в веб-приложении с помощью Git.

![Пример приложения, выполняющегося в Azure](media/quickstart-php/hello-world-in-browser.png)

Выполните инструкции, приведенные в этом руководстве, с помощью компьютера Mac, Windows или Linux.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим кратким руководством сделайте следующее:

* <a href="https://git-scm.com/" target="_blank">установите Git</a>;
* <a href="https://php.net" target="_blank">установите PHP</a>.

## <a name="download-the-sample"></a>Скачивание примера приложения

В окне терминала выполните команды ниже. Так вы клонируете пример приложения на локальный компьютер и перейдете в каталог, содержащий пример кода.

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>Локальный запуск приложения

Запустите приложение в локальной среде, открыв окно терминала и выполнив команду `php`, чтобы запустить встроенный веб-сервер PHP.

```bash
php -S localhost:8080
```

Откройте веб-браузер и перейдите к примеру приложения по адресу `http://localhost:8080`.

На странице отобразится сообщение **Hello World!** из примера приложения.

![Пример приложения, выполняющегося локально](media/quickstart-php/localhost-hello-world-in-browser.png)

В окне терминала нажмите клавиши **CTRL+C**, чтобы выйти из веб-сервера.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Создание веб-приложения

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

Перейдите на сайт, чтобы просмотреть созданное веб-приложение со встроенным образом. Замените _&lt;имя_приложения>_ уникальным именем веб-приложения.

```bash
http://<app_name>.azurewebsites.net
```

Новое веб-приложение должно выглядеть так:

![Пустая страница веб-приложения](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
   cc39b1e..25f1805  master -> master
```

## <a name="browse-to-the-app"></a>Переход в приложение

Перейдите в развертываемое приложение с помощью веб-браузера.

```bash
http://<app_name>.azurewebsites.net
```

Пример кода PHP выполняется в веб-приложении со встроенным образом.

![Пример приложения, выполняющегося в Azure](media/quickstart-php/hello-world-in-browser.png)

**Поздравляем!** Вы развернули свое первое приложение PHP в службе приложений на платформе Linux.

## <a name="update-locally-and-redeploy-the-code"></a>Обновление на локальном компьютере и повторное развертывание кода

В локальном каталоге в приложении PHP откройте файл `index.php` и внесите небольшое изменение в текстовой строке рядом с `echo`:

```php
echo "Hello Azure!";
```

Зафиксируйте изменения в Git, а затем отправьте изменения кода в Azure.

```bash
git commit -am "updated output"
git push azure master
```

После завершения развертывания перейдите в окно браузера, открытое на шаге **перехода в приложение**, и обновите страницу.

![Обновленный пример приложения, выполняющегося в Azure](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Управление новым веб-приложением Azure

Перейдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a> для управления созданным веб-приложением.

В меню слева выберите **Службы приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

Отобразится страница обзора вашего веб-приложения. Вы можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление.

![Страница службы приложений на портале Azure](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

В меню слева доступно несколько страниц для настройки приложения. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Использование PHP и MySQL](tutorial-php-mysql-app.md)
