<properties
   pageTitle="Развертывание 3-узлового кластера Deis | Microsoft Azure"
   description="В этой статье описывается создание 3-узлового кластера Deis в Azure с помощью шаблона диспетчера ресурсов Azure."
   services="virtual-machines"
   documentationCenter=""
   authors="HaishiBai"
   manager="larar"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/24/2015"
   ms.author="hbai"/>

# Развертывание 3-узлового кластера Deis

В этой статье пошагово описана подготовка кластера [Deis](http://deis.io/) в Azure. Она охватывает все действия, от создания необходимых сертификатов до развертывания и масштабирования примера приложения **Go** на новом подготовленном кластере.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]В этой статье описывается процесс создания ресурса с помощью модели развертывания на основе диспетчера ресурсов.

На следующей схеме показана архитектура развернутой системы. Системный администратор управляет кластером с помощью инструментов Deis, таких как **deis** и **deisctl**. Подключения устанавливаются через посредством подсистемы балансировки нагрузки Azure, переадресовывающей подключения одному из узлов, участвующих в кластере. Кроме того, доступ клиентов к развернутым приложениям также осуществляется через подсистему балансировки нагрузки. В этом случае подсистема балансировки нагрузки переадресовывает трафик в сеть маршрутизатора Deis, который, в свою очередь, перенаправляет трафик в соответствующие контейнеры Docker, размещенные в кластере.

  ![Схема архитектуры развернутого кластера Deis](media/virtual-machines-deis-cluster/architecture-overview.png)

Вот что требуется, чтобы выполнить следующие действия:

 * Активная подписка Azure. Если у вас ее нет, можно получить бесплатную ознакомительную версию на сайте [azure.com](https://azure.microsoft.com).
 * Рабочий или учебный идентификатор для использования групп ресурсов Azure. Если у вас имеется личная учетная запись и для входа вы используете идентификатор Майкрософт, необходимо [создать рабочий идентификатор на основе вашего личного идентификатора](resource-group-create-work-id-from-personal.md).
 * Либо, в зависимости от клиентской операционной системы клиента, [Azure PowerShell](powershell-install-configure.md) или [интерфейса командной строки Azure для Mac, Linux и Windows](xplat-cli-install.md).
 * [OpenSSL](https://www.openssl.org/). OpenSSL используется для создания необходимых сертификатов.
 * Клиент Git, например [Git Bash](https://git-scm.com/).
 * Чтобы протестировать пример приложения, также необходим DNS-сервер. Можно использовать любые DNS-серверы или службы, которые поддерживают записи A с подстановочным знаком.
 * Компьютер для запуска клиентских инструментов Deis. Можно использовать локальный компьютер или виртуальную машину. Эти инструменты можно запустить практически на любом дистрибутиве Linux, но приведенные указания относятся к Ubuntu.

## Подготовка кластера

В этом разделе вы будете использовать шаблон [диспетчера ресурсов Azure](../resource-group-overview.md) из репозитория открытого кода [azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates). Сначала вы скопируете шаблон. Затем создадите новую пару ключей SSH для аутентификации. После этого вы настроите новый идентификатор для кластера. И, наконец, вы используете сценарий оболочки или сценарий PowerShell для подготовки кластера.

1. Клонируйте репозиторий: [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).

        git clone https://github.com/Azure/azure-quickstart-templates

2. Перейдите в папку шаблона:

        cd azure-quickstart-templates\deis-cluster-coreos

3. Создайте новую пару ключей SSH, используя ssh-keygen:

        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"

4. Создайте сертификат с помощью полученного выше закрытого ключа:

        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]

5. Перейдите по адресу [https://discovery.etcd.io/new](https://discovery.etcd.io/new), чтобы создать новый токен кластера, который имеет следующий вид:

        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
<br /> У каждого кластера CoreOS должен быть уникальный токен от этой бесплатной службы. Дополнительные сведения см. в [документации по CoreOS](https://coreos.com/docs/cluster-management/setup/cluster-discovery/).

6. Измените файл **cloud-config.yaml**, чтобы заменить существующий токен **discovery** новым:

        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...

7. Измените файл **azuredeploy-parameters.json**: откройте сертификат, созданный на шаге 4, в текстовом редакторе. Скопируйте весь текст между `----BEGIN CERTIFICATE-----` и `-----END CERTIFICATE-----` в параметр **sshKeyData** (необходимо удалить все знаки новой строки).

8. Измените параметр **newStorageAccountName**. Это учетная запись хранения для дисков операционной системы виртуальной машины. Это имя учетной записи должно быть глобально уникальным.

9. Измените параметр **publicDomainName**. Это значение станет частью DNS-имени, связанного с общедоступным IP-адресом подсистемы балансировки нагрузки. У окончательного полного доменного имени будет такой формат: _[значение этого параметра]_._[регион]_.cloudapp.azure.com. Например, если указать имя deishbai32, и группа ресурсов развернута в западной части США, окончательным полным доменным именем вашей подсистемы балансировки нагрузки будет deishbai32.westus.cloudapp.azure.com.

10. Сохраните файл параметров. После этого можно подготовить кластер с помощью Azure PowerShell:

        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml

  Или с помощью Azure CLI:

        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  

11. После подготовки группы ресурсов вы увидите все ресурсы в группе на портале Azure. Как показано на следующем снимке экрана, группа ресурсов содержит виртуальную сеть с тремя виртуальными машинами, которые присоединены к одной группе доступности. Группа также содержит подсистему балансировки нагрузки, имеющую связанный общедоступный IP-адрес.

  ![Подготовленная группа ресурсов на портале Azure](media/virtual-machines-deis-cluster/resource-group.png)

## Установка клиента

Для управления кластером Deis необходим **deisctl**. Хотя deisctl автоматически устанавливается на всех узлах кластера, рекомендуется использовать deisctl на отдельном компьютере администрирования. Более того, так как для всех узлов настроены только частные IP-адреса, для подключения к компьютерам узлов необходимо использовать туннелирование SSH через подсистему балансировки нагрузки, имеющую общедоступный IP-адрес. Ниже приведены шаги по настройке deisctl на отдельном физическом компьютере или виртуальной машине Ubuntu.

1. Установите deisctl:mkdir deis:

        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl

2. Добавьте закрытый ключ в агент ssh:

        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]

3. Настройте deisctl:

        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

Шаблон определяет правила для входящих подключений NAT, сопоставляющие 2223 с экземпляром 1, 2224 с экземпляром 2 и 2225 с экземпляром 3. Это обеспечивает избыточность при использовании инструмента deisctl. Вы можете просмотреть эти правила на портале Azure:

![Правила NAT в подсистеме балансировки нагрузки](media/virtual-machines-deis-cluster/nat-rules.png)

> [AZURE.NOTE]Сейчас шаблон поддерживает только 3-узловые кластеры. Это связано с ограничением в определении правил NAT шаблона диспетчера ресурсов Azure, которое не позволяет использовать синтаксис цикла.

## Установка и запуск платформы Deis

Теперь можно использовать deisctl для установки и запуска платформы Deis:

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [AZURE.NOTE]Запуск платформы занимает некоторое время (до 10 минут). Особенно долго запускается служба builder. Иногда требуется несколько попыток: если операция перестает отвечать на запросы, попробуйте ввести `ctrl+c`, чтобы прервать выполнение команды и повторить попытку.

Можно использовать `deisctl list`, чтобы проверить, все ли службы запущены:

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

Поздравляем! Теперь у вас есть работающий кластер Deis в Azure! Теперь давайте развернем пример приложения Go, чтобы просмотреть, как работает кластер.

## Развертывание и масштабирование приложения Hello World

Ниже показано, как развернуть приложение Go «Hello World» в кластере. Шаги основаны на [документации по Deis](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles).

1. Чтобы сеть маршрутизации работала правильно, необходимо иметь запись A с подстановочным знаком для домена, указывающую на общедоступный IP-адрес подсистемы балансировки нагрузки. На следующем снимке экрана показана запись A для примера регистрации домена на GoDaddy:

    ![Запись A Godaddy](media/virtual-machines-deis-cluster/go-daddy.png)
<p />
2. Установите deis:

        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
        
3. Создайте новый ключ SSH, а затем добавьте открытый ключ в GitHub (конечно, можно также повторно использовать существующие ключи). Чтобы создать новую пару ключей SSH, используйте:

        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)

4. Добавьте в GitHub id\_rsa.pub или открытый ключ (по своему усмотрению). Это можно сделать с помощью кнопки «Add SSH key» (Добавить ключ SSH) на экране настройки ключей SSH:

  ![Ключ GitHub](media/virtual-machines-deis-cluster/github-key.png) <p /> 5. Зарегистрируйте нового пользователя:

        deis register http://deis.[your domain]
<p />
6. Добавьте ключ SSH:

        deis keys:add [path to your SSH public key]
  <p />
7. Создайте приложение:

        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
<p />
8. Отправка git инициирует сборку и развертывание образов Docker, что может занять несколько минут. Из личного опыта: иногда на шаге 10 (при отправке образа в частный репозиторий) система может перестать отвечать на запросы. В этом случае можно остановить процесс, удалить приложение с помощью `deis apps:destroy –a <application name>` и повторить попытку. Чтобы узнать имя приложения, можно использовать `deis apps:list`. Если все работает, по завершении вывода команды вы увидите примерно следующее:

        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
<p />
9. Проверьте, работает ли приложение:

        curl -S http://[your application name].[your domain]
  Вы должны увидеть следующее:

        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
<p />
10. Масштабируйте приложение на 3 экземпляра:

        deis scale cmd=3
<p />
11. Кроме того, можно использовать deis info, чтобы просмотреть сведения о приложении. Ниже приведены выходные данные моего развертывания приложения:

        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }

        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)

        === lambda-underdog Domains
        No domains

## Дальнейшие действия

В этой статье подробно описаны все шаги по подготовке нового кластера Deis в Azure с помощью шаблона диспетчера ресурсов Azure. Шаблон поддерживает избыточность подключений инструментов, а также балансировку нагрузки для развернутых приложений. Шаблон также позволяет избежать использования общедоступных IP-адресов на узлах кластера, что позволяет экономить ценные ресурсы общедоступных IP-адресов и обеспечивает более защищенную среду для размещения приложений. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

[Обзор диспетчера ресурсов Azure][resource-group-overview] [Использование интерфейса командной строки Azure][azure-command-line-tools] [Использование Azure PowerShell с диспетчером ресурсов Azure][powershell-azure-resource-manager]

[azure-command-line-tools]: ../xplat-cli-install.md
[resource-group-overview]: ../resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md

<!---HONumber=Oct15_HO1-->