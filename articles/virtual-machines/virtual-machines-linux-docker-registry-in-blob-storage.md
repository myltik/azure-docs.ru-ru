<properties 
  pageTitle="Развертывание частного реестра Docker в Azure | Microsoft Azure"
  description="В этой статье объясняется, как разместить образы контейнеров в службе хранилища BLOB-объектов Azure."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="" 
  tags="azure-service-management,azure-resource-manager" />

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="04/19/2016" 
  ms.author="ahmetb" />

# Развертывание частного реестра Docker в Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



В этой статье объясняется, что представляет собой частный реестр Docker и как можно развернуть образ контейнера Docker Registry 2.0 в частный реестр Docker в Microsoft Azure, используя хранилище BLOB-объектов Azure.

В этом документе предполагается, что:

1. Вы знаете, как работать с Docker, и у вас есть образы Docker для хранения. (Если это не так, посетите [веб-сайт Docker](https://www.docker.com).)
2. У вас есть сервер, на котором установлено ядро Docker. (Если это не так, [нужный сервер можно быстро создать в Azure.](https://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/))


## Что представляет собой частный реестр Docker

Чтобы доставить контейнерное приложение в облако, вам нужно создать образ контейнера Docker и где-нибудь сохранить его для последующего использования.

Создать образ контейнера и доставить его в облако просто, а вот надежное хранения созданного образа сопряжено с определенными сложностями. По этой причине для Docker предусмотрена централизованная служба под названием [Docker Hub][docker-hub], которая позволяет хранить в облаке образы контейнеров и с их помощью создавать нужные контейнеры.

[Docker Hub][docker-hub] — платная служба для хранения частных образов контейнеров приложений. Тем не менее, создатели Docker учли потребности других разработчиков и создали набор инструментов с открытым исходным кодом, который позволяет хранить образы в частном реестре Docker. Такой реестр размещается локально за брандмауэром (а не в общедоступном сегменте Интернета). Так как для хранилища BLOB-объектов Azure можно легко настроить требуемую защиту, на его основе в Azure можно создать частный реестр Docker и самостоятельно им управлять.

## Преимущества размещения реестра Docker в Azure

Размещение экземпляра Docker Registry в Microsoft Azure и хранение образов в хранилище BLOB-объектов Azure имеет несколько преимуществ.

**Безопасность**. Ваши образы Docker хранятся только в центрах обработки данных Azure, тогда как служба Docker Hub размещена в общедоступном сегменте Интернета.
  
**Производительность**. Ваши образы Docker хранятся в том же центре обработки данных или регионе, что и ваши приложения. Это означает, что образы будут извлекаться быстрее и надежнее, чем в случае со службой Docker Hub.

**Надежность**. Отдав предпочтение хранилищу BLOB-объектов Microsoft Azure, вы получаете целый ряд возможностей и преимуществ, в частности высокую доступность, избыточность, хранилище класса Premium (диски SSD) и т. д.

## Настройка Docker Registry для работы в хранилище BLOB-объектов Azure

(Для начала рекомендуем изучить [документацию по Docker Registry 2.0][registry-docs].)

[Настройку][registry-config] Docker Registry можно выполнить двумя различными способами. Вы можете:

1. Воспользоваться файлом `config.yml`. В этом случае вам потребуется создать отдельный образ Docker и заменить им образ `registry`.
2. Переопределить файл конфигурации по умолчанию через переменные среды. В этом случае вам не нужно создавать и поддерживать отдельный образ Docker.

Чтобы не усложнять эту статью, мы поговорим только о втором варианте, который предусматривает использование переменных среды.

Мы запустим экземпляр Docker Registry, который:
* использует учетную запись хранения Azure для хранения образов;
* прослушивает порт 5000 виртуальной машины;
* не использует аутентификацию (не рекомендуется, см. примечание ниже).

Чтобы запустить экземпляр Docker Registry, в терминале Bash необходимо выполнить такую команду Docker (вместо `<storage-account>` и `<storage-key>` укажите свои учетные данные):

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

После выполнения команды вы сможете просмотреть контейнер с частным экземпляром Docker Registry, выполнив на узле команду `docker ps`.

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT] В этом документе не рассматривается настройка безопасности экземпляра Docker Registry. Если вы откроете порт реестра на конечной точке с виртуальной машиной или в балансировщике нагрузки, приведенная выше команда развертывания сделает ваш реестр доступным для всех (по умолчанию проверка подлинности не будет выполняться).
>
> Сведения о том, как защитить экземпляр реестра и образы, приведены в документации по [настройке Docker Registry][registry-config].

## Дальнейшие действия

Настроив свой реестр, попробуйте поработать с ним. Для начала изучите [документацию по Docker Registry].

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[registry-docs]: http://docs.docker.com/registry/
[документацию по Docker Registry]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 

<!---HONumber=AcomDC_0427_2016-->