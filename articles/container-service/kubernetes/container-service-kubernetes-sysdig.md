---
title: Мониторинг кластера Azure Kubernetes с помощью Sysdig
description: Мониторинг кластера Kubernetes в Службе контейнеров Azure с помощью Sysdig.
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 275e71ce054b83c16b9f9cbfe621c6a7e31f79c6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>Мониторинг кластера Kubernetes в Службе контейнеров Azure с помощью Sysdig

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>предварительным требованиям
В этом пошаговом руководстве предполагается, что вы [создали кластер Kubernetes с помощью службы контейнеров Azure](container-service-kubernetes-walkthrough.md).

Также предполагается, что у вас установлен интерфейс командной строки Azure и средство kubectl.

Чтобы проверить наличие средства `az`, выполните такую команду:

```console
$ az --version
```

Если средство `az` не установлено, следуйте инструкциям, приведенным [здесь](https://github.com/azure/azure-cli#installation).

Чтобы проверить наличие средства `kubectl`, выполните такую команду:

```console
$ kubectl version
```

Если средство `kubectl` не установлено, выполните команду:

```console
$ az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
Sysdig является компанией, предоставляющей внешний мониторинг как услугу, которая может отслеживать контейнеры в кластере Kubernetes, работающем в Azure. Для использования услуг Sysdig требуется активная учетная запись Sysdig.
Зарегистрироваться для получения учетной записи можно на [сайте компании](https://app.sysdigcloud.com).

Войдите на облачный сайт Sysdig, щелкните свое имя пользователя. На странице отобразится ваш ключ доступа. 

![Sysdig: ключ API](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Установка агентов Sysdig в Kubernetes
Для мониторинга контейнеров Sysdig запускает на каждом компьютере процесс с помощью `DaemonSet` Kubernetes.
Наборы DaemonSet являются объектами API Kubernetes, которые выполняют отдельный экземпляр контейнера на каждом компьютере.
Они идеально подходят для установки инструментов, например агента мониторинга Sysdig.

Чтобы установить DaemonSet Sysdig, следует сначала скачать [шаблон](https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml) с сайта Sysdig. Сохраните этот файл как `sysdig-daemonset.yaml`.

В Linux и OS X можно выполнить команду:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

В PowerShell:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Измените этот файл, чтобы вставить в него ключ доступа, который был получен из вашей учетной записи Sysdig.

Наконец, создайте DaemonSet.

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Просмотр данных мониторинга
После установки и запуска агенты должны передавать данные обратно в Sysdig.  Вернитесь к [панели мониторинга Sysdig](https://app.sysdigcloud.com), и вы должны увидеть сведения о контейнерах.

Вы можете также установить панели мониторинга для Kubernetes с помощью [мастера создания панелей мониторинга](https://app.sysdigcloud.com/#/dashboards/new).
