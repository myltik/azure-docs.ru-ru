---
title: "Использование расширения виртуальных машин Docker для Linux | Документация Майкрософт"
description: "Описывает расширения виртуальных машин Azure и Docker, а также создание виртуальных машин Azure, являющихся узлами Docker, с помощью Azure CLI в классической модели развертывания."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 19cf64e8-f92c-43ad-a120-8976cd9102ac
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/27/2016
ms.author: rasquill
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 932744208d9d53c87e31dcdf9e34539750be4bdb
ms.contentlocale: ru-ru
ms.lasthandoff: 04/03/2017

---
# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>Использование расширения виртуальных машин Docker на классическом портале Azure
> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../../../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

[Docker](https://www.docker.com/) — один из самых популярных подходов к виртуализации, использующий [контейнеры Linux](http://en.wikipedia.org/wiki/LXC) вместо виртуальных машин как способ изоляции данных и вычислений при использовании общих ресурсов. Вы можете использовать расширение виртуальных машин Docker, управляемое [агентом Linux для Azure] , чтобы создать виртуальную машину Docker, в которой будет размещено любое количество контейнеров с приложениями в Azure.

> [!NOTE]
> В этом разделе описывается создание виртуальной машины Docker на классическом портале Azure. Сведения о создании виртуальной машины Docker с помощью командной строки см. в статье [Использование расширения виртуальных машин Docker в интерфейсе командной строки Azure (CLI Azure)]. Обзорное обсуждение контейнеров и их преимуществ см. на [доске по Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).
> 
> 

## <a name="create-a-new-vm-from-the-image-gallery"></a>Создание новой виртуальной машины в коллекции образов
На первом этапе необходима виртуальная машина Azure на основе образа Linux, который поддерживает расширение виртуальных машин Docker. Пример образа сервера из коллекции образов — Ubuntu 14.04 LTS, пример образа клиента — Ubuntu 14.04. На портале в нижнем левом углу нажмите кнопку **+ Создать**, чтобы создать экземпляр виртуальной машины, и выберите образ Ubuntu 14.04 LTS из доступных вариантов или из полной коллекции образов, как показано ниже.

> [!NOTE]
> В настоящее время расширение виртуальных машин Docker поддерживается только в образах Ubuntu 14.04 LTS, созданных позже июля 2014 г.
> 
> 

![Создание нового образа Ubuntu](./media/portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Создание сертификатов Docker
После создания виртуальной машины убедитесь, что Docker установлен на клиентском компьютере. (Подробные инструкции по установке Docker см. по [этой ссылке](https://docs.docker.com/installation/#installation)).

Создайте файлы сертификата и ключа для соединения Docker (сведения о запуске Docker с использованием https см. по [этой ссылке]) и поместите их в каталог **`~/.docker`** на клиентском компьютере.

> [!NOTE]
> В настоящее время для расширения виртуальных машин Docker на портале необходимы учетные данные с кодировкой base64.
> 
> 

В командной строке используйте **`base64`** или другой предпочитаемый инструмент кодирования для создания разделов с кодировкой base64. Пример для простого набора файлов сертификатов и ключей:

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Добавление расширения виртуальных машин Docker
Для добавления расширения виртуальных машин Docker найдите созданный экземпляр виртуальной машины и щелкните **Расширения**, чтобы открыть расширения виртуальных машин (см. ниже).

> [!NOTE]
> Эта функциональность поддерживается только на портале предварительной версии: https://portal.azure.com/.
> 
> 

![](media/portal-use-docker/ClickExtensions.png)

### <a name="add-an-extension"></a>Добавление расширения
Щелкните **+ Добавить** , чтобы просмотреть расширения, которые можно добавить к этой виртуальной машине.

![](media/portal-use-docker/ClickAdd.png)

### <a name="select-the-docker-vm-extension"></a>Выбор расширения виртуальных машин Docker
Выберите расширение виртуальных машин Docker, которое обеспечит описание Docker и важные ссылки, и нажмите кнопку **Создать** внизу, чтобы начать процедуру установки.

![](media/portal-use-docker/ChooseDockerExtension.png)

![](media/portal-use-docker/CreateButtonFocus.png)

### <a name="add-your-certificate-and-key-files"></a>Добавление файлов сертификатов и ключей.
В полях формы введите версии сертификата ЦС, сертификата сервера и ключа сервера в кодировке base64, как показано на следующем графике.

![](media/portal-use-docker/AddExtensionFormFilled.png)

> [!NOTE]
> Обратите внимание, что значение 2376 заполнено по умолчанию (как показано на предыдущем рисунке). Здесь можно указать любую конечную точку, однако на следующем этапе потребуется открыть соответствующую конечную точку. Изменяя значение по умолчанию, на следующем этапе откройте соответствующую конечную точку.
> 
> 

## <a name="add-the-docker-communication-endpoint"></a>Добавление конечной точки соединения Docker
При просмотре созданной группы ресурсов выберите группу безопасности сети, связанную с вашей виртуальной машиной, и щелкните **Правила безопасности для входящего трафика** , чтобы просмотреть правила, как показано ниже.

![](media/portal-use-docker/AddingEndpoint.png)

Щелкните **+ Добавить**, чтобы добавить еще одно правило, и, в случае по умолчанию, введите имя конечной точки (в этом примере — **Docker**) и значение 2376 в поле "Диапазон конечных портов". Укажите протокол **TCP** и нажмите кнопку **OK**, чтобы создать правило.

![](media/portal-use-docker/AddEndpointFormFilledOut.png)

## <a name="test-your-docker-client-and-azure-docker-host"></a>Тестирование клиента Docker и узла Docker в Azure
Найдите и скопируйте имя домена виртуальной машины, затем в командной строке клиентского компьютера введите `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:2376 info` (замените *dockerextension* поддоменом виртуальной машины).

Результат должен выглядеть следующим образом:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

После завершения описанных выше действий вы получаете полнофункциональный узел Docker, выполняемый в виртуальной машине Azure и настроенный на удаленное подключение с других клиентов.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Дальнейшие действия
Переходите к [руководству пользователя Docker] и использованию виртуальной машины Docker. Если нужно автоматизировать создание узлов Docker на виртуальных машинах Azure через интерфейс командной строки, см. статью [Использование расширения виртуальных машин Docker в интерфейсе командной строки Azure (CLI Azure)].

<!--Anchors-->
[Create a new VM from the Image Gallery]:#createvm
[Create Docker Certificates]:#dockercerts
[Add the Docker VM Extension]:#adddockerextension
[Test Docker Client and Azure Docker Host]:#testclientandserver
[Next steps]:#next-steps

<!--Image references-->
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[6]:./media/markdown-template-for-new-articles/pretty49.png
[7]:./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Использование расширения виртуальных машин Docker в интерфейсе командной строки Azure (CLI Azure)]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[агентом Linux для Azure]:../agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md

[этой ссылке]:http://docs.docker.com/articles/https/
[руководству пользователя Docker]:https://docs.docker.com/userguide/

