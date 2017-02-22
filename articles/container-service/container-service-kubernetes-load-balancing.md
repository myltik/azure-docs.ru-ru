---
title: "Балансировка нагрузки контейнеров Kubernetes в Azure | Документация Майкрософт"
description: "Подключайтесь извне и выполняйте балансировку нагрузки нескольких контейнеров в кластере Kubernetes в Службе контейнеров Azure."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Контейнеры, микрослужбы, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 545ec23bc720dc1a17ce3d084642e96c2397d482
ms.openlocfilehash: e61638db3c6c3acdc58f374b94b28d855161cf59


---
# <a name="load-balance-containers-in-a-kubernetes-cluster-in-azure-container-service"></a>Балансировка нагрузки контейнеров в кластере Kubernetes в Службе контейнеров Azure 
В этой статье рассматривается балансировка нагрузки в кластере Kubernetes в Службе контейнеров Azure. При балансировке нагрузки предоставляется доступный через Интернет IP-адрес для службы и распределяется сетевой трафик между модулями, запущенными в виртуальных машинах агента.

Службу Kubernetes можно настроить для использования [Azure Load Balancer](../load-balancer/load-balancer-overview.md), чтобы управлять трафиком внешней сети (TCP или UDP). Благодаря дополнительной настройке возможна балансировка нагрузки и маршрутизация трафика HTTP или HTTPS или более сложные сценарии.

## <a name="prerequisites"></a>Предварительные требования
* [Развертывание кластера Kubernetes](container-service-kubernetes-walkthrough.md) в Службе контейнеров Azure
* [Подключение клиента](container-service-connect.md) к кластеру

## <a name="azure-load-balancer"></a>Azure Load Balancer

По умолчанию кластер Kubernetes, развернутый в Службе контейнеров Azure, включает Azure Load Balancer с выходом в Интернет для виртуальных машин агента. (Для главных виртуальных машин настраивается отдельный ресурс балансировщика нагрузки.) Azure Load Balancer является балансировщиком нагрузки 4-го уровня (TCP, UDP).

При создании службы Kubernetes можно автоматически настроить балансировщик нагрузки Azure, чтобы обеспечить доступ к службе. Чтобы настроить балансировщик нагрузки, задайте для параметра `type` службы значение `LoadBalancer`. Балансировщик нагрузки создает правило для сопоставления открытого IP-адреса и номера порта для входящего трафика службы с закрытыми IP-адресами и номерами портов в модулях виртуальных машин (и наоборот для ответного трафика). 

 Ниже приведены два примера задания для параметра `type` службы Kubernetes значения `LoadBalancer`. (После выполнения примеров удалите развертывания, если они больше не нужны.)

### <a name="example-use-the-kubectl-expose-command"></a>Пример. Использование команды `kubectl expose` 
В статье [Обработчик службы контейнеров Microsoft Azure. Пошаговое руководство по использованию Kubernetes](container-service-kubernetes-walkthrough.md) содержится пример предоставления службы с командой `kubectl expose` и ее параметром `--type=LoadBalancer`. Выполните следующие действия.

1. Запустите новое развертывание контейнера. Например, следующая команда запускает новое развертывание с именем `mynginx`. Оно состоит из трех контейнеров на основе образа Docker для веб-сервера Nginx.

    ```console
    kubectl run mynginx --replicas=3 --image nginx
    ```
2. Убедитесь, что контейнеры запущены. Например, если выполнить запрос на контейнеры, используя команду `kubectl get pods`, должен появиться результат, аналогичный следующему:

    ![Получение контейнеров Nginx](./media/container-service-kubernetes-load-balancing/nginx-get-pods.png)

3. Чтобы настроить балансировщик нагрузки для принятия внешнего трафика в развертывание, выполните команду `kubectl expose` со значением `--type=LoadBalancer`. Следующая команда предоставляет сервер Nginx через порт 80:

    ```console
    kubectl expose deployments mynginx --port=80 --type=LoadBalancer
    ```

4. Введите `kubectl get svc`, чтобы просмотреть сведения о состоянии служб в кластере. Пока балансировщик нагрузки настраивает правило, `EXTERNAL-IP` службы отображается как `<pending>`. Через несколько минут настройка внешнего IP-адреса будет завершена: 

    ![Настройка Azure Load Balancer](./media/container-service-kubernetes-load-balancing/nginx-external-ip.png)

5. Убедитесь, что у вас есть доступ к службе по внешнему IP-адресу. Например, откройте веб-браузер и используйте отображаемый IP-адрес. В браузере отображается веб-сервер Nginx, запущенный в одном из контейнеров. Или выполните команду `curl` либо `wget`. Например:

    ```
    curl 13.82.93.130
    ```

    Должен отобразиться примерно такой результат:

    ![Доступ к Nginx с использованием curl](./media/container-service-kubernetes-load-balancing/curl-output.png)

