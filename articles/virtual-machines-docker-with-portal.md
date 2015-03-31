<properties 
	pageTitle="Использование расширения виртуальных машин Docker для Linux на Azure" 
	description="Описание механизма Docker и расширений виртуальных машин Azure, а также программного создания виртуальных машин в Azure как узлов Docker с помощью интерфейса командной строки azure-cli." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services" 
	ms.date="02/11/2015" 
	ms.author="rasquill"/>
<!--The next line, with one pound sign at the beginning, is the page title--> 
# Использование расширения виртуальных машин Docker на портале Azure

[Docker](https://www.docker.com/) - один из самых популярных подходов к виртуализации, использующий [контейнеры Linux](http://en.wikipedia.org/wiki/LXC) вместо виртуальных машин как способ изоляции данных и вычислений при использовании общих ресурсов. Можно использовать расширение Docker для виртуальных машин, управляемое [агентом Linux для Azure], чтобы создать виртуальную машину Docker, в которой будет размещено любое количество контейнеров с приложениями в Azure. 

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->
В этом разделе

+ [Создание новой виртуальной машины в коллекции образов]
+ [Создание сертификатов Docker]
+ [Добавление расширения виртуальных машин Docker]
+ [Тестирование клиента Docker и узла Docker в Azure]
+ [Дальнейшие действия]

> [AZURE.NOTE] В этом разделе описывается создание виртуальной машины Docker на портале Azure. Информацию о создании виртуальной машины Docker с помощью командной строки см. в разделе [Использование расширения виртуальных машин Docker в межплатформенном интерфейсе Azure (xplat-cli)]. Обзорное обсуждение контейнеров и их преимуществ см. на [доске по Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard). 

## <a id='createvm'>Создание новой виртуальной машины в коллекции образов</a>
На первом этапе необходима виртуальная машина Azure на основе образа Linux, который поддерживает расширение виртуальных машин Docker. Пример образа сервера из коллекции образов - Ubuntu 14.04 LTS, пример образа клиента - Ubuntu 14.04. На портале в нижнем левом углу нажмите кнопку **+ Создать**, чтобы создать новый экземпляр виртуальной машины, и выберите образ Ubuntu 14.04 LTS из доступных вариантов или из полной коллекции образов, как показано ниже. 

> [AZURE.NOTE] В настоящее время расширение виртуальных машин Docker поддерживается только в образах Ubuntu 14.04 LTS, созданных позже июля 2014 г.

![Create a new Ubuntu Image](./media/virtual-machines-docker-with-portal/ChooseUbuntu.png)

## <a id'dockercerts'>Создание сертификатов Docker</a>

После создания виртуальной машины убедитесь, что Docker установлен на клиентском компьютере. (Подробную информацию см. в разделе [Указания по установке Docker](https://docs.docker.com/installation/#installation).) 

Создайте файлы сертификата и ключа для соединения Docker (см. раздел [Работа Docker с https]) и поместите их в каталог **`~/.docker`** на клиентском компьютере. 

> [AZURE.NOTE] В настоящее время для расширения виртуальных машин Docker на портале необходимы учетные данные с кодировкой base64.

В командной строке используйте **`base64`** или другое предпочитаемое средство кодирования для создания разделов с кодировкой base64. Пример для простого набора файлов сертификатов и ключей:

```
 ~/.docker$ l
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ l
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a id'adddockerextension'>Добавление расширения виртуальных машин Docker</a>
Для добавления расширения виртуальных машин Docker найдите созданный экземпляр виртуальной машины и щелкните **Расширения**, чтобы открыть расширения виртуальных машин, как показано ниже.
> [AZURE.NOTE] Эта функциональность поддерживается только на портале предварительной версии. https://portal.azure.com/

![](./media/virtual-machines-docker-with-portal/ClickExtensions.png)
### Добавление расширения
Щелкните **+ Добавить**, чтобы просмотреть расширения, которые можно добавить к этой виртуальной машине. 

![](./media/virtual-machines-docker-with-portal/ClickAdd.png)
### Выбор расширения виртуальных машин Docker
Выберите расширение виртуальных машин Docker, которое обеспечит описание Docker и важные ссылки, и нажмите кнопку **Создать** внизу, чтобы начать процедуру установки.

![](./media/virtual-machines-docker-with-portal/ChooseDockerExtension.png)

![](./media/virtual-machines-docker-with-portal/CreateButtonFocus.png)
### Добавление файлов сертификатов и ключей.

В полях формы введите версии сертификата ЦС, сертификата сервера и ключа сервера в кодировке base64, как показано на следующем графике.

![](./media/virtual-machines-docker-with-portal/AddExtensionFormFilled.png)

> [AZURE.NOTE] Обратите внимание, что значение 4243 заполнено по умолчанию (как показано на предыдущем рисунке). Здесь можно указать любую конечную точку, однако на следующем этапе потребуется открыть соответствующую конечную точку. Изменяя значение по умолчанию, на следующем этапе откройте соответствующую конечную точку.

## Добавление конечной точки соединения Docker
При просмотре виртуальной машины в созданной группе ресурсов щелкните **Конечные точки**, чтобы просмотреть конечные точки в виртуальной машине.

![](./media/virtual-machines-docker-with-portal/AddingEndpoint.png)

Щелкните **+ Добавить**, чтобы добавить еще одну конечную точку, и в случае по умолчанию введите имя конечной точки (в этом примере **docker**) и значение 4243 для частного и общего портов. Не изменяйте значение протокола **TCP** и нажмите кнопку **OK**, чтобы создать конечную точку.

![](./media/virtual-machines-docker-with-portal/AddEndpointFormFilledOut.png)


## <a id='testclientandserver'>Тестирование клиента Docker и узла Docker в Azure</a>
Нажмите и скопируйте имя домена виртуальной машины и в командной строке клиентского компьютера введите `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:4243 info` (замените *dockerextension* поддоменом своей виртуальной машины). 

Результат должен выглядеть следующим образом:

```
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
```

После завершения описанных выше действий вы получаете полнофункциональный узел Docker, выполняемый в виртуальной машине Azure и настроенный на удаленное подключение с других клиентов.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия

Вы готовы перейти к [руководству пользователя Docker] и использовать виртуальную машину Docker. Если нужно автоматизировать создание узлов Docker на виртуальных машинах Azure через интерфейс командной строки, см. раздел [Использование расширения виртуальных машин Docker в межплатформенном интерфейсе Azure (xplat-cli)]

<!--Anchors-->
[Создание новой виртуальной машины в коллекции образов]: #createvm
[Создание сертификатов Docker]: #dockercerts
[Добавление расширения виртуальных машин Docker]: #adddockerextension
[Тестирование клиента Docker и узла Docker в Azure]: #testclientandserver
[Дальнейшие действия]: #next-steps

<!--Image references-->
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Использование расширения виртуальных машин Docker в межплатформенном интерфейсе Azure (xplat-cli)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Агент Linux для Azure]: ../virtual-machines-linux-agent-user-guide/
[Ссылка 3 на другой раздел документации azure.microsoft.com]: ../storage-whatis-account/

[Запуск Docker с использованием https]: http://docs.docker.com/articles/https/
[Руководство пользователя Docker]: https://docs.docker.com/userguide/

<!--HONumber=47-->
