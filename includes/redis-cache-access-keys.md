Чтобы подключаться к кэшу Redis для Azure, клиентам кэша нужны имя узла, порты и ключи кэша. Некоторые клиенты могут ссылаться на эти элементы с помощью незначительно различающихся имен. Вы можете получить эти сведения на портале Azure или с помощью программ командной строки, таких как Azure CLI.

### <a name="retrieve-host-name-ports-and-access-keys-using-the-azure-portal"></a>Получение имени узла, портов и ключей доступа с помощью портала Azure
Для получения имени узла, портов и ключей доступа с помощью портала Azure [перейдите](../articles/redis-cache/cache-configure.md#configure-redis-cache-settings) к сведениям о кэше на [портале Azure](https://portal.azure.com), а затем в меню **ресурсов** щелкните **Ключи доступа** и **Свойства**. 

![Параметры кэша Redis](media/redis-cache-access-keys/redis-cache-hostname-ports-keys.png)

### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Получение имени узла, портов и ключей доступа с помощью Azure CLI
Для получения имени узла и портов с помощью Azure CLI 2.0 вы можете вызвать [az redis show](https://docs.microsoft.com/cli/azure/redis#show), а для получения ключей — [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#list-keys). Следующий скрипт вызывает эти две команды и выводит сведения об имени узла, портах и ключах в консоль.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Redis Cache instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Redis Cache instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Дополнительные сведения об этом скрипте см. в статье [Get the hostname, ports, and keys for Azure Redis Cache](../articles/redis-cache/scripts/cache-keys-ports.md) (Получение имени узла, портов и ключей для кэша Redis Azure). Дополнительные сведения об Azure CLI 2.0 см. в статье [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Установка Azure CLI 2.0) и [Get started with Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) (Приступая к работе с Azure CLI 2.0).
