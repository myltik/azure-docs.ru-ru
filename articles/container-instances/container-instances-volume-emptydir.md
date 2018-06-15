---
title: Подключение тома emptyDir в службе "Экземпляры контейнеров Azure"
description: Узнайте, как подключить том emptyDir для обмена данными между контейнерами в группе контейнеров службы "Экземпляры контейнеров Azure"
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 89289a7a0bb5c486c662d528c5014bdbd8eebaca
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164896"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Подключение тома emptyDir в службе "Экземпляры контейнеров Azure"

Узнайте, как подключить том *emptyDir* для обмена данными между контейнерами в группе контейнеров службы "Экземпляры контейнеров Azure".

> [!NOTE]
> Сейчас подключение тома *emptyDir* поддерживается только для контейнеров Linux. Мы работаем над тем, чтобы обеспечить все функции для контейнеров Windows, но для текущей платформы есть отличия в [квотах и доступности регионов для службы "Экземпляры контейнеров Azure"](container-instances-quotas.md).

## <a name="emptydir-volume"></a>Том emptyDir

Том *emptyDir* предоставляет доступный для записи каталог каждому контейнеру в группе контейнеров. Контейнеры в группе могут считывать и записывать одни и те же файлы в томе. Также их можно подключать с использованием одинаковых или разных путей в каждом контейнере.

Некоторые примеры использования тома *emptyDir*:

* область временных файлов;
* контрольные точки во время длительных задач;
* хранение данных, полученных сопроводительным контейнером и обслуживаемых контейнером приложения.

В случае сбоев контейнеров данные в томе *emptyDir* сохраняются. Но при перезапуске контейнеров сохранность данных в томе *emptyDir* не гарантируется.

## <a name="mount-an-emptydir-volume"></a>Подключение тома emptyDir

Чтобы подключить том emptyDir в экземпляре контейнера, необходимо выполнить развертывание с помощью [шаблона Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Сначала заполните массив `volumes` в разделе `properties` группы контейнеров шаблона. Затем для каждого контейнера в группе контейнеров, в которой нужно подключить том *emptyDir*, заполните массив `volumeMounts` в разделе `properties` определения контейнера.

Например, при помощи следующего шаблона Resource Manager создается группа из двух контейнеров, в каждом из которых подключается том *emptyDir*:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Пример развертывания экземпляра контейнера с помощью шаблона Azure Resource Manager см. в статье [Развертывание группы контейнеров](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Дополнительная информация

Сведения о подключении других типов томов в службе "Экземпляры контейнеров Azure" см. в следующих статьях:

* [Mount an Azure file share in Azure Container Instances](container-instances-volume-azure-files.md) (Подключение файлового ресурса Azure с помощью службы "Экземпляры контейнеров Azure")
* [Подключение тома gitRepo в службе "Экземпляры контейнеров Azure"](container-instances-volume-gitrepo.md)
* [Подключение тома secret в службе "Экземпляры контейнеров Azure"](container-instances-volume-secret.md)