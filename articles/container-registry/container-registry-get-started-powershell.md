---
title: "Репозитории реестра контейнеров Azure | Документация Майкрософт"
description: "Использование репозиториев реестра контейнеров Azure для образов Docker"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: cristyg
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 1e5d5ea5b1ec121fe008abc48178b1d58f540ce1
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-powershell"></a>Создание частного реестра контейнеров Docker с помощью Azure PowerShell
Команды в [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview) позволяют создать реестр контейнеров и управлять его параметрами с компьютера Windows. Кроме того, эти действия можно выполнять на [портале Azure](container-registry-get-started-portal.md), в [интерфейсе командной строки Azure](container-registry-get-started-azure-cli.md) или программными средствами с помощью [API REST](https://go.microsoft.com/fwlink/p/?linkid=834376) реестра контейнеров.


* Общие сведения и основные понятия см. в статье [Общие сведения о службе реестра контейнеров Azure](container-registry-intro.md).
* Полный список поддерживаемых командлетов см. в разделе [Командлеты управления реестром контейнеров Azure](https://docs.microsoft.com/en-us/powershell/module/azurerm.containerregistry/).


## <a name="prerequisites"></a>Предварительные требования
* **Azure PowerShell.** Инструкции по установке и началу работы с Azure PowerShell см. [здесь](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps). Войдите в свою подписку Azure, выполнив команду `Login-AzureRMAccount`. Дополнительные сведения см. в статье [Начало работы с Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/get-started-azurep).
* **Группа ресурсов.** Перед созданием реестра контейнеров создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md#resource-groups) или используйте имеющуюся. Группа ресурсов должна находиться в расположении, где [доступна](https://azure.microsoft.com/regions/services/) служба реестра контейнеров. Сведения о создании группы ресурсов с помощью Azure PowerShell см. в [справочнике по PowerShell](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps#create-a-resource-group).
* **Учетная запись хранения (необязательно).** Создайте стандартную [учетную запись хранения](../storage/common/storage-introduction.md) Azure для хранения сведений реестра контейнеров в том же расположении. Учетную запись хранения (если она не задана при создании реестра) можно создать с помощью команды `New-AzureRMContainerRegistry`. Сведения о создании учетной записи хранения с помощью Azure PowerShell см. в [справочнике по PowerShell](https://docs.microsoft.com/en-us/powershell/module/azure/new-azurestorageaccount). Хранилище класса Premium сейчас не поддерживается.
* **Субъект-служба (необязательно).** Если реестр создан с помощью PowerShell, по умолчанию доступ к нему не предоставляется. В зависимости от ваших требований можно назначить для реестра существующий субъект-службу Azure Active Directory или создать и назначить новый. Кроме того, можно включить учетную запись администратора реестра. См. следующие разделы этой статьи. Дополнительные сведения о доступе к реестру см. в статье [Authenticate with the container registry](container-registry-authentication.md) (Проверка подлинности с помощью реестра контейнеров).

## <a name="create-a-container-registry"></a>Создание реестра контейнеров
Чтобы создать реестр контейнеров, выполните команду `New-AzureRMContainerRegistry`.

> [!TIP]
> При создании реестра укажите глобально уникальное доменное имя верхнего уровня, содержащее только буквы и цифры. В примерах в качестве имени реестра используется имя `MyRegistry`, но его нужно заменить собственным.
>
>

Следующая команда создает реестр контейнеров `MyRegistry` в группе ресурсов `MyResourceGroup` в юго-центральном регионе США. При этом используются минимальные параметры.

```PowerShell
$Registry = New-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

* `-StorageAccountName` является необязательным. Если это не определено, учетная запись хранения создается с именем, состоящим из имени реестра и метки времени в указанной группе ресурсов.

## <a name="assign-a-service-principal"></a>Назначение субъекта-службы
Команды PowerShell позволяют назначать [субъект-службу](../azure-resource-manager/resource-group-authenticate-service-principal.md) Azure Active Directory для реестра. В этих примерах субъект-служба имеет роль владельца, но при необходимости можно назначить [другие роли](../active-directory/role-based-access-control-configure.md).

### <a name="create-a-service-principal"></a>Создание субъекта-службы
С помощью следующей команды создается новый субъект-служба. Укажите надежный пароль с помощью параметра `-Password`.

```PowerShell
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName ApplicationDisplayName -Password "MyPassword"
```

### <a name="assign-a-new-or-existing-service-principal"></a>Назначение нового или существующего субъекта-службы
Реестру можно назначить новый или существующий субъект-службу. Чтобы назначить ему роль владельца для доступа к реестру, выполните команду, аналогичную следующей.

```PowerShell
New-AzureRMRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Registry.Id
```

##<a name="sign-in-to-the-registry-with-the-service-principal"></a>Вход в реестр с помощью субъекта-службы
После назначения реестру субъекта-службы можно войти в реестр с помощью следующей команды:

```PowerShell
docker login -u $ServicePrincipal.ApplicationId -p myPassword
```

## <a name="manage-admin-credentials"></a>Управление учетными данными администратора
Учетная запись администратора автоматически создается для каждого реестра контейнеров, но по умолчанию она отключена. В следующих примерах показаны команды PowerShell, с помощью которых можно управлять учетными данными администратора для реестра контейнеров.

### <a name="obtain-admin-user-credentials"></a>Получение учетных данных пользователя с правами администратора
```PowerShell
Get-AzureRMContainerRegistryCredential -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Включение пользователя с правами администратора для имеющегося реестра
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -EnableAdminUser
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Отключение пользователя с правами администратора для имеющегося реестра
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -DisableAdminUser
```

## <a name="next-steps"></a>Дальнейшие действия
* [Отправка первого образа с помощью интерфейса командной строки Docker](container-registry-get-started-docker-cli.md)

