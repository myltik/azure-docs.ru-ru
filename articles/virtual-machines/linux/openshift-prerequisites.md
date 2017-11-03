---
title: "Предварительные требования для OpenShift в Azure | Документация Майкрософт"
description: "Необходимые условия для развертывания OpenShift в Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 0c90b8a6d17fa293b6708d942afd35e1333623cb
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2017
---
# <a name="common-prerequisites-for-openshift-in-azure"></a>Общие предварительные требования для OpenShift в Azure

При развертывании OpenShift в Azure существует несколько общих предварительных условий, независимо от того, развертываете ли вы источник OpenShift или платформу контейнеров OpenShift.

Установка OpenShift осуществляется с помощью модулей playbook Ansible. Ansible подключается ко всем узлам, которые будут частью кластера, по протоколу SSH для завершения установки.
При SSL-подключении к удаленным компьютерам невозможно ввести парольную фразу. Поэтому с закрытым ключом невозможно связать парольную фразу, так как развертывание завершится сбоем.
Поскольку все виртуальные машины развертываются с помощью шаблонов Resource Manager, для доступа к ним используется один открытый ключ. Нужно включить соответствующий закрытый ключ в виртуальную машину, на которой также выполняются все тренировочные задания.
Чтобы сделать это безопасно, используйте Azure Key Vault для передачи закрытого ключа на виртуальную машину.

Если контейнерам требуется постоянное хранилище, вам понадобятся постоянные тома. Эти постоянные тома должны поддерживаться определенным видом постоянных хранилищ. OpenShift поддерживает виртуальные диски Azure (VHD) для этой возможности, но сначала требуется настроить Azure в качестве поставщика облачных услуг. В этой модели OpenShift выполняет следующие действия:

- Создает объект виртуального жесткого диска в учетной записи хранилища Azure.
- Подключает виртуальный жесткий диск к виртуальной машине и форматирует том.
- Подключает том к модулю.

OpenShift нуждается в разрешениях для выполнения этих задач в Azure. Для этого требуется субъект-служба. Субъект-служба — это учетная запись безопасности в Azure Active Directory, которая получает разрешения на доступ к ресурсам.
Субъект-служба должна иметь доступ к учетным записям хранения и виртуальным машинам, входящим в состав кластера. При развертывании всех ресурсов кластера OpenShift в одной группе ресурсов субъект-служба может получить разрешения на доступ к этой группе ресурсов.

В этом руководстве описывается создание артефактов, связанных с необходимыми компонентами.

> [!div class="checklist"]
> * создать хранилище ключей KeyVault, чтобы управлять ключами SSH для кластера OpenShift;
> * Создайте субъект-службу для поставщика облачных служб Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="log-in-to-azure"></a>Вход в Azure 
Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране или щелкните **Попробовать**, чтобы использовать Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Рекомендуется использовать выделенную группу ресурсов для размещения хранилища ключей отдельно от группы ресурсов, в которую будут развернуты ресурсы кластера OpenShift. 

В следующем примере создается группа ресурсов с именем *keyvaultrg* в расположении *eastus*.

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.
Создайте хранилище ключей KeyVault, где будут храниться ключи SSH для кластера, с помощью команды [az keyvault create](/cli/azure/keyvault#create). Имя хранилища ключей должно быть глобально уникальным.

В следующем примере создается хранилище ключей с именем *keyvault* в группе ресурсов *keyvaultrg*.

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Создание ключа SSH 
Ключ SSH необходим для обеспечения безопасного доступа к кластеру OpenShift Origin. Создайте пару ключей SSH с помощью команды `ssh-keygen` (на компьютере Linux или Mac).
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Создаваемая пара ключей SSH не должна содержать парольную фразу.

Дополнительные сведения о ключах SSH в Windows см. в статье [Использование ключей SSH с Windows в Azure](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-ssh-private-key-in-key-vault"></a>Сохранение закрытого ключа SSH в хранилище ключей
Развертывание OpenShift использует созданный ключ SSH, чтобы защитить доступ к основному кластеру OpenShift. Чтобы обеспечить для развертывания безопасное извлечение ключа SSH, сохраните ключ в хранилище ключей с помощью команды ниже:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>Создание субъекта-службы 
OpenShift взаимодействует с Azure, используя имя пользователя и пароль или субъект-службу. Субъект-служба Azure является удостоверением безопасности, которое можно использовать с приложениями, службами и инструментами автоматизации, такими как OpenShift. Вы можете управлять разрешениями на то, какие операции может выполнять субъект-служба в Azure, и определять их. Для обеспечения более высокого уровня безопасности, чем при предоставлении имени пользователя и пароля, в этом примере создается простейший субъект-служба.

Создайте субъект-службу с помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) и выведите учетные данные, необходимые для OpenShift:

В следующем примере создается субъект-служба и назначаются разрешения участника группе ресурсов с именем myResourceGroup. Если используется Windows, выполните ```az group show --name myResourceGroup --query id``` отдельно и используйте выходные данные для передачи параметра --scopes.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

Запишите свойство appId, возвращенное из команды.
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",            
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Не используйте ненадежный пароль.  Следуйте руководству по [правилам и ограничениям для паролей Azure AD](/azure/active-directory/active-directory-passwords-policy).

Дополнительные сведения о субъект-службе см. в статье [Создание субъекта-службы Azure с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

## <a name="next-steps"></a>Дальнейшие действия

В этой статье рассматриваются следующие темы:
> [!div class="checklist"]
> * создать хранилище ключей KeyVault, чтобы управлять ключами SSH для кластера OpenShift;
> * Создайте субъект-службу для поставщика облачных служб Azure.

Теперь разверните кластер OpenShift

- [Развертывание источника OpenShift в Azure](./openshift-origin.md)
- [Развертывание платформы контейнеров OpenShift в Azure](./openshift-container-platform.md)

