---
title: "Краткое руководство по службе контейнеров Azure. Развертывание кластера DC/OS | Документы Майкрософт"
description: "Краткое руководство по службе контейнеров Azure. Развертывание кластера DC/OS"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 848c38ea9d0676c90090ee7b0bc917aec9d31b81
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017

---

# <a name="deploy-a-dcos-cluster"></a>Развертывание кластера DC/OS

DC/OS предоставляет распределенную платформу для запуска современных и контейнерных приложений. Служба контейнеров Azure упрощает и убыстряет подготовку кластера DC/OS для использования в рабочей среде. В этом кратком руководстве по началу работы описываются базовые шаги, необходимые для развертывания кластера DC/OS и запуска основной рабочей нагрузки.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

Для этого руководства требуется Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>Вход в Azure 

Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране.

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. 

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>Создание кластера DC/OS

Кластер DC/OS создается с помощью команды [az acs create](/cli/azure/acs#create).

В следующем примере создается кластер DC/OS с именем *myDCOSCluster* и ключи SSH, если они еще не существуют. Чтобы использовать определенный набор ключей, используйте параметр `--ssh-key-value`.  

```azurecli-interactive
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

Через несколько минут выполнение команды завершается, и отображаются сведения о развертывании.

## <a name="connect-to-dcos-cluster"></a>Подключение к кластеру DC/OS

После создания кластера DC/OS доступ к нему возможен через туннель SSH. Выполните следующую команду, чтобы получить общедоступный IP-адрес главного кластера DC/OS. Этот IP-адрес сохраняется в переменной и будет использоваться на следующем шаге.

```azurecli-interactive
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Чтобы создать туннель SSH, выполните следующую команду и следуйте инструкциям на экране. Если порт 80 уже используется, команда завершится с ошибкой. Измените туннелированный порт на тот, который не используется, например на `85:localhost:80`. 

```azurecli-interactive
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

Туннель SSH можно протестировать путем перехода к `http://localhost`. Если используется порт, отличный от порта 80, настройте расположение соответствующим образом. 

Если туннель SSH успешно создан, возвращается портал DC/OS.

![Пользовательский интерфейс DCOS](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>Установка интерфейса командной строки DC/OS

Интерфейс командной строки DC/OS используется для управления кластером DC/OS из командной строки. Установите интерфейс командной строки DC/OS с помощью команды [az acs dcos install-cli](/azure/acs/dcos#install-cli). Если вы используете Azure CloudShell, то интерфейс командной строки DC/OS уже установлен. 

При запуске интерфейса командной строки Azure в macOS или Linux может потребоваться выполнить эту команду с sudo.

```azurecli-interactive
az acs dcos install-cli
```

Прежде чем можно будет использовать интерфейс командной строки в кластере, необходимо настроить в нем туннель SSH. Для этого выполните следующую команду, при необходимости изменив порт.

```azurecli-interactive
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

```azurecli-interactive
dcos marathon app add marathon-app.json
```

Чтобы просмотреть состояние развертывания для приложения, выполните следующую команду.

```azurecli-interactive
dcos marathon app list
```

Когда значение столбца **WAITING** изменится с *True* на *False*, развертывание приложения завершено.

```azurecli-interactive
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

Получите общедоступный IP-адрес агентов кластера DC/OS.

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

При переходе на этот адрес возвращается сайт NGINX по умолчанию.

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>Удаление кластера DC/OS

Вы можете удалить ставшие ненужными группу ресурсов, кластер DC/OS и все связанные с ним ресурсы, выполнив команду [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве рассматривалось развертывание кластера DC/OS и запуск простого контейнера Docker в этом кластере. Дополнительные сведения о службе контейнеров Azure см. в учебниках по ACS.

> [!div class="nextstepaction"]
> [Управление кластером DC/OS ACS](container-service-dcos-manage-tutorial.md)