6. Чтобы просмотреть конфигурацию Azure Load Balancer, перейдите на [портал Azure](https://portal.azure.com).

7. Перейдите к группе ресурсов для кластера службы контейнеров и выберите балансировщик нагрузки для виртуальных машин агента. Его имя должно совпадать с именем службы контейнеров. (Не выбирайте балансировщик нагрузки для главных узлов, имя которого содержит **master-lb**.) 

    ![Балансировщик нагрузки в группе ресурсов](./media/container-service-kubernetes-load-balancing/container-resource-group-portal.png)

8. Чтобы просмотреть сведения о конфигурации балансировщика нагрузки, выберите параметр **Правила балансировки нагрузки** и имя настроенного правила.

    ![Правила балансировщика нагрузки](./media/container-service-kubernetes-load-balancing/load-balancing-rules.png) 

### <a name="example-specify-type-loadbalancer-in-the-service-configuration-file"></a>Пример. Указание `type: LoadBalancer` в файле конфигурации службы

При развертывании приложения контейнера Kubernetes из [файла конфигурации службы](https://kubernetes.io/docs/user-guide/services/operations/#service-configuration-file) YAML или JSON укажите внешний балансировщик нагрузки, добавив следующую строку в спецификацию службы:

```YAML
 "type": "LoadBalancer"
``` 



В следующих действиях используется [пример гостевой книги](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook) Kubernetes. Этот пример является многоуровневым веб-приложением на основе образов Redis и PHP Docker. В файле конфигурации службы можно указать, что сервер переднего плана PHP использует Azure Load Balancer.

1. Скачайте файл `guestbook-all-in-one.yaml` с сайта [GitHub](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook/all-in-one). 
2. Перейдите к параметру `spec` для службы `frontend`.
3. Раскомментируйте строку `type: LoadBalancer`.

    ![Балансировщик нагрузки в конфигурации службы](./media/container-service-kubernetes-load-balancing/guestbook-frontend-loadbalance.png)

4. Сохраните файл и разверните приложение, выполнив следующую команду:

    ```
    kubectl create -f guestbook-all-in-one.yaml
    ```

5. Введите `kubectl get svc`, чтобы просмотреть сведения о состоянии служб в кластере. Пока балансировщик нагрузки настраивает правило, `EXTERNAL-IP` службы `frontend` отображается как `<pending>`. Через несколько минут настройка внешнего IP-адреса будет завершена: 

    ![Настройка Azure Load Balancer](./media/container-service-kubernetes-load-balancing/guestbook-external-ip.png)

6. Убедитесь, что у вас есть доступ к службе по внешнему IP-адресу. Например, можно открыть веб-браузер и использовать внешний IP-адрес службы.

    ![Внешний доступ к гостевой книге](./media/container-service-kubernetes-load-balancing/guestbook-web.png)

    Вы можете добавить записи гостевой книги.

7. Чтобы просмотреть конфигурацию Azure Load Balancer, перейдите к ресурсу балансировщика нагрузки для кластера на [портале Azure](https://portal.azure.com). Ознакомьтесь с действиями в предыдущем примере.

### <a name="considerations"></a>Рекомендации

* Создание правила балансировки нагрузки выполняется асинхронно, и сведения о подготовленном балансировщике нагрузки публикуются в поле `status.loadBalancer` службы.
* Каждой службе автоматически назначается собственный виртуальный IP-адрес в балансировщике нагрузки.
* Если вы хотите получить доступ к балансировщику нагрузки с помощью DNS-имени, используйте поставщик службы доменных имен, чтобы создать DNS-имя для IP-адреса правила.

## <a name="http-or-https-traffic"></a>Трафик HTTP или HTTPS

Чтобы выполнить балансировку нагрузки трафика HTTP или HTTPS в веб-приложениях контейнера и управлять сертификатами для протокола TLS, можно использовать ресурс [Ingress](https://kubernetes.io/docs/user-guide/ingress/) Kubernetes. Ingress — это коллекция правил, которые обеспечивают входящие подключения для доступа к службам кластера. Для работы ресурса Ingress в кластере Kubernetes должен быть запущен [контроллер Ingress](https://kubernetes.io/docs/user-guide/ingress/#ingress-controllers).

Служба контейнеров Azure не реализует контроллер Ingress Kubernetes автоматически. Доступны несколько реализаций контроллера. Сейчас мы рекомендуем настроить в [контроллере Ingress Nginx](https://github.com/kubernetes/contrib/blob/master/ingress/controllers/nginx/README.md) правила Ingress и выполнить балансировку нагрузки трафика HTTP и HTTPS. 

Дополнительные сведения и примеры см. в [документации по контроллеру Ingress Nginx](https://github.com/kubernetes/contrib/blob/master/ingress/controllers/nginx/README.md).

> [!IMPORTANT]
> При использовании контроллера Ingress Nginx в Службе контейнеров Azure необходимо предоставить развертывание контроллера как службу с помощью `type: LoadBalancer`. При этом Azure Load Balancer будет настроен для маршрутизации трафика в контроллер. Дополнительные сведения см. в предыдущем разделе.


## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с [документацией по Kubernetes LoadBalancer](https://kubernetes.io/docs/user-guide/load-balancer/)
* Узнайте больше о [контроллерах Ingress Kubernetes](https://kubernetes.io/docs/user-guide/ingress/)
* Ознакомьтесь с [примерами Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples)




<!--HONumber=Jan17_HO5-->


