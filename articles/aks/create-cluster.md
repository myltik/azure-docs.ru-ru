---
title: Создание кластера службы Azure Kubernetes (AKS)
description: Созадние кластера AKS с помощью интерфейса командной строки или портала Azure.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 46c2f718911f27ad8f51423589c6fdda100a68d2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Создание кластера службы Azure Kubernetes (AKS)

Кластер Службы Azure Kubernetes (AKS) можно создать с помощью портала Azure или Azure CLI.

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Используйте команду [az aks create][az-aks-create], чтобы создать кластер AKS.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Для команды `az aks create` доступны следующие параметры.

| Аргумент | ОПИСАНИЕ | Обязательно |
|---|---|:---:|
| `--name` `-n` | Имя ресурса для управляемого кластера. | Да |
| `--resource-group` `-g` | Имя группы ресурсов Службы Azure Kubernetes. | Да |
| `--admin-username` `-u` | Имя пользователя для службы "Виртуальные машины Linux".  По умолчанию: azureuser. | Нет |
| ` --client-secret` | Секрет, связанный с субъектом-службой. | Нет |
| `--dns-name-prefix` `-p` | Префикс DNS для общедоступного IP-адреса кластера. | Нет |
| `--generate-ssh-keys` | Создание файлов открытого и закрытого ключей SSH, если они отсутствуют. | Нет |
| `--kubernetes-version` `-k` | Версия Kubernetes, используемая для создания кластера, например 1.7.9 или 1.8.2.  По умолчанию: 1.7.7. | Нет |
| `--no-wait` | Не ожидать завершения длительной операции. | Нет |
| `--node-count` `-c` | Количество узлов по умолчанию в пулах узлов.  По умолчанию: 3. | Нет |
| `--node-osdisk-size` | Размер диска ОС (в ГБ) виртуальной машины пула узлов. | Нет |
| `--node-vm-size` `-s` | Размер виртуальной машины.  По умолчанию: Standard_D1_v2. | Нет |
| `--service-principal` | Субъект-служба, используемый для аутентификации кластера. | Нет |
| `--ssh-key-value` | Значение файла ключа SSH или путь к файлу ключа.  По умолчанию: ~ /.ssh/id_rsa.pub. | Нет |
| `--tags` | Разделенные пробелами теги в формате "key[=value]". Используйте '' для очистки существующих тегов. | Нет |

## <a name="azure-portal"></a>Портал Azure

См. дополнительные сведения о [развертывании кластера Службе Azure Kubernetes (AKS) с помощью портала Azure].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
