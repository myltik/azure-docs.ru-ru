---
title: Доступ к Azure Resource Manager с помощью пользовательского удостоверения MSI виртуальной машины Windows
description: Из этого руководства вы узнаете, как получить доступ к Azure Resource Manager с помощью пользовательского управляемого удостоверения службы (MSI) на виртуальной машине Windows.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: a2225409e4cb50d91c09207ee70b76df12925192
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301217"
---
# <a name="tutorial-use-a-user-assigned-managed-service-identity-msi-on-a-windows-vm-to-access-azure-resource-manager"></a>Руководство по получению доступа к Azure Resource Manager с помощью пользовательского управляемого удостоверения службы (MSI) на виртуальной машине Windows

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

В этом руководстве объясняется, как создать пользовательское удостоверение, назначить его виртуальной машине Windows и затем с помощью этого удостоверения получить доступ к API Azure Resource Manager. Управляемые удостоверения службы автоматически управляются платформой Azure. Они обеспечивают проверку подлинности для служб, поддерживающих проверку подлинности Azure AD, без необходимости внедрения учетных данных в код. 

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины Windows 
> * Создание пользовательского удостоверения
> * Назначение пользовательского удостоверения виртуальной машине Windows.
> * Предоставление пользовательскому удостоверению доступа к группе ресурсов в Azure Resource Manager. 
> * Получение маркера доступа с помощью пользовательского удостоверения и вызов Azure Resource Manager с его помощью. 
> * Чтение свойств группы ресурсов.

## <a name="prerequisites"></a>предварительным требованиям

- Если вы не работали с управляемым удостоверением службы, изучите [общие сведения](overview.md). **Обратите внимание на [различия между системным и пользовательским удостоверениями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения требуемых операций по созданию ресурсов и управлению ролями учетной записи в этом руководстве нужно предоставить учетной записи разрешения "Владелец" в соответствующей области (подписка или группа ресурсов). Прочитайте раздел [Использование управления доступом на основе ролей для контроля доступа к ресурсам в подписке Azure](/azure/role-based-access-control/role-assignments-portal), если нуждаетесь в помощи с назначением ролей.

Чтобы установить и использовать PowerShell локально для работы с этим руководством, вам понадобится модуль Azure PowerShell 5.7 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="create-resource-group"></a>Создать группу ресурсов

В следующем примере создается группа ресурсов *myResourceGroupVM* в регионе *EastUS*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

## <a name="create-virtual-machine"></a>Создание виртуальной машины

После создания группы ресурсов создайте виртуальную машину Windows.

Настройте на виртуальной машине имя пользователя и пароль для учетной записи администратора с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```
Создайте виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="create-a-user-assigned-identity"></a>Создание пользовательского удостоверения

Пользовательское удостоверение создается как изолированный ресурс Azure. С помощью команды [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) Azure создает в клиенте Azure AD удостоверение, которое можно назначить одному или нескольким экземплярам службы Azure.

> [!IMPORTANT]
> В пользовательских удостоверениях можно использовать только буквы, цифры и символ дефиса (0–9, a–z, A–Z и -). Кроме того, чтобы назначение виртуальной машине или VMSS производилось правильно, длина имени не должна превышать 24 символа. Загляните сюда позже, чтобы проверить наличие новой информации. Дополнительные сведения см. в разделе [Часто задаваемые вопросы и известные проблемы](known-issues.md).

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

Ответ содержит подробные сведения о созданном пользовательском удостоверении, приведенном в следующем примере. Запишите значение `Id` пользовательского удостоверения, так как оно будет использоваться на следующем шаге.

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Назначение пользовательского удостоверения виртуальной машине Windows

Клиенты могут использовать пользовательское удостоверение для целого ряда ресурсов Azure. Чтобы назначить пользовательское удостоверение отдельной виртуальной машине, используйте следующие команды. Используйте свойство `Id`, возвращенное на предыдущем шаге, для параметра `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Предоставление пользовательскому удостоверению MSI доступа к группе ресурсов в Azure Resource Manager 

Компонент "Управляемое удостоверение службы" (MSI) предоставляет удостоверения, используемые кодом для запроса маркеров доступа в целях аутентификации в интерфейсах API ресурсов, которые поддерживают аутентификацию Azure AD. В этом руководстве код получит доступ к API Azure Resource Manager. 

Чтобы ваш код мог получить доступ к API, необходимо сначала предоставить удостоверению доступ к ресурсу в Azure Resource Manager. В этом случае — к группе ресурсов, в которой содержится виртуальная машина. Измените значение `<SUBSCRIPTION ID>` в соответствии с вашей средой.

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

Ответ содержит подробные сведения о созданном назначении роли, подобные следующему примеру.

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и вызов Resource Manager с его помощью 

Далее в этом руководстве мы будем работать с виртуальной машиной, которую создали ранее.

1. Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

2. На портале перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Windows и в разделе **Обзор** щелкните **Подключить**.

3. Введите **имя пользователя** и **пароль**, указанные при создании виртуальной машины Windows.

4. Теперь, когда создано **подключение к удаленному рабочему столу** с виртуальной машиной, откройте **PowerShell** в удаленном сеансе.

5. С помощью команды PowerShell `Invoke-WebRequest` выполните запрос к локальной конечной точке MSI, чтобы получить маркер доступа к Azure Resource Manager.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=73444643-8088-4d70-9532-c3a0fdc190fz&resource=https://management.azure.com' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Чтение свойств группы ресурсов.

Используйте маркер, полученный на предыдущем шаге, для доступа к Azure Resource Manager и считайте свойства группы ресурсов, доступ к которой вы предоставили пользовательскому удостоверению. Замените <SUBSCRIPTION ID> идентификатором подписки своей среды.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
Ответ содержит сведения об определенной группе ресурсов, подобные следующему примеру:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/TestRG","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Дополнительная информация

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](overview.md).
