---
title: "Создание статического веб-приложения HTML в Azure за пять минут | Документация Майкрософт"
description: "Узнайте, как можно быстро запускать веб-приложения в службе приложений, развернув пример приложения."
services: app-service\web
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/08/2017
ms.author: riande
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 895906e1ab4bc50093ed3b18f043c3dd515ca054
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---
# <a name="create-a-static-html-web-app-in-azure-in-five-minutes"></a>Создание статического веб-приложения HTML в Azure за пять минут

Это краткое руководство поможет развернуть базовый сайт HTML+CSS в Azure. Вы запустите приложение, используя [план службы приложений Azure](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview), а затем создадите в нем веб-приложение с помощью [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli). Используйте Git, чтобы развернуть приложение в Azure. Для работы с этим руководством потребуется около 5 минут (при условии, что предварительные требования уже выполнены).

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы скачайте и установите следующие компоненты:

- [Git.](https://git-scm.com/)
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Скачивание примера приложения

В окне терминала клонируйте репозиторий с примером приложения на локальный компьютер.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="view-the-html"></a>Просмотр HTML

Перейдите в каталог, в котором содержится пример HTML. Откройте файл *index.html* в браузере.

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser.png)

[!INCLUDE [login-to-azure](../../includes/login-to-azure.md)] 
[!INCLUDE [configure-deployment-user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [app-service-web-quickstart1](../../includes/app-service-web-quickstart1.md)] 

Создайте [веб-приложение](app-service-web-overview.md) в плане службы приложений `quickStartPlan`. Веб-приложение предоставляет место для размещения кода, а также URL-адрес для просмотра развернутого приложения.

[!INCLUDE [app-service-web-quickstart2](../../includes/app-service-web-quickstart2.md)] 

Страница выполняется как веб-приложение службы приложений Azure:

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="update-and-redeploy-the-app"></a>Обновление и повторное развертывание приложения

Откройте файл *index.html* . Измените разметку. Например, измените `Hello world!` на `Hello Azure!`.

Зафиксируйте изменения в Git, а затем отправьте изменения кода в Azure.

```bash
git commit -am "updated HTML"
git push azure master
```

Завершив развертывание, обновите страницу в браузере, чтобы увидеть изменения.

[!INCLUDE [manage-azure-web-app](../../includes/manage-azure-web-app.md)]


[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с примерами [сценариев интерфейса командной строки для веб-приложений](app-service-cli-samples.md).
- Узнайте, как [сопоставить имя личного домена](app-service-web-tutorial-custom-domain.md), например contoso.com, с [приложением службы приложений](app-service-web-tutorial-custom-domain.md).
