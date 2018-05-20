---
title: Надстройка для маршрутизации приложений HTTP в Службе контейнеров Azure
description: Использование надстройки для маршрутизации приложений HTTP в Службе контейнеров Azure
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/25/2018
ms.author: laevenso
ms.openlocfilehash: 1cf1ffc5038b32232b42152e62cca9d20b3cc57b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="http-application-routing"></a>Маршрутизация приложений HTTP

Решение маршрутизации приложений HTTP упрощает доступ к приложениям, развернутым в кластере Службы контейнеров Azure (AKS). Если включено, решение настраивает контроллер входящего трафика в кластере AKS. Кроме того, когда приложения развернуты, решение также создает общедоступные DNS-имена для конечных точек приложения.

При включении этой надстройки в вашей подписке создается зона DNS. Дополнительные сведения о стоимости DNS см. [на этой странице][dns-pricing].

## <a name="http-routing-solution-overview"></a>Обзор решения маршрутизации HTTP-трафика

Надстройка развертывает два компонента: [контроллер входящего трафика Kubernetes][ingress] и [внешний контроллер DNS][external-dns].

- **Контроллер входящего трафика.** Доступ к контроллеру входящего трафика в Интернете предоставляется с помощью службы Kubernetes типа LoadBalancer. Контроллер входящего трафика отслеживает и реализует [ресурсы входящего трафика Kubernetes][ingress-resource], которые создают маршруты к конечным точкам приложения.
- **Внешний контроллер DNS.** Отслеживает ресурсы входящего трафика Kubernetes и создает записи A DNS в зоне DNS, связанной с кластером.

## <a name="deploy-http-routing"></a>Развертывание маршрутизации HTTP-трафика

Надстройку для маршрутизации приложений HTTP можно активировать на портале Azure при развертывании кластера AKS.

![Включение функции маршрутизации HTTP-трафика](media/http-routing/create.png)

После развертывания кластера перейдите к автоматически созданной группе ресурсов AKS и выберите зону DNS. Запишите имя зоны DNS. Оно потребуется при развертывании приложений в кластер AKS.

![Получение имени зоны DNS](media/http-routing/dns.png)

## <a name="use-http-routing"></a>Использование маршрутизации HTTP-трафика

Решение маршрутизации приложений HTTP можно активировать только в ресурсах входящего трафика, которые помечены следующим образом:

```
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Создайте файл `samples-http-application-routing.yaml` и скопируйте в него следующий код YAML. В строке 43 обновите `<CLUSTER_SPECIFIC_DNS_ZONE>`, указав имя зоны DNS, полученное на последнем этапе этого документа.


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: party-clippy
spec:
  template:
    metadata:
      labels:
        app: party-clippy
    spec:
      containers:
      - image: r.j3ss.co/party-clippy
        name: party-clippy
        tty: true
        command: ["party-clippy"]
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: party-clippy
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: party-clippy
  type: ClusterIP
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: party-clippy
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: party-clippy.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: party-clippy
          servicePort: 80
        path: /
```

Выполните команду [kubectl create][kubectl-create], чтобы создать ресурсы.

```
$ kubectl create -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

С помощью cURL или браузера перейдите к имени узла, указанном в разделе узла файла `samples-http-application-routing.yaml`. Прежде чем приложение станет доступным через Интернет, может пройти около одной минуты.

```
$ curl party-clippy.471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io

 _________________________________
/ It looks like you're building a \
\ microservice.                   /
 ---------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |/
    || ||
    |\_/|
    \___/

```

## <a name="troubleshooting"></a>Устранение неполадок

Выполните команду [kubectl logs][kubectl-logs], чтобы просмотреть журналы приложений для внешнего приложения DNS. В журналах должно быть указано, что записи A и TXT DNS успешно созданы.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Эти записи также можно увидеть в ресурсе зоны DNS на портале Azure.

![Получение записей DNS](media/http-routing/clippy.png)

Выполните команду [kubectl logs][kubectl-logs], чтобы просмотреть журналы приложений для контроллера входящего трафика NGINX. В журналах должно быть указано, что ресурс входящего трафика создан и контроллер перезагружен. Все действия по протоколу HTTP регистрируются в журнале.

```
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"party-clippy", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/party-clippy
W0426 21:51:57.908771       9 controller.go:775] service default/party-clippy does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-party-clippy-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="cleanup"></a>Очистка

Удалите связанные объекты Kubernetes, созданные на этом шаге.

```
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>Дополнительная информация

Сведения об установке контроллера защищенного входящего HTTPS-трафика в AKS см. в статье [Входящий трафик HTTPS в Службе контейнеров Azure (AKS)][ingress-https].

<!-- LINKS - internal -->
[ingress-https]: ./ingress.md

<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource