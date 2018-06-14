---
title: Управление веб-приложением для контейнеров с помощью Azure CLI 2.0 | Документация Майкрософт
description: Сведения о том, как управлять веб-приложением для контейнеров с помощью Azure CLI.
keywords: служба приложений azure, веб-приложение, cli, linux, oss
services: app-service
documentationCenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.openlocfilehash: 54c979313a6ffa43008aa9870332b92d2b2f182a
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2017
ms.locfileid: "24105395"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Управление веб-приложением для контейнеров с помощью Azure CLI

Используя команды в этой статье, вы сможете создавать и администрировать веб-приложения для контейнеров с помощью Azure CLI 2.0.
Начать использовать новую версию CLI можно двумя способами:

* [установить Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) на компьютер;
* использовать [Azure Cloud Shell (предварительная версия)](../../cloud-shell/overview.md).

## <a name="create-a-linux-app-service-plan"></a>Создание плана службы приложений Linux

Чтобы создать план службы приложений Linux, выполните следующую команду:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Создание пользовательского веб-приложения контейнера Docker

Чтобы создать веб-приложение и настроить его для запуска пользовательского контейнера Docker, выполните следующую команду:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Активация ведения журнала контейнера Docker

Чтобы включить ведение журнала для контейнера Docker, можно использовать следующую команду:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Изменение пользовательского контейнера Docker для существующего веб-приложения для контейнеров

Чтобы изменить текущий образ Docker ранее созданного приложения на новый образ, выполните следующую команду:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Использование образов Docker из частного реестра

Вы можете настроить свое приложение для использования образов из частного реестра. Необходимо указать URL-адрес для реестра, имени пользователя и пароля. Для этого выполните следующую команду:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Включение непрерывного развертывания для пользовательских образов Docker

Выполните указанную ниже команду, чтобы обеспечить возможность использования компакт-диска и получить URL-адрес веб-перехватчика. Этот URL-адрес можно использовать для настройки репозиториев DockerHub или реестра контейнеров Azure.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Создание веб-приложения для контейнеров с помощью одной из встроенных платформ среды выполнения

Чтобы создать веб-приложение для контейнеров на основе PHP 5.6, выполните следующую команду:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Изменение версии платформы для существующего веб-приложения для контейнеров

Чтобы изменить ранее созданное приложение с текущей версии на Node.js 6.11, выполните следующую команду:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Настройка развертываний Git для веб-приложения

Чтобы настроить развертывания Git для приложения, выполните следующую команду:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Дополнительная информация

* [Общие сведения о службе приложений на платформе Linux](app-service-linux-intro.md).
* [Установите Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
* [Обзор Azure Cloud Shell (предварительная версия)](../../cloud-shell/overview.md)
* [Настройка промежуточных сред в службе приложений Azure](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Непрерывное развертывание с использованием платформы Azure "Веб-приложения для контейнеров"](app-service-linux-ci-cd.md).
