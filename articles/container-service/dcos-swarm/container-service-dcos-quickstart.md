---
title: Краткое руководство по службе контейнеров Azure. Развертывание кластера DC/OS
description: Краткое руководство по службе контейнеров Azure. Развертывание кластера DC/OS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 9893606943324f5a651cc800feeb86b8966dc15e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-a-dcos-cluster"></a>Развертывание кластера DC/OS

DC/OS предоставляет распределенную платформу для запуска современных и контейнерных приложений. Служба контейнеров Azure упрощает и убыстряет подготовку кластера DC/OS для использования в рабочей среде. В этом кратком руководстве по началу работы описываются базовые шаги, необходимые для развертывания кластера DC/OS и запуска основной рабочей нагрузки.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

Для этого руководства требуется Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Вход в Azure 

Войдите в подписку Azure с помощью команды [az login](/cli/azure/reference-index#az_login) и следуйте инструкциям на экране.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. 

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>Создание кластера DC/OS

Кластер DC/OS создается с помощью команды [az acs create](/cli/azure/acs#az_acs_create).

В следующем примере создается кластер DC/OS с именем *myDCOSCluster* и ключи SSH, если они еще не существуют. Чтобы использовать определенный набор ключей, используйте параметр `--ssh-key-value`.  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

В некоторых случаях, например при использовании ограниченной пробной версии, доступ подписки Azure к ресурсам Azure ограничен. Если происходит сбой развертывания из-за ограничения доступных ядер, уменьшите количество агентов по умолчанию, добавив `--agent-count 1` в команду [az acs create](/cli/azure/acs#az_acs_create). 

Через несколько минут выполнение команды завершается, и отображаются сведения о развертывании.

## <a name="connect-to-dcos-cluster"></a>Подключение к кластеру DC/OS

После создания кластера DC/OS доступ к нему возможен через туннель SSH. Выполните следующую команду, чтобы получить общедоступный IP-адрес главного кластера DC/OS. Этот IP-адрес сохраняется в переменной и будет использоваться на следующем шаге.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Чтобы создать туннель SSH, выполните следующую команду и следуйте инструкциям на экране. Если порт 80 уже используется, команда завершится с ошибкой. Измените туннелированный порт на тот, который не используется, например на `85:localhost:80`. 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

Туннель SSH можно протестировать путем перехода к `http://localhost`. Если используется порт, отличный от порта 80, настройте расположение соответствующим образом. 

Если туннель SSH успешно создан, возвращается портал DC/OS.

![Пользовательский интерфейс DCOS](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>Установка интерфейса командной строки DC/OS

Интерфейс командной строки DC/OS используется для управления кластером DC/OS из командной строки. Установите интерфейс командной строки DC/OS с помощью команды [az acs dcos install-cli](/azure/acs/dcos#install-cli). Если вы используете Azure CloudShell, то интерфейс командной строки DC/OS уже установлен. 

При запуске интерфейса командной строки Azure в macOS или Linux может потребоваться выполнить эту команду с sudo.

```azurecli
az acs dcos install-cli
```

Прежде чем можно будет использовать интерфейс командной строки в кластере, необходимо настроить в нем туннель SSH. Для этого выполните следующую команду, при необходимости изменив порт.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Запуск приложения

Механизм планирования по умолчанию для кластера DC/OS ACS — Marathon. Marathon используется для запуска приложения и управления состоянием приложения в кластере DC/OS. Чтобы планировать приложение с помощью Marathon, создайте файл с именем *marathon app.json* и скопируйте в него следующее. 

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

Выполните следующую команду для планирования работы приложения в кластере DC/OS.

```azurecli
dcos marathon app add marathon-app.json
```

Чтобы просмотреть состояние развертывания для приложения, выполните следующую команду.

```azurecli
dcos marathon app list
```

Когда значение столбца **WAITING** изменится с *True* на *False*, развертывание приложения завершено.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

Получите общедоступный IP-адрес агентов кластера DC/OS.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

При переходе на этот адрес возвращается сайт NGINX по умолчанию.

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>Удаление кластера DC/OS

Вы можете удалить ставшие ненужными группу ресурсов, кластер DC/OS и все связанные с ним ресурсы, выполнив команду [az group delete](/cli/azure/group#az_group_delete).

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве рассматривалось развертывание кластера DC/OS и запуск простого контейнера Docker в этом кластере. Дополнительные сведения о службе контейнеров Azure см. в учебниках по ACS.

> [!div class="nextstepaction"]
> [Управление кластером DC/OS ACS](container-service-dcos-manage-tutorial.md)