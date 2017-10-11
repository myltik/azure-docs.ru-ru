---
title: "Развертывание OpenShift Origin в Azure | Документация Майкрософт"
description: "Узнайте, как развернуть OpenShift Origin на виртуальных машинах Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jbinder
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: jbinder
ms.openlocfilehash: e03da05625e440eab29ccc28a2343d3433fc7607
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-openshift-origin-to-azure-virtual-machines"></a>Развертывание OpenShift Origin на виртуальных машинах Azure 

[OpenShift Origin](https://www.openshift.org/) — это платформа контейнера с открытым исходным кодом, созданная на основе [Kubernetes](https://kubernetes.io/). Она упрощает развертывание, масштабирование и эксплуатацию приложений с несколькими клиентами. 

В этом руководстве описывается развертывание OpenShift Origin на виртуальных машинах Azure с помощью шаблонов Azure CLI и Azure Resource Manager. Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создать хранилище ключей KeyVault, чтобы управлять ключами SSH для кластера OpenShift;
> * развернуть кластер OpenShift на виртуальных машинах Azure; 
> * установить и настроить [интерфейс командной строки OpenShift](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) для управления кластером;
> * настроить развертывание OpenShift.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

Для работы с этим кратким руководством требуется Azure CLI версии не ниже 2.0.8. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>Вход в Azure 
Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране или щелкните **Попробовать**, чтобы использовать Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. 

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-key-vault"></a>создать хранилище ключей;
Создайте хранилище ключей KeyVault, где будут храниться ключи SSH для кластера, с помощью команды [az keyvault create](/cli/azure/keyvault#create).  

```azurecli 
az keyvault create --resource-group myResourceGroup --name myKeyVault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Создание ключа SSH 
Ключ SSH необходим для обеспечения безопасного доступа к кластеру OpenShift Origin. Создайте пару ключей SSH с помощью команды `ssh-keygen`. 
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Создаваемая пара ключей SSH не должна содержать парольную фразу.

Дополнительные сведения о ключах SSH в Windows см. в статье [Использование ключей SSH с Windows в Azure](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-ssh-private-key-in-key-vault"></a>Сохранение закрытого ключа SSH в Key Vault
Развертывание OpenShift использует созданный ключ SSH, чтобы защитить доступ к основному кластеру OpenShift. Чтобы обеспечить для развертывания безопасное извлечение ключа SSH, сохраните ключ в Key Vault с помощью команды ниже:

# <a name="enabled-for-template-deployment"></a>Включение для развертывания шаблона
```azurecli
az keyvault secret set --vault-name KeyVaultName --name OpenShiftKey --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>Создание субъекта-службы 
OpenShift взаимодействует с Azure, используя имя пользователя и пароль или субъект-службу. Субъект-служба Azure является удостоверением безопасности, которое можно использовать с приложениями, службами и инструментами автоматизации, такими как OpenShift. Вы можете управлять разрешениями на то, какие операции может выполнять субъект-служба в Azure, и определять их. Для обеспечения более высокого уровня безопасности, чем при предоставлении имени пользователя и пароля, в этом примере создается базовая субъект-служба.

Создайте субъект-службу с помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) и выведите учетные данные, необходимые для OpenShift:

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {strong password} \
          --scopes $(az group show --name myResourceGroup --query id)
```
Запишите свойство appId, возвращенное из команды.
```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {strong password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Не используйте ненадежный пароль.  Следуйте руководству по [правилам и ограничениям для паролей Azure AD](/azure/active-directory/active-directory-passwords-policy).

Дополнительные сведения о субъект-службе см. в статье [Создание субъекта-службы Azure с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

## <a name="deploy-the-openshift-origin-template"></a>Развертывание шаблона OpenShift Origin
Далее развернем OpenShift Origin с помощью шаблона Azure Resource Manager. 

> [!NOTE] 
> Для выполнения следующей команды необходим интерфейс командной строки версии не ниже 2.0.8. Узнать свою версию CLI вы можете с помощью команды `az --version`. Чтобы обновить версию CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

Используйте значение `appId` созданной ранее субъект-службы для параметра `aadClientId`.

```azurecli 
az group deployment create --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --params \ 
        openshiftMasterPublicIpDnsLabel=myopenshiftmaster \
        infraLbPublicIpDnsLabel=myopenshiftlb \
        openshiftPassword=Pass@word!
        sshPublicKey=~/.ssh/openshift_rsa.pub \
        keyVaultResourceGroup=myResourceGroup \
        keyVaultName=myKeyVault \
        keyVaultSecret=OpenShiftKey \
        aadClientId={appId} \
        aadClientSecret={strong password} 
```
Развертывание может занять до 20 минут. После завершения развертывания в терминале появятся сведения об URL-адресе консоли OpenShift, а также DNS-имя основного кластера OpenShift.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ocpadmin@myopenshiftmaster.cloudapp.azure.com"
}
```
## <a name="connect-to-the-openshift-cluster"></a>Подключение к кластеру OpenShift
После завершения развертывания подключитесь к консоли OpenShift через браузер с помощью `OpenShift Console Uri`. Кроме того, вы можете подключиться к основному кластеру OpenShift, используя команду ниже:

```bash
$ ssh ocpadmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Очистка ресурсов
Вы можете удалить ставшие ненужными группу ресурсов, кластер OpenShift и все связанные с ним ресурсы, выполнив команду [az group delete](/cli/azure/group#delete).

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

В рамках этого руководства вы узнали, как выполнять следующие задачи:
> [!div class="checklist"]
> * создать хранилище ключей KeyVault, чтобы управлять ключами SSH для кластера OpenShift;
> * развернуть кластер OpenShift на виртуальных машинах Azure; 
> * установить и настроить [интерфейс командной строки OpenShift](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) для управления кластером.

Теперь, когда вы развернули кластер OpenShift Origin, можно ознакомиться со следующими руководствами, чтобы узнать, как развернуть свое первое приложение, а также как использовать инструменты OpenShift. Ознакомьтесь с [обзором OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html). 
