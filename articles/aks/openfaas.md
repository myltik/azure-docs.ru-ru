---
title: Использование OpenFaaS со Службой Azure Kubernetes (AKS)
description: Развертывание и использование OpenFaaS со Службой Azure Kubernetes (AKS)
services: container-service
author: justindavies
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: e26f1c298b05153736edd2b2efd0f1b27162bc3d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="using-openfaas-on-aks"></a>Использование OpenFaaS в AKS

[OpenFaaS][open-faas] — это платформа для создания бессерверных функций на основе контейнеров. Как проект с открытым кодом она стала очень популярной в сообществе. В этом документе описано, как установить и использовать OpenFaas в кластере Службы Azure Kubernetes (AKS).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Базовое представление о Kubernetes.
* Кластер Службы Azure Kubernetes (AKS) и настроенные учетные данные AKS в системе разработки.
* Установленный компонент Azure CLI в системе разработки.
* Средства командной строки Git, установленные в системе.

## <a name="get-openfaas"></a>Получение OpenFaaS

Клонируйте репозиторий проектов OpenFaaS в свою систему разработки.

```azurecli-interactive
git clone https://github.com/openfaas/faas-netes
```

Перейдите в каталог клонированного репозитория.

```azurecli-interactive
cd faas-netes
```

## <a name="deploy-openfaas"></a>Развертывание OpenFaaS

Рекомендуется, чтобы OpenFaaS и функции OpenFaaS хранились в собственном пространстве имен Kubernetes.

Создайте пространство имен для системы OpenFaaS.

```azurecli-interactive
kubectl create namespace openfaas
```

Создайте второе пространство имен для функций OpenFaaS.

```azurecli-interactive
kubectl create namespace openfaas-fn
```

Диаграмма Helm для OpenFaaS будет включена в клонированный репозиторий. С помощью этой диаграммы разверните OpenFaaS в кластере AKS.

```azurecli-interactive
helm install --namespace openfaas -n openfaas \
  --set functionNamespace=openfaas-fn, \
  --set serviceType=LoadBalancer, \
  --set rbac=false chart/openfaas/
```

Выходные данные:

```
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

  kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Для получения доступа к шлюзу OpenFaaS создается общедоступный IP-адрес. Чтобы получить этот IP-адрес, используйте команду [kubectl get service][kubectl-get]. Процесс назначения службе IP-адреса может занять около минуты.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Выходные данные.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Чтобы протестировать систему OpenFaaS, перейдите к внешнему IP-адресу на порту 8080 (в этом примере — `http://52.186.64.52:8080`).

![Пользовательский интерфейс OpenFaaS](media/container-service-serverless/openfaas.png)

Установите интерфейс командной строки (CLI) OpenFaaS. В этом примере используется brew. Дополнительные варианты см. в [документации по OpenFaaS CLI][open-faas-cli].

```console
brew install faas-cli
```

## <a name="create-first-function"></a>Создание первой функции

Теперь, когда OpenFaaS можно использовать, создайте функцию с помощью портала OpenFaas.

Щелкните **Deploy New Function** (Развернуть новую функцию) и выполните поиск по слову **Figlet**. Выберите функцию Figlet и нажмите кнопку **Deploy** (Развернуть).

![Figlet](media/container-service-serverless/figlet.png)

Для вызова функции используйте curl. Замените IP-адрес в следующем примере IP-адресом шлюза OpenFaas.

```azurecli-interactive
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Выходные данные:

```console
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Создание второй функции

Теперь создайте вторую функцию. Этот пример будет развернут с помощью OpenFaaS CLI. Он включает настраиваемый образ контейнера и предусматривает получение данных из Cosmos DB. Перед созданием функции необходимо настроить несколько элементов.

Сначала создайте группу ресурсов для Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Разверните экземпляр CosmosDB вида `MongoDB`. Этому экземпляру требуется уникальное имя. Обновите `openfaas-cosmos` уникальным значением для вашей среды.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Получите строку подключения к базе данных Cosmos и сохраните ее в переменной.

Обновите значение аргумента `--resource-group` именем вашей группы ресурсов, а значение аргумента `--name` — именем базы данных Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Теперь заполните базу данных Cosmos DB тестовыми данными. Создайте файл с именем `plans.json` и скопируйте в него следующий код JSON.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Используйте средство *mongoimport* для загрузки экземпляра CosmosDB с данными.

При необходимости установите средства MongoDB. В следующем примере эти средства устанавливаются с помощью brew. Другие варианты см. в [документации MongoDB][install-mongo].

```azurecli-interactive
brew install mongodb
```

Загрузите данные в базу данных.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Выходные данные:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Выполните следующую команду для создания функции. Обновите значение аргумента `-g` адресом своего шлюза OpenFaaS.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

После развертывания можно будет увидеть только что созданную конечную точку OpenFaaS для функции.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Протестируйте функцию с помощью curl. Обновите IP-адрес адресом шлюза OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Выходные данные:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Также функцию можно протестировать в пределах пользовательского интерфейса OpenFaaS.

![замещающий текст](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Дальнейшие действия

Развертывание OpenFaas по умолчанию необходимо заблокировать как для шлюза, так и для функций OpenFaaS. Дополнительные сведения о параметрах защищенной конфигурации см. в [блоге Алекса Эллиса (Alex Ellis)](https://blog.alexellis.io/lock-down-openfaas/).

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli