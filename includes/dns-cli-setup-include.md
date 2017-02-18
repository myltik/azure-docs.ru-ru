## <a name="set-up-azure-cli-for-azure-dns"></a>Настройка интерфейса командной строки Azure для Azure DNS

### <a name="before-you-begin"></a>Перед началом работы

Перед началом настройки убедитесь, что у вас есть следующие компоненты.

* Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
* Установите последнюю версию интерфейса командной строки Azure для Windows, Linux или Mac. Дополнительные сведения см. в статье [Установка Azure CLI](../articles/xplat-cli-install.md).

### <a name="sign-in-to-your-azure-account"></a>Вход в учетную запись Azure

Откройте окно консоли и пройдите проверку подлинности с помощью своих учетных данных. Дополнительную информацию см. в статье о [входе в Azure из командной строки Azure](../articles/xplat-cli-connect.md).

```azurecli
azure login
```

### <a name="switch-cli-mode"></a>Переключение режима интерфейса командной строки

Azure DNS использует диспетчер ресурсов Azure. Обязательно переключите интерфейс командной строки (CLI) в режим использования команд Azure Resource Manager.

```azurecli
azure config mode arm
```

### <a name="select-the-subscription"></a>Выбор подписки

Просмотрите подписки учетной записи.

```azurecli
azure account list
```

Выберите подписку Azure.

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Но так как все ресурсы DNS глобальные, а не региональные, выбор расположения группы ресурсов не влияет на Azure DNS.

Если используется существующая группа ресурсов, можно пропустить этот шаг.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>Регистрация поставщика ресурсов

Служба Azure DNS управляется поставщиком ресурсов Microsoft.Network. Вашу подписку Azure необходимо зарегистрировать, чтобы использовать этот поставщик ресурсов, прежде чем работать с Azure DNS. Эта операция выполняется один раз для каждой подписки.

```azurecli
azure provider register --namespace Microsoft.Network
```



<!--HONumber=Jan17_HO1-->


