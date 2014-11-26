<properties title="Использование расширения виртуальных машин Docker на портале Azure" pageTitle="Использование расширения виртуальных машин Docker для Linux на Azure" description="Описание механизма Docker и расширений виртуальных машин Azure, а также программного создания виртуальных машин в Azure как узлов Docker с помощью интерфейса командной строки azure-cli." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill" />
<!--The next line, with one pound sign at the beginning, is the page title-->

# Использование расширения виртуальных машин Docker на портале Azure

[Docker][Docker] — один из самых популярных подходов к виртуализации, использующий [контейнеры Linux][контейнеры Linux] вместо виртуальных машин как способ изоляции данных и вычислений при использовании общих ресурсов. Можно использовать расширение Docker в [агенте Linux для Azure][агенте Linux для Azure] для создания виртуальной машины, в которой будет размещено любое количество контейнеров с приложениями Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

В этом разделе

-   [Создание новой виртуальной машины в коллекции образов][Создание новой виртуальной машины в коллекции образов]
-   [Создание сертификатов Docker][Создание сертификатов Docker]
-   [Добавление расширения виртуальных машин Docker][Добавление расширения виртуальных машин Docker]
-   [Тестирование клиента Docker и узла Docker в Azure][Тестирование клиента Docker и узла Docker в Azure]
-   [Дальнейшие действия][Дальнейшие действия]

> [WACOM.NOTE] В этом разделе описывается создание виртуальной машины Docker на портале Azure. Информацию о создании виртуальной машины Docker с помощью командной строки см. в разделе [Использование расширения виртуальных машин Docker в межплатформенном интерфейсе Azure (xplat-cli)][Использование расширения виртуальных машин Docker в межплатформенном интерфейсе Azure (xplat-cli)]. Обзорное обсуждение контейнеров и их преимуществ см. на [доске по Docker][доске по Docker].

## <span id="createvm"></span>Создание новой виртуальной машины в коллекции образов</a>

На первом этапе необходима виртуальная машина Azure на основе образа Linux, который поддерживает расширение виртуальных машин Docker. Пример образа сервера из коллекции образов — Ubuntu 14.04 LTS, пример образа клиента — Ubuntu 14.04. На портале в нижнем левом углу нажмите кнопку **+ Создать**, чтобы создать новый экземпляр виртуальной машины, и выберите образ Ubuntu 14.04 LTS из доступных вариантов или из полной коллекции образов, как показано ниже.

> [WACOM.NOTE] В настоящее время расширение виртуальных машин Docker поддерживается только в образах Ubuntu 14.04 LTS, созданных позже июля 2014 г.

![Создание нового образа Ubuntu][Создание нового образа Ubuntu]

## <a id'dockercerts'>Создание сертификатов Docker</a>

После создания виртуальной машины убедитесь, что Docker установлен на клиентском компьютере. (Подробную информацию см. в разделе [Инструкции по установке Docker][Инструкции по установке Docker].)

Создайте файлы сертификата и ключа для соединения Docker (см. раздел [Работа Docker с https][Работа Docker с https]) и поместите их в каталог **`~/.docker`** на клиентском компьютере.

> [WACOM.NOTE] В настоящее время для расширения виртуальных машин Docker на портале необходимы учетные данные с кодировкой base64.

В командной строке используйте **`base64`** или другое предпочитаемое средство кодирования для создания разделов с кодировкой base64. Пример для простого набора файлов сертификатов и ключей:

     ~/.docker$ l
     ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
     ~/.docker$ base64 ca.pem > ca64.pem
     ~/.docker$ base64 server-cert.pem > server-cert64.pem
     ~/.docker$ base64 server-key.pem > server-key64.pem
     ~/.docker$ l
     ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
     ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem

## <a id'adddockerextension'>Добавление расширения виртуальных машин Docker</a>

Для добавления расширения виртуальных машин Docker найдите созданный экземпляр виртуальной машины и щелкните **Расширения**, чтобы открыть расширения виртуальных машин (см. ниже).

![][0]

### Добавление расширения

