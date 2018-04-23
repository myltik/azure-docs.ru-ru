---
title: Настройка подготовки устройств с помощью Azure CLI | Документация Майкрософт
description: Краткое руководство Azure. Настройка службы "Подготовка устройств к добавлению в Центр Интернета вещей" с помощью Azure CLI
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr
ms.date: 02/26/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 70248ea2bdd595e6206084ef59822ec0a7ca7d2d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Настройка службы подготовки устройств к добавлению в Центр Интернета вещей с помощью Azure CLI

Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов. В этом кратком руководстве описываются сведения об использовании Azure CLI для создания Центра Интернета вещей и службы подготовки устройств к добавлению в этот центр, а также сведения о связывании этих двух служб. 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

> [!IMPORTANT]
> Центр Интернета вещей и служба подготовки, которые вы создадите в этом кратком руководстве, будут общедоступными как конечные точки DNS, поэтому не используйте конфиденциальную информацию при изменении имен этих ресурсов.
>


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. 

В следующем примере создается группа ресурсов с именем *my-sample-resource-group* в расположении *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> В примере создается группа ресурсов в расположении западной части США. Список доступных расположений можно просмотреть, выполнив команду `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

Создайте Центр Интернета вещей с помощью команды [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create). 

В следующем примере создается Центр Интернета вещей с именем *my-sample-hub* в расположении *westus*.  

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-provisioning-service"></a>Создание службы подготовки устройств

Создайте службу подготовки устройств с помощью команды [az iot dps create](/cli/azure/iot/dps#az_iot_dps_create). 

В следующем примере создается служба подготовки устройств с именем *my-sample-dps* в расположении *westus*.  

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> В примере создается служба подготовки устройств в расположении "Западная часть США". Чтобы просмотреть список доступных расположений, выполните команду `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table`, или перейдите на страницу [Состояние Azure](https://azure.microsoft.com/status/) и введите в строке поиска "Служба подготовки устройств". В командах расположения можно указать, используя одно слово или несколько. Например: westus, West US, WEST US и т. д. Обратите внимание, что регистр не учитывается. Если для указания расположения вы используете несколько слов, укажите значение поиска в кавычках, например `-- location "West US"`.
>


## <a name="get-the-connection-string-for-the-iot-hub"></a>Получение строки подключения для Центра Интернета вещей

Необходимо связать строку подключения Центра Интернета вещей со службой подготовки устройств. Используйте команду [az iot hub show-connection-string](/cli/azure/iot/hub#az_iot_hub_show_connection_string), чтобы получить строку подключения. Ее выходные данные потребуются, чтобы задать переменную, необходимую для связывания двух ресурсов. 

В следующем примере переменной *hubConnectionString* задается значение строки подключения для первичного ключа политики *iothubowner* в центре. Другую политику можно задать с помощью параметра `--policy-name`. Для извлечения строки подключения из выходных данных команды в Azure CLI в ней используются параметры [query](/cli/azure/query-azure-cli) и [output](/cli/azure/format-output-azure-cli#tsv-output-format).

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Чтобы получить строку подключения, можно использовать команду `echo`.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Эти две команды можно использовать для узла, где работает Bash. Если вы используете локальную оболочку Windows или CMD либо узел PowerShell, вам необходимо изменить команды, чтобы использовать правильный синтаксис для этой среды.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Связывание Центра Интернета вещей со службой подготовки устройств

Свяжите Центр Интернета вещей со службой подготовки устройств с помощью команды [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az_iot_dps_linked_hub_create). 

В следующем примере связываются Центр Интернета вещей с именем *my-sample-hub* в расположении *westus* и служба подготовки устройств с именем *my-sample-dps*. В команде используется строка подключения для Центра Интернета вещей *my-sample-hub*, сохраненная в переменной *hubConnectionString* на предыдущем шаге.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

## <a name="verify-the-provisioning-service"></a>Проверка службы подготовки устройств

Получите сведения о службе подготовки устройств, выполнив команду [az iot dps show](/cli/azure/iot/dps#az_iot_dps_show).

В следующем примере возвращаются сведения о службе подготовки устройств с именем *my-sample-dps*. Связанный Центр Интернета вещей отображается в коллекции *properties.iotHubs*.

```azurecli-interactive
az iot dps show --name my-sample-dps
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Другие краткие руководства в этой коллекции созданы на основе этого документа. Если вы планируете продолжать работу с последующими краткими руководствами или обычными руководствами, не удаляйте созданные ресурсы. Если же вы не планируете продолжать работу, вы можете использовать команды ниже, чтобы удалить службу подготовки устройств, Центр Интернета вещей или группу ресурсов со всеми связанными ресурсами.

Чтобы удалить службу подготовки устройств, выполните команду [az iot dps delete](/cli/azure/iot/dps#az_iot_dps_delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Чтобы удалить Центр Интернета вещей, выполните команду [az iot hub delete](/cli/azure/iot/hub#az_iot_hub_delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Чтобы удалить группу ресурсов со всеми связанными ресурсами, выполните команду [az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Дополнительная информация

Вы развернули Центр Интернета вещей и экземпляр службы подготовки устройств, а затем связали эти два ресурса. Чтобы узнать, как подготовить виртуальное устройство, см. руководство по созданию виртуального устройства.

> [!div class="nextstepaction"]
> [Краткое руководство по созданию виртуального устройства](./quick-create-simulated-device.md)

