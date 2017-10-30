---
title: "Создание приложения-контейнера Azure Service Fabric в Linux | Документация Майкрософт"
description: "Создание первого приложения-контейнера Linux в Azure Service Fabric.  Создание образа Docker с приложением, отправка образа в реестр контейнеров, сборка и развертывание приложения-контейнера Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.openlocfilehash: 7c1ac13d50180909bbe55b01f47721387d1195d7
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2017
---
# <a name="deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Развертывание приложения-контейнера Azure Service Fabric для Linux в Azure
Azure Service Fabric — это платформа распределенных систем для развертывания масштабируемых надежных микрослужб и контейнеров и управления ими. 

В этом руководстве объясняется, как развертывать контейнеры Linux в кластер Service Fabric. Изучив это руководство, вы создадите приложение для голосования с веб-интерфейсом Python и серверной частью Redis, которое работает в кластере Service Fabric. 

![quickstartpic][quickstartpic]

Из этого краткого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Развертывание контейнеров в кластере Service Fabric под управлением Azure Linux
> * Масштабирование и отработка отказа контейнеров в Service Fabric

## <a name="prerequisite"></a>Предварительные требования
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/en-us/free/), прежде чем начинать работу.
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки (CLI), вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду az --version. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

## <a name="get-application-package"></a>Получение пакета приложения
Чтобы развернуть контейнеры в кластер Service Fabric, вам понадобится набор файлов манифеста (определение приложения), которые описывают отдельные контейнеры и приложение.

В Cloud Shell используйте git, чтобы клонировать копию определения приложения.

```azurecli-interactive
git clone https://github.com/Azure-Samples/service-fabric-dotnet-containers.git

cd service-fabric-dotnet-containers/Linux/container-tutorial/Voting
```

## <a name="deploy-the-containers-to-a-service-fabric-cluster-in-azure"></a>Развертывание контейнеров в кластер Service Fabric в Azure
Для развертывания приложения в кластере Azure можно использовать собственный кластер или кластер сообщества.

> [!Note]
> Приложения должны быть развернуты в кластере Azure, а не в кластере Service Fabric на локальном компьютере для разработки. 
>