Щелкните **+ Добавить**, чтобы просмотреть расширения, которые можно добавить к этой виртуальной машине.

![][1]

### Выбор расширения виртуальных машин Docker

Выберите расширение виртуальных машин Docker, которое обеспечит описание Docker и важные ссылки, и нажмите кнопку **Создать** внизу, чтобы начать процедуру установки.

![][2]

![][3]

### Добавление файлов сертификатов и ключей.

В полях формы введите версии сертификата ЦС, сертификата сервера и ключа сервера в кодировке base64, как показано на следующем графике.

![][4]

> [WACOM.NOTE] Обратите внимание, что значение 4243 заполнено по умолчанию (как показано на предыдущем рисунке). Здесь можно указать любую конечную точку, однако на следующем этапе потребуется открыть соответствующую конечную точку. Изменяя значение по умолчанию, на следующем этапе откройте соответствующую конечную точку.

## Добавление конечной точки соединения Docker

При просмотре виртуальной машины в созданной группе ресурсов щелкните **Конечные точки**, чтобы просмотреть конечные точки на виртуальной машине.

![][5]

Щелкните **+ Добавить** для добавления еще одной конечной точки и, в случае по умолчанию, введите имя конечной точки (в этом примере **docker**) и значение 4243 для частного и общедоступного портов. Не изменяйте значение протокола **TCP** и щелкните **OK** для создания конечной точки.

![][6]

## <span id="testclientandserver"></span>Тестирование клиента Docker и узла Docker в Azure</a>

Нажмите и скопируйте имя домена виртуальной машины и в командной строке клиентского компьютера введите `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:4243 info` (замените *dockerextension* дочерним доменом виртуальной машины).

Результат должен выглядеть следующим образом:

    $ docker --tls -H tcp://dockerextension.cloudapp.net:4243 info
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

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Дальнейшие действия

Переходите к [Руководству пользователя Docker][Руководству пользователя Docker] и использованию виртуальной машины Docker. Для быстрого и многократного создания виртуальных машин Docker на Azure см. раздел [Использование расширения виртуальных машин Docker в межплатформенном интерфейсе Azure (xplat-cli)][Использование расширения виртуальных машин Docker в межплатформенном интерфейсе Azure (xplat-cli)]

<!--Anchors-->\r\n<!--Image references-->\r\n<!--Link references-->

  [Docker]: https://www.docker.com/
  [контейнеры Linux]: http://en.wikipedia.org/wiki/LXC
  [агенте Linux для Azure]: ../virtual-machines-linux-agent-user-guide/
  [Создание новой виртуальной машины в коллекции образов]: #createvm
  [Создание сертификатов Docker]: #dockercerts
  [Добавление расширения виртуальных машин Docker]: #adddockerextension
  [Тестирование клиента Docker и узла Docker в Azure]: #testclientandserver
  [Дальнейшие действия]: #next-steps
  [Использование расширения виртуальных машин Docker в межплатформенном интерфейсе Azure (xplat-cli)]: http://azure.microsoft.com/ru-ru/documentation/articles/virtual-machines-docker-with-xplat-cli/
  [доске по Docker]: http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard
  [Создание нового образа Ubuntu]: ./media/virtual-machines-docker-with-portal/ChooseUbuntu.png
  [Инструкции по установке Docker]: https://docs.docker.com/installation/#installation
  [Работа Docker с https]: http://docs.docker.com/articles/https/
  [0]: ./media/virtual-machines-docker-with-portal/ClickExtensions.png
  [1]: ./media/virtual-machines-docker-with-portal/ClickAdd.png
  [2]: ./media/virtual-machines-docker-with-portal/ChooseDockerExtension.png
  [3]: ./media/virtual-machines-docker-with-portal/CreateButtonFocus.png
  [4]: ./media/virtual-machines-docker-with-portal/AddExtensionFormFilled.png
  [5]: ./media/virtual-machines-docker-with-portal/AddingEndpoint.png
  [6]: ./media/virtual-machines-docker-with-portal/AddEndpointFormFilledOut.png
  [Руководству пользователя Docker]: https://docs.docker.com/userguide/
