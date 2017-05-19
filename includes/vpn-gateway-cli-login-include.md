Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране. Дополнительные сведения о входе в систему см. в статье [Get started with Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) (Приступая к работе с Azure CLI 2.0).

```azurecli
az login
```

Если у вас есть несколько подписок Azure, укажите подписки для этой учетной записи.

```azurecli
az account list --all
```

Укажите подписку, которую нужно использовать.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```