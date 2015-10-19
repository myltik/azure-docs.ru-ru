<properties
	pageTitle="Быстрое использование Docker с образом виртуальной машины Ubuntu-Docker"
	description="Здесь описано использование Docker на сервере Ubuntu непосредственно из коллекции образов Azure, занимающее всего несколько минут"
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure"
	ms.date="10/04/2015"
	ms.author="rasquill"/>

# Как быстро приступить к работе с Docker в Azure Marketplace

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]В этой статье описывается процесс создания ресурсов с помощью классической модели развертывания.

Самый быстрый способ приступить к работе с [Docker] — перейти в Azure Marketplace и создать виртуальную машину с помощью шаблона образа **Docker on Ubuntu Server**, созданного [Canonical] при сотрудничестве с [MSOpenTech]. При этом создается виртуальная машина Ubuntu Server и автоматически устанавливается [расширение виртуальных машин Docker](virtual-machines-docker-vm-extension.md) вместе с **последней** версией ядра Docker, которая предварительно установлена и запущена в Azure.

Вы можете немедленно подключиться к виртуальной машине через SSH и начать работу с Docker напрямую, без выполнения каких-либо других действий.

> [AZURE.NOTE]Виртуальная машина, созданная с помощью шаблона Azure Marketplace, не имеет удаленного API Docker для управления удаленным клиентом Docker. Чтобы включить удаленное управление узлом Docker в этой виртуальной машине, см. [статью о запуске Docker с использованием HTTPS](https://docs.docker.com/articles/https/) или выполните действия, перечисленные в статье [Использование расширения виртуальных машин Docker на портале Azure](virtual-machines-docker-with-portal.md) или [Использование расширения виртуальных машин Docker в CLI Azure](virtual-machines-docker-with-xplat-cli-install.md). <!-- --> Если нужно автоматизировать виртуальную машину Docker Azure из Windows, можно [установить панель элементов Docker Toolbox](https://docs.docker.com/installation/windows/) или получить Docker.exe [из Chocolatey](https://chocolatey.org/packages/docker).

## Вход на портал

Эта часть не представляет никаких сложностей, если у вас есть учетная запись Azure. [Кроме того, вы можете легко получить такую учетную запись совершенно бесплатно](http://azure.microsoft.com/pricing/free-trial/)!

## Создание виртуальной машины с использованием образа Docker от Canonical и MSOpenTech

1. После входа в систему нажмите кнопку **Создать** в нижнем левом углу, чтобы создать образ виртуальной машины. Подходящий образ может сразу же отобразиться на баннере:

> ![Выбор образа Ubuntu Docker на баннере](./media/virtual-machines-docker-ubuntu-quickstart/CreateNewDockerBanner.png)

2. Если этого не происходит, найдите его в коллекции образов:

> ![Поиск изображения в коллекции образов](./media/virtual-machines-docker-ubuntu-quickstart/DockerOnUbuntuServerMSOpenTech.png)

3. Укажите имя пользователя и пароль для экземпляра или **PUB**-файл (в формате ssh-rsa), чтобы включить SSH с использованием сертификата. (На приведенном ниже изображении показан процесс задания имени пользователя и пароля.) Нажмите кнопку **Создать** в нижней части окна.

> ![Настройка экземпляра виртуальной машины](./media/virtual-machines-docker-ubuntu-quickstart/CreateVMDockerUbuntuPwd.png)

4. Дождитесь завершения выполнения, это не должно занять много времени.

> ![Запуск образа Docker на портале](./media/virtual-machines-docker-ubuntu-quickstart/DockerUbuntuRunning.png)

## Подключение через SSH и готовность к работе

Теперь начинается самое интересное. Вы можете немедленно подключиться к виртуальной машине с помощью SSH:

> ![Подключение по протоколу SSH](./media/virtual-machines-docker-ubuntu-quickstart/SSHToDockerUbuntu.png)

и начать выполнять команды Docker, не забывая о том, что стандартная конфигурация на этой виртуальной машине Azure требует наличия **`sudo`**:

> ![Извлечение образов](./media/virtual-machines-docker-ubuntu-quickstart/DockerPullSmallImages.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия

Вам остается лишь начать пользоваться [Docker]!

<!--Anchors-->
[Log on to the Portal]: #logon
[Create a VM with the Docker Image from Canonical and MSOpenTech]: #createvm
[Connect with SSH and Have Fun]: #havingfun
[Next steps]: #next-steps


[Docker]: https://www.docker.com/
[BusyBox]: http://en.wikipedia.org/wiki/BusyBox
[Docker scratch image]: https://docs.docker.com/articles/baseimages/#creating-a-simple-base-image-using-scratch
[Canonical]: http://www.canonical.com/
[MSOpenTech]: http://msopentech.com/
 

<!---HONumber=Oct15_HO2-->