Кластеры сообщества — это бесплатные временные кластеры Service Fabric, размещенные в Azure. Их обслуживает команда Service Fabric. Любой пользователь может развертывать приложения в этих кластерах и изучать платформу. Чтобы получить доступ к кластеру сообщества, следуйте инструкциям на [этом сайте](http://aka.ms/tryservicefabric). 

Сведения о создании собственного кластера см. в разделе [Создание первого кластера Service Fabric в Azure](service-fabric-get-started-azure-cluster.md).

> [!Note]
> Служба веб-интерфейса ожидает передачи данных через порт 80 для входящего трафика. Убедитесь, что порт открыт в кластере. При использовании кластера сообщества этот порт открыт.
>

### <a name="deploy-the-application-manifests"></a>Развертывание манифестов приложения 
Установка [интерфейса командной строки Service Fabric (sfctl)](service-fabric-cli.md) в среде CLI

```azurecli-interactive
pip3 install --user sfctl 
export PATH=$PATH:~/.local/bin
```
Подключитесь к кластеру Service Fabric в Azure с помощью Azure CLI. Конечная точка — это конечная точка управления кластера. Например: `http://linh1x87d1d.westus.cloudapp.azure.com:19080`.

```azurecli-interactive
sfctl cluster select --endpoint http://linh1x87d1d.westus.cloudapp.azure.com:19080
```

С помощью скрипта установки скопируйте определения приложения для голосования в кластер, зарегистрируйте тип приложения и создайте экземпляр приложения.

```azurecli-interactive
./install.sh
```

Откройте браузер и перейдите к обозревателю Service Fabric Explorer по адресу http://\<URL-адрес_моего_кластера_Azure_Service_Fabric>:19080/Explorer. Например: `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. Разверните узел приложения, чтобы увидеть запись для типа приложения голосования и созданный вами экземпляр.

![Service Fabric Explorer][sfx]

Подключитесь к запущенному контейнеру.  Откройте в веб-браузере URL-адрес своего кластера, например: `http://linh1x87d1d.westus.cloudapp.azure.com:80`. Вы должны увидеть приложение голосования в браузере.

![quickstartpic][quickstartpic]

## <a name="fail-over-a-container-in-a-cluster"></a>Отработка отказа контейнера в кластере
Платформа Service Fabric обеспечивает автоматический перенос экземпляров контейнера на другие узлы в кластере в случае сбоя. Можно также вручную перенести контейнеры с одного узла на другие узлы в кластере. Службы можно масштабировать разными способами. В этом примере мы используем Service Fabric Explorer.

Чтобы выполнить отработку отказа внешнего контейнера, сделайте следующее:

1. Откройте Service Fabric Explorer в своем кластере (например, по ссылке `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`).
2. Щелкните узел **fabric:/Voting/azurevotefront** в представлении в виде дерева и разверните узел раздела (он отображается в виде идентификатора GUID). Найдите имя узла в представлении в виде дерева с узлами, на которых сейчас выполняется контейнер. Пример: `_nodetype_4`.
3. Разверните узел **Узлы** в представлении в виде дерева. Щелкните многоточие рядом с узлом, на котором выполняется контейнер.
4. Выберите **Перезапустить**, чтобы перезапустить этот узел, и подтвердите операцию перезапуска. Перезапуск приведет к отработке отказа контейнера на другой узел в кластере.

![sfxquickstartshownodetype][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Масштабирование приложений и служб в кластере
Службы Service Fabric можно легко масштабировать в пределах кластера в соответствии с изменениями нагрузки на службы. Масштабирование службы осуществляется путем изменения числа экземпляров, запущенных в кластере.

Для масштабирования службы веб-интерфейса выполните следующие действия:

1. Откройте Service Fabric Explorer в своем кластере (например, по ссылке `http://linh1x87d1d.westus.cloudapp.azure.com:19080`).
2. Щелкните многоточие рядом с узлом **fabric:/Voting/azurevotefront** в представлении в виде дерева и выберите **Масштабировать службу**.

    ![containersquickstartscale][containersquickstartscale]

  Теперь вы можете изменить количество экземпляров службы веб-интерфейса.

3. Измените количество на **2** и нажмите кнопку **Масштабировать службу**.
4. Щелкните узел **fabric:/Voting/azurevotefront** в представлении в виде дерева и разверните узел раздела (он отображается в виде идентификатора GUID).

    ![containersquickstartscaledone][containersquickstartscaledone]

    Теперь вы видите два экземпляра службы. В представлении в виде дерева можно просмотреть, на каких узлах выполняются эти экземпляры.

С помощью этой простой задачи управления мы удвоили количество ресурсов для обработки пользовательской нагрузки для службы веб-интерфейса. Важно понимать, что для надежной работы службы не требуется запускать несколько экземпляров службы. При сбое в работе службы Service Fabric запускает новый экземпляр службы в кластере.

## <a name="clean-up"></a>Очистка
Чтобы удалить экземпляр приложения из кластера и отменить регистрацию типа приложения, используйте скрипт удаления, предоставленный в шаблоне. Очистка экземпляра займет некоторое время. Поэтому не следует запускать команду install'sh сразу же после выполнения скрипта удаления. 

```bash
./uninstall.sh
```

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства вы узнали, как выполнить следующие действия:
> [!div class="checklist"]
> * Развертывание приложения-контейнера Linux в Azure
> * Отработка отказа контейнера в кластере Service Fabric
> * Масштабирование контейнера в кластере Service Fabric

* Дополнительные сведения о запуске [контейнеров в Service Fabric](service-fabric-containers-overview.md).
* Дополнительные сведения о [жизненном цикле приложения](service-fabric-application-lifecycle.md) Service Fabric.
* См. [примеры кода для контейнера Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) в репозитории GitHub.

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
