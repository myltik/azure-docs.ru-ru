## <a name="create-a-service-principal"></a>Создание субъекта-службы

Чтобы создать субъект-службу с доступом к реестру контейнеров, используйте приведенный ниже скрипт. Замените значение переменной `ACR_NAME` именем реестра контейнеров, а также (при необходимости) значение `--role` в команде [az ad sp create-for-rbac][az-ad-sp-create-for-rbac], чтобы предоставить другие разрешения. Для использования этого скрипта нужно установить [Azure CLI](/cli/azure/install-azure-cli).

После запуска скрипта запишите **идентификатор** и **пароль** субъекта-службы. Сохранив эти учетные данные, вы можете настроить приложения и службы для аутентификации в качестве субъектов-служб в реестре контейнеров.

[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Использование существующего субъекта-службы

Чтобы предоставить субъекту-службе доступ к реестру, нужно назначить новую роль субъекту-службе. Как и при создании субъекта-службы, вы можете предоставить разрешения на извлечение данных, отправку и извлечение данных, а также разрешения владельца.

В следующем скрипте используется команда [az role assignment create][az-role-assignment-create], чтобы предоставить разрешения на *извлечение* субъекту-службе, указанному в переменной `SERVICE_PRINCIPAL_ID`. Измените значение `--role`, если нужно предоставить другой уровень доступа.

[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

Оба эти сценария можно найти на сайте GitHub, включая версии для PowerShell.

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
