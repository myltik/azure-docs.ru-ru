---
title: "Развертывание приложения Azure Service Fabric в кластер сообщества | Документы Майкрософт"
description: "Сведения о развертывания приложения в кластер сообщества."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 5766ef2097b0da295d42e7c5909efc524049f418
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-application-to-a-party-cluster-in-azure"></a>Развертывание приложения в кластер сообщества в Azure
Это руководство из цикла. В нем показано, как развернуть приложение Azure Service Fabric в кластер сообщества в Azure.

Во втором руководстве из цикла вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * Развертывание приложения на удаленный кластер с помощью Visual Studio
> * Удаление приложения из кластера с помощью Service Fabric Explorer

Из этого цикла руководств вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * [Создание приложения .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md).
> * Развертывание приложения в удаленном кластере.
> * [Настройка непрерывной интеграции и непрерывного развертывания с помощью Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).
> * [Настройка мониторинга и диагностики приложения](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим руководством выполните следующие действия:
- Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Установите Visual Studio 2017](https://www.visualstudio.com/), а также рабочие нагрузки **разработка Azure** и **ASP.NET и веб-разработка**.
- [Установка пакета SDK для Service Fabric](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Скачивание примера приложения для голосования
Если вы не создавали пример приложения для голосования [в первой части этой серии руководств](service-fabric-tutorial-create-dotnet-app.md), вы можете скачать его. В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-a-party-cluster"></a>Настройка кластера сообщества
Кластеры сообщества — это бесплатные кластеры Service Fabric, которые доступны в течение ограниченного времени. Эти кластеры размещены в Azure и поддерживаются командой Service Fabric. Любой пользователь может развертывать приложения на этих кластерах и знакомиться с платформой. Бесплатно!

Чтобы получить доступ к кластеру сообщества, зайдите на сайт http://aka.ms/tryservicefabric и следуйте инструкциям. Для получения доступа к кластеру сообщества потребуется учетная запись Facebook или GitHub.

При желании можно использовать собственный кластер вместо кластера сообщества.  Веб-интерфейс ASP.NET Core использует обратный прокси-сервер для взаимодействия с серверной частью службы с отслеживанием состояния.  Обратный прокси-сервер включен по умолчанию для кластеров сообщества и кластера локальной разработки.  При развертывании примера приложения для голосования в собственном кластере вам необходимо [включить в кластере обратный прокси-сервер](service-fabric-reverseproxy.md#setup-and-configuration).

> [!NOTE]
> Кластеры сообщества не защищены, поэтому все ваши приложения и данные, размещенные на этих кластерах, могут быть доступны для других пользователей. Не разворачивайте приложения, которые не должны увидеть другие пользователи. Обязательно прочтите условия использования, чтобы получить все необходимые сведения.

## <a name="configure-the-listening-port"></a>Настройка порта прослушивания
Когда создается интерфейсная служба VotingWeb, Visual Studio случайным образом выбирает порт для ее прослушивания.  Так как служба VotingWeb выступает в роли клиентской части этого приложения и принимает внешний трафик, мы привяжем эту службу к фиксированному и хорошо известному порту. В обозревателе решений откройте файл *VotingWeb/PackageRoot/ServiceManifest.xml*.  В разделе **Ресурсы** найдите ресурс **Конечная точка** и задайте для **порта** значение 80.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="80" />
    </Endpoints>
  </Resources>
```

Обновите значение свойства "URL-адрес приложения" в проекте Voting, чтобы в веб-браузере открывался правильный порт при отладке с помощью клавиши F5.  В обозревателе решений выберите проект **Voting** и обновите свойство **URL-адрес приложения**.

![URL-адрес приложения](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-url.png)

## <a name="deploy-the-app-to-the-azure"></a>Развертывание приложения в Azure
Теперь, когда приложение готово, можно развернуть его в кластер сообщества прямо из Visual Studio.

1. Щелкните правой кнопкой мыши **Voting** в обозревателе решений и выберите **Опубликовать**.

    ![Диалоговое окно "Опубликовать"](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

2. Укажите конечную точку подключения кластера сообщества в поле **Конечная точка подключения** и нажмите кнопку **Опубликовать**.

    После завершения публикации вы сможете отправить запрос к приложению через браузер.

3. Откройте любой браузер и введите адрес кластера (конечную точку подключения без указания порта — например, win1kw5649s.westus.cloudapp.azure.com).

    Вы должны увидеть такой же результат, как и при локальном запуске приложения.

    ![Ответ API из кластера](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="remove-the-application-from-a-cluster-using-service-fabric-explorer"></a>Удаление приложения из кластера с помощью Service Fabric Explorer
Service Fabric Explorer — графический пользовательский интерфейс для просмотра приложений в кластере Service Fabric и управления ими.

Чтобы удалить приложение из кластера сообщества, сделайте следующее:

1. Перейдите в Service Fabric Explorer, используя соответствующую ссылку на странице регистрации для кластера сообщества. Например, http://win1kw5649s.westus.cloudapp.azure.com:19080/Explorer/index.html.

2. В Service Fabric Explorer перейдите к узлу **fabric://Voting** в дереве слева.

3. Нажмите кнопку **Действия** в области **Основные действия** справа и выберите **Удалить приложение**. Подтвердите удаление экземпляра приложения. После этого экземпляр нашего приложения, запущенный в кластере, будет удален.

![Удаление приложения в Service Fabric Explorer](./media/service-fabric-tutorial-deploy-app-to-party-cluster/delete-application.png)

## <a name="remove-the-application-type-from-a-cluster-using-service-fabric-explorer"></a>Удаление типа приложения из кластера с помощью Service Fabric Explorer
Приложения в кластере Service Fabric развертываются по типам приложений. Это позволяет иметь несколько экземпляров и версий приложения в одном и том же кластере. После удаления запущенного экземпляра приложения также можно удалить тип, чтобы завершить очистку.

Дополнительные сведения о моделировании приложений в Service Fabric см. в разделе [Моделирование приложений в Service Fabric](service-fabric-application-model.md).

1. Перейдите к узлу **VotingType** в дереве.

2. Нажмите кнопку **Действия** в области **Основные действия** справа и выберите **Отменить подготовку типа**. Подтвердите отмену подготовки типа приложения.

![Отмена подготовки типа приложения в Service Fabric Explorer](./media/service-fabric-tutorial-deploy-app-to-party-cluster/unprovision-type.png)

Это заключительный шаг учебника.

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Развертывание приложения на удаленный кластер с помощью Visual Studio
> * Удаление приложения из кластера с помощью Service Fabric Explorer

Перейдите к следующему руководству:
> [!div class="nextstepaction"]
> [Настройка непрерывной интеграции с Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)