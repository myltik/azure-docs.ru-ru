---
title: "Создание приложения-контейнера Azure Service Fabric в Linux | Документация Майкрософт"
description: "В этом кратком руководстве вы создадите свое первое приложение-контейнер Linux в Azure Service Fabric.  Создание образа Docker с приложением, отправка образа в реестр контейнеров, сборка и развертывание приложения-контейнера Service Fabric."
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: f568bdf6ce40ff2d437f3566b66f6dd1478a74fa
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Краткое руководство. Развертывание приложения-контейнера Azure Service Fabric на платформе Linux в Azure
Azure Service Fabric — это платформа распределенных систем для развертывания масштабируемых надежных микрослужб и контейнеров и управления ими. 

В этом руководстве объясняется, как развертывать контейнеры Linux в кластер Service Fabric. Изучив это руководство, вы создадите приложение для голосования с веб-интерфейсом Python и серверной частью Redis, которое работает в кластере Service Fabric. 

![quickstartpic][quickstartpic]

Из этого краткого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Развертывание контейнеров в кластере Service Fabric под управлением Azure Linux
> * Масштабирование и отработка отказа контейнеров в Service Fabric

## <a name="prerequisite"></a>Предварительные требования
1. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

2. Если вы решили установить и использовать интерфейс командной строки (CLI), вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду az --version. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="get-application-package"></a>Получение пакета приложения
Чтобы развернуть контейнеры в кластер Service Fabric, вам понадобится набор файлов манифеста (определение приложения), которые описывают отдельные контейнеры и приложение.

В Cloud Shell используйте git, чтобы клонировать копию определения приложения.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```
## <a name="deploy-the-application-to-azure"></a>Развертывание приложения в Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Настройка кластера Azure Service Fabric
Чтобы развернуть приложение в кластере Azure, создайте собственный кластер.

Кластеры сообщества — это бесплатные временные кластеры Service Fabric, размещенные в Azure. Их запускает команда Service Fabric. Любой пользователь может развертывать приложения в этих кластерах и изучать платформу. Чтобы получить доступ к кластеру сообщества, следуйте инструкциям в [этом разделе](http://aka.ms/tryservicefabric). 

Чтобы выполнять операции управления на безопасном общедоступном кластере, можно использовать Service Fabric Explorer, CLI или Powershell. Чтобы использовать Service Fabric Explorer, необходимо скачать с веб-сайта общедоступного кластера PFX-файл и импортировать сертификат в хранилище сертификатов (Windows или Mac) или сам браузер (Ubuntu). Пароль для самозаверяющих сертификатов из общедоступного кластера стороны отсутствует. 

Для выполнения операций управления с помощью Powershell или CLI, требуется PFX-файл (Powershell) или PEM-файл (CLI). Чтобы преобразовать PFX-файл в PEM-файл, используйте следующую команду:  

```bash
openssl pkcs12 -in party-cluster-1277863181-client-cert.pfx -out party-cluster-1277863181-client-cert.pem -nodes -passin pass:
```

См. дополнительные сведения о [создании кластера Service Fabric в Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

> [!Note]
> Служба веб-интерфейса ожидает передачи данных через порт 80 для входящего трафика. Убедитесь, что порт открыт в кластере. При использовании кластера сообщества этот порт открыт.
>

### <a name="install-service-fabric-command-line-interface-and-connect-to-your-cluster"></a>Установка интерфейса командной строки Service Fabric и подключение к кластеру

Подключитесь к кластеру Service Fabric в Azure с помощью Azure CLI. Конечная точка — это конечная точка управления кластера. Например: `https://linh1x87d1d.westus.cloudapp.azure.com:19080`.

```bash
sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
```

### <a name="deploy-the-service-fabric-application"></a>Развертывание приложения Service Fabric 
Приложения-контейнеры Service Fabric можно развернуть с помощью описанного пакета приложений Service Fabric или Docker Compose. 

#### <a name="deploy-using-service-fabric-application-package"></a>Развертывание с помощью пакета приложений Service Fabric
С помощью скрипта установки скопируйте определения приложения для голосования в кластер, зарегистрируйте тип приложения и создайте экземпляр приложения.

```bash
./install.sh
```

#### <a name="deploy-the-application-using-docker-compose"></a>Развертывание приложения с помощью Docker Compose
Разверните и установите приложение в кластере Service Fabric с помощью Docker Compose, выполнив следующую команду.
```bash
sfctl compose create --deployment-name TestApp --file-path docker-compose.yml
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

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие действия:
> [!div class="checklist"]
> * Развертывание приложения-контейнера Linux в Azure
> * Отработка отказа контейнера в кластере Service Fabric
> * Масштабирование контейнера в кластере Service Fabric

* Дополнительные сведения о запуске [контейнеров в Service Fabric](service-fabric-containers-overview.md).
* Дополнительные сведения о [жизненном цикле приложения](service-fabric-application-lifecycle.md) Service Fabric.
* См. [примеры кода для контейнера Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) в репозитории GitHub.

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
