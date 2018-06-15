---
title: Установка и настройка Ansible для использования с виртуальными машинами Azure | Документы Майкрософт
description: Узнайте, как установить и настроить Ansible для управления ресурсами Azure в Ubuntu, CentOS и SLES.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/04/2018
ms.author: iainfou
ms.openlocfilehash: c00ebcb771081f8e35c67bf384f5f6822e16f268
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652997"
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Установка и настройка Ansible для управления виртуальными машинами в Azure

Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. Ansible можно использовать для управления виртуальными машинами в Azure так же, как любым другим ресурсом. В этой статье описывается установка Ansible и необходимых модулей пакета SDK Azure Python для некоторых из наиболее распространенных дистрибутивов Linux. Чтобы установить Ansible в других дистрибутивах, настройте установленные пакеты в соответствии с платформой. Вы также узнаете, как создавать и определять учетные данные, используемые Ansible для безопасного создания ресурсов Azure.

Сведения о дополнительных параметрах установки и инструкции для других платформ см. в [руководстве по установке Ansible](https://docs.ansible.com/ansible/intro_installation.html).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.30 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="install-ansible"></a>Установка Ansible

Один из самых простых способов использования Ansible с Azure заключается в применении Azure Cloud Shell — оболочки на основе браузера, которая позволяет разрабатывать ресурсы Azure и управлять ими. Система Ansible предварительно установлена в Cloud Shell, поэтому можно пропустить этап ее установки и перейти к [созданию учетных данных Azure](#create-azure-credentials). Список дополнительных средств, которые также доступны в Cloud Shell, см. в разделе [Средства](../../cloud-shell/features.md#tools).

Ниже показано, как создать виртуальную машину Linux для различных дистрибутивов, а затем установить Ansible. Если создавать виртуальную машину Linux не требуется, пропустите этап создания группы ресурсов Azure. В противном случае сначала создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Теперь выберите один из следующих дистрибутивов для выполнения шагов по созданию виртуальной машины, если это необходимо, а затем установите Ansible:

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Если необходимо, создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create). В приведенном ниже примере создается виртуальная машина с именем *myVMAnsible*.

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Подключитесь по SSH к виртуальной машине, используя адрес `publicIpAddress`, указанный в выходных данных операции по созданию виртуальной машины.

```bash
ssh azureuser@<publicIpAddress>
```

Установите в виртуальной машине необходимые пакеты модулей SDK для Azure Python и Ansible следующим образом:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Теперь перейдите к разделу [Создание учетных данных Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Если необходимо, создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create). В приведенном ниже примере создается виртуальная машина с именем *myVMAnsible*.

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Подключитесь по SSH к виртуальной машине, используя адрес `publicIpAddress`, указанный в выходных данных операции по созданию виртуальной машины.

```bash
ssh azureuser@<publicIpAddress>
```

Установите в виртуальной машине необходимые пакеты модулей SDK для Azure Python и Ansible следующим образом:

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
pip install ansible[azure]
```

Теперь перейдите к разделу [Создание учетных данных Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Если необходимо, создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create). В приведенном ниже примере создается виртуальная машина с именем *myVMAnsible*.

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Подключитесь по SSH к виртуальной машине, используя адрес `publicIpAddress`, указанный в выходных данных операции по созданию виртуальной машины.

```bash
ssh azureuser@<publicIpAddress>
```

Установите в виртуальной машине необходимые пакеты модулей SDK для Azure Python и Ansible следующим образом:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Теперь перейдите к разделу [Создание учетных данных Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Создание учетных данных Azure

Ansible взаимодействует с Azure, используя имя пользователя и пароль или субъект-службу. Субъект-служба Azure является удостоверением безопасности, которое можно использовать с приложениями, службами и средствами автоматизации, такими как Ansible. Вы можете управлять разрешениями на то, какие операции может выполнять субъект-служба в Azure, и определять их. Для обеспечения более высокого уровня безопасности, чем при предоставлении имени пользователя и пароля, в этом примере создается простейший субъект-служба.

Создайте на главном компьютере или в Azure Cloud Shell субъект-службу с помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). Учетные данные, необходимые для Ansible выводятся на экран:

```azurecli-interactive
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

Ниже приведен пример выходных данных предыдущей команды.

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Чтобы проверить подлинность в Azure, также необходимо получить идентификатор подписки Azure с помощью команды [az account show](/cli/azure/account#az-account-show):

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

Используйте выходные данные этих двух команд на следующем шаге.

## <a name="create-ansible-credentials-file"></a>Создание файла учетных данных Ansible

Чтобы предоставить учетные данные для Ansible, необходимо определить переменные среды или создать локальный файл учетных данных. Дополнительные сведения об определении учетных данных Ansible см. в разделе [предоставление учетных данных для модулей Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

В среде разработки создайте файл *учетных данных* Ansible в виртуальной машине узла. Создайте файл учетных данных на виртуальной машине, куда на предыдущем шаге была установлена система Ansible:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

В файле *учетных данных* объединяются идентификатор подписки и выходные данные, полученные в результате создания субъекта-службы. Выходные данные предыдущей команды [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) те же, что и для *client_id*, *secret* и *tenant*. В приведенном ниже примере файла *учетных данных* показаны значения, соответствующие выходным данным предыдущей команды. Введите свои значения следующим образом:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```

Сохраните и закройте файл.

## <a name="use-ansible-environment-variables"></a>Использование переменных среды Ansible

Если вы собираетесь использовать такие средства, как Ansible Tower или Jenkins, необходимо определить переменные среды. Этот шаг можно пропустить, если вы просто собираетесь использовать клиент Ansible и файл учетных данных Azure, созданный на предыдущем шаге. Переменные среды определяют ту же информацию, что и файл учетных данных Azure:

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Дополнительная информация

Теперь Ansible и необходимые модули SDK для Azure Python установлены, а учетные данные для Ansible определены. Узнайте, как [создать виртуальную машину с Ansible](ansible-create-vm.md). Вы также можете узнать, как [создать готовую виртуальную машину Azure и вспомогательные ресурсы с помощью Ansible](ansible-create-complete-vm.md).
