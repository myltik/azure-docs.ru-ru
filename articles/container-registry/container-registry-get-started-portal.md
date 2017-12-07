---
title: "Краткое руководство. Создание частного реестра Docker в Azure с помощью портала Azure"
description: "Быстрый способ изучить создание частного реестра контейнеров Docker с помощью портала Azure."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 10/31/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: e6338ca03bcb0daa040a62b06cfa3ad7ba66a1b2
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2017
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Создание реестра контейнеров на портале Azure

Реестр контейнеров Azure — это частный реестр Docker в Azure, где можно хранить частные образы контейнеров Docker и управлять ими. В этом кратком руководстве вы создадите реестр контейнеров с помощью портала Azure.

Для выполнения этого краткого руководства потребуется локально установленное средство Docker. Docker содержит пакеты, которые позволяют быстро настроить Docker в любой системе [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) или [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="create-a-container-registry"></a>Создание реестра контейнеров

Выберите **Создать** > **Контейнеры** > **Реестр контейнеров Azure**.

![Создание реестра контейнеров на портале Azure][qs-portal-01]

Введите значения параметров **Имя реестра** и **Группа ресурсов**. Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов. Создайте новую группу ресурсов с именем `myResourceGroup`, а для параметра **SKU** выберите значение "Базовый". Выберите **Создать**, чтобы развернуть экземпляр записи контроля доступа (ACR).

![Создание реестра контейнеров на портале Azure][qs-portal-03]

В этом кратком руководстве мы создадим реестр уровня *Базовый*. Реестр контейнеров Azure доступен в нескольких номерах SKU, которые кратко описаны в следующей таблице. См. дополнительные сведения о [номерах SKU реестра контейнеров](container-registry-skus.md).

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Когда появится сообщение **Развертывание прошло успешно**, выберите реестр контейнеров на портале, а затем выберите **Ключи доступа**.

![Создание реестра контейнеров на портале Azure][qs-portal-05]

В разделе **Пользователь-администратор** выберите **Включить**. Запишите следующие значения:

* сервер входа;
* Имя пользователя
* пароль

Эти значения мы будем использовать на дальнейших этапах работы с реестром с помощью Docker CLI.

![Создание реестра контейнеров на портале Azure][qs-portal-06]

## <a name="log-in-to-acr"></a>Вход в ACR

Перед отправкой и извлечением образов контейнеров необходимо войти в экземпляр ACR. Чтобы сделать это, используйте команду [docker login](https://docs.docker.com/engine/reference/commandline/login/). Замените значения *username* (имя пользователя), *password* (пароль) и *login server* (сервер входа) значениями, записанными на предыдущем шаге.

```bash
docker login --username <username> --password <password> <login server>
```

По завершении команда возвращает `Login Succeeded`. Также может появиться предупреждение системы безопасности, рекомендующее использовать параметр `--password-stdin`. Хотя его использование выходит за рамки данной статьи, мы рекомендуем следовать данной рекомендации. Чтобы получить дополнительные сведения, ознакомьтесь с описанием команды [docker login](https://docs.docker.com/engine/reference/commandline/login/).

## <a name="push-image-to-acr"></a>Отправка образа в ACR

Чтобы отправить образ в реестр контейнеров Azure, сначала нужно получить этот образ. При необходимости выполните следующую команду, чтобы извлечь существующий образ из Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Прежде чем отправить образ в реестр, необходимо снабдить его тегом имени сервера входа в ACR. Добавьте к образу тег с помощью команды [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Замените значение *login server* (сервер входа) именем сервера входа, записанным ранее.

```
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Наконец, воспользуйтесь командой [docker push](https://docs.docker.com/engine/reference/commandline/push/) для отправки образа в экземпляр ACR. Замените значение *login server* именем сервера входа для вашего экземпляра ACR.

```
docker push <login server>/aci-helloworld:v1
```

Выходные данные успешно выполненной команды `docker push` выглядят примерно так:

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>Список образов контейнеров

Чтобы вывести список образов в вашем экземпляре ACR, перейдите в реестр на портале и выберите **Репозитории**, а затем выберите репозиторий, созданный с помощью `docker push`.

В этом примере мы выбираем репозиторий **aci-helloworld** и видим образ с тегом `v1` в разделе **Теги**.

![Создание реестра контейнеров на портале Azure][qs-portal-09]

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов **myResourceGroup**, когда она станет не нужна. При этом будут удалены группа ресурсов, экземпляр ACR и все образы контейнеров.

![Создание реестра контейнеров на портале Azure][qs-portal-08]

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали реестр контейнеров Azure с помощью Azure CLI. Если вы хотите использовать реестр контейнеров Azure со службой "Экземпляры контейнеров Azure", перейдите к соответствующему руководству.

> [!div class="nextstepaction"]
> [Руководства по использованию службы "Экземпляры контейнеров Azure"](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png