---
title: Краткое руководство. Создание частного реестра Docker в Azure с помощью портала Azure
description: Быстрый способ изучить создание частного реестра контейнеров Docker с помощью портала Azure.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 55da52e0d314c353c669c56ad918c4dd6bef44c3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32169811"
---
# <a name="quickstart-create-a-container-registry-using-the-azure-portal"></a>Краткое руководство. Создание реестра контейнеров на портале Azure

Реестр контейнеров Azure — это частный реестр Docker в Azure, где можно хранить частные образы контейнеров Docker и управлять ими. Из этого краткого руководства вы узнаете, как создать реестр контейнера на портале Azure, отправить образ контейнера в реестр и, наконец, развернуть контейнер из реестра в службе "Экземпляры контейнеров Azure" (ACI).

Для выполнения этого краткого руководства потребуется локально установленное средство Docker. Docker предоставляет пакеты, которые позволяют быстро настроить Docker в любой системе [Mac][docker-mac], [Windows][docker-windows] или [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="create-a-container-registry"></a>Создание реестра контейнеров

Последовательно выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров Azure**.

![Создание реестра контейнеров на портале Azure][qs-portal-01]

Введите значения параметров **Имя реестра** и **Группа ресурсов**. Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов. Создайте новую группу ресурсов с именем `myResourceGroup`, а для параметра **SKU** выберите значение "Базовый". Выберите **Создать**, чтобы развернуть экземпляр записи контроля доступа (ACR).

![Создание реестра контейнеров на портале Azure][qs-portal-03]

В этом кратком руководстве мы создадим реестр уровня *Базовый*. Реестр контейнеров Azure доступен в нескольких номерах SKU, которые кратко описаны в следующей таблице. См. дополнительные сведения о [номерах SKU реестра контейнеров][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Когда появится сообщение **Развертывание прошло успешно**, выберите реестр контейнеров на портале, а затем выберите **Ключи доступа**.

![Создание реестра контейнеров на портале Azure][qs-portal-05]

В разделе **Пользователь-администратор** выберите **Включить**. Запишите следующие значения:

* сервер входа;
* Имя пользователя
* password

Эти значения мы будем использовать на дальнейших этапах работы с реестром с помощью Docker CLI.

![Создание реестра контейнеров на портале Azure][qs-portal-06]

## <a name="log-in-to-acr"></a>Вход в ACR

Перед отправкой и извлечением образов контейнеров необходимо войти в экземпляр ACR. Чтобы сделать это, используйте команду [docker login][docker-login]. Замените значения *username* (имя пользователя), *password* (пароль) и *login server* (сервер входа) значениями, записанными на предыдущем шаге.

```bash
docker login --username <username> --password <password> <login server>
```

По завершении команда возвращает `Login Succeeded`. Также может появиться предупреждение системы безопасности, рекомендующее использовать параметр `--password-stdin`. Хотя его использование выходит за рамки данной статьи, мы рекомендуем следовать данной рекомендации. Чтобы получить дополнительные сведения, ознакомьтесь с описанием команды [docker login][docker-login].

## <a name="push-image-to-acr"></a>Отправка образа в ACR

Чтобы отправить образ в реестр контейнеров Azure, сначала нужно получить этот образ. При необходимости выполните следующую команду, чтобы извлечь существующий образ из Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Прежде чем отправить образ в реестр, необходимо снабдить его тегом имени сервера входа в ACR. Присвойте образу тег с помощью команды [docker tag][docker-tag]. Замените значение *login server* (сервер входа) именем сервера входа, записанным ранее.

```bash
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Наконец, воспользуйтесь командой [docker push][docker-push] для принудительной отправки образа в экземпляр ACR. Замените значение *login server* именем сервера входа для вашего экземпляра ACR.

```bash
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

## <a name="deploy-image-to-aci"></a>Развертывание образа в службе "Экземпляры контейнеров Azure"

Чтобы развернуть образ из реестра в экземпляр, нужно перейти в репозиторий (aci helloworld), а затем щелкнуть многоточие рядом со значением v1.

![Запуск экземпляра контейнера Azure на портале][qs-portal-10]

Когда появится контекстное меню, выберите **Запустить экземпляр**.

![Контекстное меню для запуска экземпляра контейнера Azure][qs-portal-11]

Для запуска экземпляра контейнера Azure заполните поле **Имя контейнера**, выберите правильную подписку и существующую **группу ресурсов** "myResourceGroup", а затем нажмите кнопку **ОК**.

![Варианты развертывания для запуска экземпляр контейнера Azure][qs-portal-12]

При запуске развертывания на панели мониторинга портала отображается плитка, показывающая ход развертывания. После завершения развертывания плитка обновляется для отображения новой группы контейнеров **mycontainer**.

![Состояния развертывания экземпляра контейнера Azure][qs-portal-13]

Для отображения свойств группы контейнеров выберите группу контейнеров mycontainer. Обратите внимание на **IP-адрес** группы контейнеров, а также на **состояние** контейнера.

![Подробная информация о контейнере ACI][qs-portal-14]

## <a name="view-the-application"></a>Просмотр приложения

Когда контейнер находится в состояние **Выполняется**, в своем браузере перейдите к IP-адресу, записанному на предыдущем шаге, чтобы отобразить приложение.

![Приложение Hello World в браузере][qs-portal-15]

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы очистить ресурсы, перейдите к группе ресурсов **myResourceGroup** на портале. После загрузки группы ресурсов щелкните пункт **Удалить группу ресурсов** для удаления группы ресурсов, реестра контейнеров Azure и всех экземпляров контейнеров Azure.

![Создание реестра контейнеров на портале Azure][qs-portal-08]

## <a name="next-steps"></a>Дополнительная информация

С помощью этого краткого руководства вы создали реестр контейнеров Azure с использованием Azure CLI и запустили его экземпляр в службе "Экземпляры контейнеров Azure". Чтобы подробнее рассмотреть службу "Экземпляры контейнеров Azure", перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Руководства по использованию службы "Экземпляры контейнеров Azure"][container-instances-tutorial-prepare-app]

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
[qs-portal-10]: ./media/container-registry-get-started-portal/qs-portal-10.png
[qs-portal-11]: ./media/container-registry-get-started-portal/qs-portal-11.png
[qs-portal-12]: ./media/container-registry-get-started-portal/qs-portal-12.png
[qs-portal-13]: ./media/container-registry-get-started-portal/qs-portal-13.png
[qs-portal-14]: ./media/container-registry-get-started-portal/qs-portal-14.png
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
