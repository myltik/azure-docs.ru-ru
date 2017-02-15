---
title: "Развертывание кластера Службы контейнеров Azure с помощью интерфейса командной строки | Документация Майкрософт"
description: "Развертывание кластера Службы контейнеров Azure с использованием предварительной версии интерфейса командной строки Azure 2.0"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 855f0fe77bd55f6ec0dacad4bc28603ac1c6979c
ms.openlocfilehash: c4a513686433e802f27f78de60e8b7fca21b4634


---
# <a name="using-the-azure-cli-20-preview-to-create-an-azure-container-service-cluster"></a>Использование предварительной версии интерфейса командной строки Azure 2.0 для создания кластера Службы контейнеров Azure

Чтобы создать кластер Службы контейнеров Azure, вам понадобится:
* Учетная запись Azure ([получите бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/)).
* [интерфейс командной строки Azure 2.0 (предварительная версия)](https://github.com/Azure/azure-cli#installation), установленный на компьютере;
* войти в учетную запись Azure (см. ниже).

## <a name="log-in-to-your-account"></a>Вход в учетную запись
```azurecli
az login 
```
Чтобы пройти проверку подлинности с использованием кода устройства, предоставленного в интерфейсе командной строки, необходимо перейти по этой [ссылке](https://login.microsoftonline.com/common/oauth2/deviceauth).

![ввод команды](media/container-service-create-acs-cluster-cli/login.png)

![браузер iPhone;](media/container-service-create-acs-cluster-cli/login-browser.png)


## <a name="create-a-resource-group"></a>Создание группы ресурсов
```azurecli
az group create -n acsrg1 -l "westus"
```

![Изображение создания группы ресурсов](media/container-service-create-acs-cluster-cli/rg-create.png)

## <a name="list-of-available-azure-container-service-cli-commands"></a>Список доступных команд интерфейса командной строки Службы контейнеров Azure

```azurecli
az acs -h
```

![Использование команды ACS](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

## <a name="create-an-azure-container-service-cluster"></a>Создание кластера Службы контейнеров Azure

*Использование создания ACS в интерфейсе командной строки*

```azurecli
az acs create -h
```
Имя службы контейнеров, группы ресурсов, созданной на предыдущем шаге, и уникальное DNS-имя являются обязательными. Для других входных данных устанавливаются значения по умолчанию (см. снимок экрана с моментальным снимком справки ниже), если они не переопределены с помощью соответствующих параметров.
![Изображение справки по созданию ACS](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

*Для быстрого создания ACS используются значения по умолчанию. Если у вас нет ключа SSH, используйте вторую команду. Эта команда create с параметром --generate-ssh-keys создаст его для вас.*

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

*Значение dns-prefix (параметр -d) должно быть уникальным. При возникновении ошибки повторите попытку, используя уникальную строку.*

После введения предыдущей команды подождите около 10 минут, пока будет создан кластер.

![Изображение создания ACS](media/container-service-create-acs-cluster-cli/cluster-create.png)

## <a name="list-acs-clusters"></a>Отображение списка кластеров ACS 

### <a name="under-a-subscription"></a>В подписке

```azurecli
az acs list --output table
```

### <a name="in-a-specific-resource-group"></a>В определенной группе ресурсов

```azurecli
az acs list -g acsrg1 --output table
```

![Изображение списка ACS](media/container-service-create-acs-cluster-cli/acs-list.png)


## <a name="display-details-of-a-container-service-cluster"></a>Отображение сведений о кластере службы контейнеров

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![Изображение списка ACS](media/container-service-create-acs-cluster-cli/acs-show.png)


## <a name="scale-the-acs-cluster"></a>Масштабирование кластера ACS
*Увеличение и уменьшение масштаба разрешено. Параметр new-agent-count представляет собой новое количество агентов в кластере ACS.*

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![Изображение масштабирования ACS](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>Удаление кластера службы контейнеров
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
*Обратите внимание, что эта команда удаления не удаляет все ресурсы (сеть и хранилище), созданные при создании службы контейнеров. Чтобы удалить все ресурсы, мы рекомендуем создать один кластер ACS в каждой группе ресурсов, а затем, когда потребность в кластере отпадет, удалить саму группу ресурсов. Таким образом все связанные ресурсы будут удалены, и за них больше не будет взиматься плата.*



<!--HONumber=Nov16_HO3-->


