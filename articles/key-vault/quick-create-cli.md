---
title: Краткое руководство Azure. Создание Key Vault с помощью CLI | Документация Майкрософт
description: Краткое руководство по созданию Azure Key Vault с помощью CLI
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 4acc894f-fee0-4c2f-988e-bc0eceea5eda
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/10/2018
ms.author: barclayn
ms.openlocfilehash: ae8957e5bf87fc190076db87d4eaca0e7a757c5e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34067662"
---
# <a name="quickstart-create-an-azure-key-vault-using-the-cli"></a>Краткое руководство. Создание Azure Key Vault с помощью CLI

Azure Key Vault — это облачная служба, которая работает как защищенное хранилище секретов. Вы можете безопасно хранить ключи, пароли, сертификаты и другие секреты. Дополнительные сведения о хранилище ключей см. в статье [обзора](key-vault-overview.md). Azure CLI используется для создания ресурсов Azure и управления ими с помощью скриптов и команд. В рамках этого краткого руководства вы создадите хранилище ключей. После этого вы сохраните в нем секрет.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

Чтобы войти в Azure с помощью CLI, введите следующее:

```azurecli
az login
```

Дополнительные сведения о параметрах входа через CLI см. в статье [Вход с помощью Azure CLI 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. В следующем примере создается группа ресурсов с именем *ContosoResourceGroup* в расположении *eastus*.

```azurecli
az group create --name 'ContosoResourceGroup' --location eastus
```

## <a name="create-a-key-vault"></a>создать хранилище ключей;

Затем вы создадите Key Vault в группе ресурсов, созданной на предыдущем шаге. Необходимо будет указать следующие сведения:

- В этом руководстве мы используем **Contoso-vault2**. Укажите уникальное имя в тестировании.
- Имя группы ресурсов: **ContosoResourceGroup**.
- Расположение: **восточная часть США**.

```azurecli
az keyvault create --name 'Contoso-Vault2' --resource-group 'ContosoResourceGroup' --location eastus
```

В выходных данных командлета будут показаны свойства созданного Key Vault. Запишите значения двух указанных ниже свойств.

- **Имя хранилища**. В данном примере это **Contoso-Vault2**. Вы будете использовать это имя для выполнения других команд Key Vault.
- **Код URI хранилища**. В данном примере это https://contoso-vault2.vault.azure.net/. Необходимо, чтобы приложения, использующие ваше хранилище через REST API, использовали этот URI.

На этом этапе любые операции в этом хранилище ключей может выполнять только учетная запись Azure.

## <a name="add-a-secret-to-key-vault"></a>Добавление секрета в Key Vault

Чтобы добавить секрет в хранилище, вам просто нужно выполнить несколько дополнительных шагов. Этот пароль может использоваться приложением. Пароль будет называться **ExamplePassword** и в нем будет храниться значение **Pa$$w0rd**.

Затем введите команды ниже, чтобы создать секрет в Key Vault с именем **ExamplePassword** и значением **Pa$$w0rd**:

```azurecli
az keyvault secret set --vault-name 'Contoso-Vault2' --name 'ExamplePassword' --value 'Pa$$w0rd'
```

Теперь пароль, добавленный в Azure Key Vault, можно вызвать, используя его URI. Используйте **https://ContosoVault.vault.azure.net/secrets/ExamplePassword**, чтобы получить текущую версию. 

Чтобы просмотреть содержащееся в секрете значение в виде обычного текста, введите следующее:

```azurecli
az keyvault secret show --name 'ExamplePassword' --vault-name 'Contoso-Vault2'
```

Вы создали Key Vault, сохранили в нем секрет и извлекли его.

## <a name="clean-up-resources"></a>Очистка ресурсов

Другие руководства в этой серии созданы на основе этого документа. Если вы планируете продолжить работу с последующими краткими руководствами и статьями, эти ресурсы можно не удалять.
Вы можете удалить ставшие ненужными группу ресурсов и все связанные с ней ресурсы, выполнив команду [az group delete](/cli/azure/group#delete). Удалите ресурсы следующим образом:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали Key Vault и сохранили в нем секрет. Чтобы получить дополнительные сведения о Key Vault и о том, как вы можете использовать его в своих приложениях, ознакомьтесь с руководством по веб-приложениям, работающим с Key Vault.

> [!div class="nextstepaction"]
> Чтобы узнать, как считать секрет из Key Vault с помощью веб-приложения, используя удостоверения управляемой службы, перейдите к [следующему руководству](tutorial-web-application-keyvault.md).
