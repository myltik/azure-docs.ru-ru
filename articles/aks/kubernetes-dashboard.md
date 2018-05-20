---
title: Управлением кластером Azure Kubernetes с помощью пользовательского веб-интерфейса
description: Использование панели мониторинга Kubernetes в AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: cdb406c5a0a314562ae886c797c5ebd9dc5f8796
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="kubernetes-dashboard-with-azure-kubernetes-service-aks"></a>Панель мониторинга Kubernetes со службой Azure Kubernetes

Для запуска панели мониторинга Kubernetes можно использовать Azure CLI. В этом документе рассматривается запуск панели мониторинга Kubernetes с помощью Azure CLI и также некоторые основные операции с ней. Дополнительные сведения о панели мониторинга Kubernetes см. в статье [Web UI (Dashboard)][kubernetes-dashboard] (Панель мониторинга веб-интерфейса).

## <a name="before-you-begin"></a>Перед началом работы

В действиях, описанных в этом документе, предполагается, что кластер AKS создан и с ним установлено соединение kubectl. Если вам требуются эти компоненты, см. статью [Развертывание кластера Службы контейнеров Azure (AKS)][aks-quickstart].

Кроме того, нужно установить и настроить Azure CLI 2.0.27 или более поздней версии. Чтобы узнать версию, выполните команду az --version. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

## <a name="start-kubernetes-dashboard"></a>Запуск панели мониторинга Kubernetes

Выполните команду `az aks browse` для запуска панели мониторинга Kubernetes. При выполнении этой команды замените имя группы ресурсов и кластера.

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Эта команда создает прокси-сервер между системой разработки и API Kubernetes и открывает панель мониторинга Kubernetes в веб-браузере.

## <a name="run-an-application"></a>Запуск приложения

В правом верхнем углу панели мониторинга Kubernetes нажмите кнопку **Создать**. Присвойте развертыванию имя `nginx` и введите `nginx:latest` в качестве имени образа. В разделе **Служба** выберите **External** (Внешний) и введите `80` для порта и целевого порта.

Когда все будет готово, щелкните **Развернуть** для создания развертывания.

![Диалоговое окно создания службы Kubernetes](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Просмотр приложения

Сведения о состоянии приложения можно просмотреть на панели мониторинга Kubernetes. Когда приложение выполняется, рядом с каждым компонентом стоит зеленый флажок.

![Модули Kubernetes](./media/container-service-kubernetes-ui/complete-deployment.png)

Чтобы просмотреть дополнительные сведения о модулях приложения, в левом меню щелкните **Pods** (Модули), а затем выберите модуль **NGINX**. Здесь можно просмотреть сведения о модуле, например данные о потреблении ресурсов.

![Ресурсы Kubernetes](./media/container-service-kubernetes-ui/running-pods.png)

Чтобы получить IP-адрес приложения, в левом меню щелкните **Службы**, а затем выберите службу **NGINX**.

![Представление nginx](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Изменение приложения

Помимо создания и просмотра приложений панель мониторинга Kubernetes можно использовать для изменения и обновления развертываний приложений.

Чтобы изменить развертывание, в левом меню щелкните **Развернутые приложения**, а затем выберите развертывание **NGINX**. Наконец, выберите **Изменить** на верхней правой панели навигации.

![Окно изменения в Kubernetes](./media/container-service-kubernetes-ui/view-deployment.png)

Найдите значение `spec.replica`, которое должно быть равно 1. Измените это значение на 3. При этом счетчик реплики развертывания NGINX увеличится с 1 до 3.

Выберите **Обновить** по окончании.

![Окно изменения в Kubernetes](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о панели мониторинга Kubernetes см. в документации по Kubernetes.

> [!div class="nextstepaction"]
> [Web UI (Dashboard)][kubernetes-dashboard] (Панель мониторинга веб-интерфейса)

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli