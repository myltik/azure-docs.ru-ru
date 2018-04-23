---
title: Подключение тома secret в службе "Экземпляры контейнеров Azure"
description: Узнайте, как подключить том secret для хранения конфиденциальной информации, чтобы обеспечить доступ экземплярам контейнеров
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: e20dc3b37bc766b2a8a352eb62c03f5e2ee14c52
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Подключение тома secret в службе "Экземпляры контейнеров Azure"

Узнайте, как подключить том *secret* в экземплярах контейнеров для хранилища, а также, как контейнеры могут извлечь конфиденциальную информацию в группах контейнеров.

> [!NOTE]
> В настоящее время подключение тома *secret* поддерживается только для контейнеров Linux. Мы работаем над тем, чтобы обеспечить все функции для контейнеров Windows, но для текущей платформы есть отличия в [квотах и доступности регионов для службы "Экземпляры контейнеров Azure"](container-instances-quotas.md).

## <a name="secret-volume"></a>том secret

Вы можете использовать том *secret*, чтобы предоставить конфиденциальную информацию контейнерам в группе контейнеров. В томе *secret* в файлах хранятся конфиденциальные сведения, к которым у контейнеров в группе контейнеров есть доступ. С помощью секретов в томе *secret* можно избежать размещения конфиденциальных данных, например ключей SSH или учетных данных базы данных, в коде приложения.

Все тома *secret* поддерживает [tmpfs][tmpfs] — файловая система, сохраняющая данные в ОЗУ. Их содержимое никогда не записывается в хранилище для долговременного хранения данных.

## <a name="mount-a-secret-volume"></a>Подключение тома secret

Чтобы подключить том *secret* в экземпляре контейнера, необходимо выполнить развертывание с помощью [шаблона Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Сначала заполните массив `volumes` в разделе `properties` группы контейнеров шаблона. Затем для каждого контейнера в группе контейнеров, в которой нужно подключить том *secret*, заполните массив `volumeMounts` в разделе `properties` определения контейнера.

Например, следующий шаблон Resource Manager создает группу контейнеров, состоящую из одного контейнера. С помощью контейнера подключается том *secret*, состоящий из двух секретов в кодировке Base64.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Пример развертывания экземпляра контейнера с помощью шаблона Azure Resource Manager см. в статье [Развертывание группы контейнеров](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Дополнительная информация

Сведения о подключении других типов томов в службе "Экземпляры контейнеров Azure" см. в следующих статьях:

* [Подключение общего файлового ресурса Azure с помощью службы "Экземпляры контейнеров Azure"](container-instances-volume-azure-files.md)
* [Mount an emptyDir volume in Azure Container Instances](container-instances-volume-emptydir.md) (Подключение тома emptyDir в службе "Экземпляры контейнеров Azure")
* [Подключение тома gitRepo в службе "Экземпляры контейнеров Azure"](container-instances-volume-gitrepo.md)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs