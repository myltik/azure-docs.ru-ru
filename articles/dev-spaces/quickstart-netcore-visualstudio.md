---
title: Создание среды разработки Kubernetes в облаке | Документация Майкрософт
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: quickstart
description: Быстрая разработка Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 9bee5677aecb235872f50eea75ddc98bc453f426
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361721"
---
# <a name="quickstart-create-a-kubernetes-development-environment-with-azure-dev-spaces-net-core-and-visual-studio"></a>Краткое руководство по созданию среды разработки Kubernetes с помощью Azure Dev Spaces (.NET Core и Visual Studio)

В этом руководстве вы узнаете как:

- создать в Azure среду на основе Kubernetes, которая оптимизирована для разработки;
- итеративно разрабатывать код в контейнерах с помощью Visual Studio.

[!INCLUDE[](includes/see-troubleshooting.md)]

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="get-the-visual-studio-tools"></a>Получение средств Visual Studio 
1. Установите последнюю версию [Visual Studio 2017](https://www.visualstudio.com/vs/).
1. Убедитесь, что выбрана следующая рабочая нагрузка в установщике Visual Studio:
    * ASP.NET и веб-разработка.
1. Установите [расширение Visual Studio для Azure Dev Spaces](https://aka.ms/get-azds-visualstudio)

Теперь вы готовы создать веб-приложение ASP.NET с помощью Visual Studio.

## <a name="create-an-aspnet-web-app"></a>Создание веб-приложения ASP.NET

Создайте новый проект в Visual Studio 2017. В настоящее время проект должен быть **веб-приложением ASP.NET Core**. Присвойте проекту имя **webfrontend**.

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

Выберите шаблон **Веб-приложение (модель-представление-контроллер)** и выберите **.NET Core** и **ASP.NET Core 2.0** в двух раскрывающихся списках в верхней части диалогового окна. Нажмите кнопку **ОК** , чтобы создать проект.

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)


## <a name="create-a-dev-environment-in-azure"></a>Создание среды разработки в Azure

В Azure Dev Spaces вы можете создавать среды разработки на основе Kubernetes, которые полностью управляются Azure и оптимизированы для разработки. В только что созданном проекте из раскрывающегося списка параметров запуска выберите **Azure Dev Spaces**, как показано ниже.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

В появившемся диалоговом окне убедитесь, что вы вошли в систему с соответствующей учетной записью, а затем выберите существующий кластер.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

В раскрывающемся списке **Пространство** оставьте пока значение по умолчанию (`default`). Позже вы узнаете больше об этом параметре. Установите флажок **Publicly Accessible** (Общедоступное), чтобы веб-приложение было доступно через общедоступную конечную точку. Этот параметр не является обязательным, но он будет полезен для демонстрации некоторых концепций далее в этом пошаговом руководстве. Но не волнуйтесь, вы в любом случае сможете выполнить отладку веб-сайта с помощью Visual Studio.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Нажмите кнопку **ОК**, чтобы выбрать или создать кластер.

Если выбран кластер, который еще не был настроен для работы с Azure Dev Spaces, вы увидите сообщение с вопросом, хотите ли вы настроить его.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Нажмите кнопку **ОК**. 

Запустится задача в фоновом режиме. Выполнение может занять несколько минут. Чтобы увидеть, находится ли кластер в процессе создания, наведите указатель мыши на значок **фоновые задачи** в левом нижнем углу строки состояния, как показано на следующем изображении.

![](media/get-started-netcore-visualstudio/BackgroundTasks.png)

> [!Note]
Пока среда разработки не будет успешно создана, вы не сможете отлаживать свое приложение.

## <a name="look-at-the-files-added-to-project"></a>Просмотр файлов, добавленных в проект
Пока вы ожидаете создания среды разработки просмотрите файлы, которые были добавлены в ваш проект при выборе среды разработки.

Во-первых, вы можете увидеть, что была добавлена папка с именем `charts`, в которой создана [диаграмма Helm](https://docs.helm.sh) для вашего приложения. Эти файлы используются для развертывания приложения в среду разработки.

Вы увидите добавленный файл с именем `Dockerfile`. Этот файл содержит сведения, необходимые для упаковки приложения в стандартном формате Docker. Также был создан файл `HeaderPropagation.cs`. Мы обсудим его позже в пошаговом руководстве. 

Наконец, вы увидите файл с именем `azds.yaml`, содержащий сведения о конфигурации, необходимые для среды разработки. Например, должно ли приложение быть доступным через общедоступную конечную точку.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Отладка контейнера в Kubernetes
После успешного создания среды разработки вы сможете приступить к отладке приложения. Установите точку останова в коде, например, в строке 20 в файле `HomeController.cs`, где установлена ​​переменная `Message`. Нажмите клавишу **F5**, чтобы запустить отладку. 

Visual Studio будет взаимодействовать со средой разработки для сборки и развертывания приложения, а затем откроет браузер с работающим веб-приложением. Может показаться, что контейнер выполняется локально, хотя фактически он выполняется в среде разработки в Azure. В адресе указано localhost, потому что Azure Dev Spaces создает временный туннель SSH к контейнеру, работающему в Azure.

Перейдите по ссылке **About** (Сведения) в верхней части страницы, чтобы вызвать точку останова. У вас есть полный доступ к отладочной информации, как если бы код выполнялся локально, например к стеку вызовов, локальным переменным, информации об исключениях и т. д.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Работа с несколькими контейнерами и командной разработкой](get-started-netcore-visualstudio.md#call-another-container)