---
title: Привязка учетной записи Azure к идентификатору партнера | Документация Майкрософт
description: Отслеживайте взаимодействия с клиентами Azure, привязав идентификатор партнера к учетной записи пользователя, используемой для управления ресурсами клиента.
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a5b6bb683538bd5359ebcbbe2640216e574b3e00
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164624"
---
# <a name="link-partner-id-to-your-azure-accounts"></a>Привязка идентификатора партнера к учетной записи Azure

Как партнер вы можете отслеживать свое влияние на привлечение клиентов, привязав идентификатор партнера к учетным записям, используемым для управления ресурсами клиента.

Эта функция предоставляется в общедоступной предварительной версии.

## <a name="get-access-from-your-customer"></a>Получение доступа от клиента

Перед привязкой идентификатора партнера клиент должен предоставить вам доступ к своим ресурсам Azure. Это можно сделать с помощью одного из следующих вариантов.

- **Гостевой пользователь.** Клиент может добавить вас в качестве гостевого пользователя и назначить роли RBAC. Дополнительные сведения см. в статье [Что такое служба совместной работы Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Учетная запись каталога.** Клиент может создать пользователя из вашей организации в каталоге и назначить ему любую роль RBAC.

- **Субъект-служба.** Клиент может добавить в каталог приложение или сценарий из вашей организации и назначить любую роль RBAC. Идентификатор приложения или сценария называют субъектом-службой.

## <a name="link-partner-id"></a>Привязка идентификатора партнера

Получив доступ к ресурсам клиента, привяжите идентификатор Microsoft Partner Network (MPN ID) к идентификатору пользователя или субъекта-службы с помощью портала Azure, PowerShell или интерфейса командной строки (CLI). Необходимо привязать идентификатор партнера в каждом клиенте.

### <a name="use-azure-portal-to-link-new-partner-id"></a>Использование портала Azure для связи нового идентификатора партнера

1. Перейдите на страницу [Link to a partner ID](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) (Ссылка на идентификатор партнера) на портале Azure.

2. Войдите на портал Azure.

3. Введите идентификатор партнера Майкрософт. Это идентификатор [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) вашей организации.

  ![Снимок экрана, показывающий ссылку на идентификатор партнера](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Чтобы переслать идентификатор партнера другому клиенту, используйте переключатель каталога. В разделе "Переключение каталога" выберите свой каталог.

  ![Снимок экрана, показывающий ссылку на идентификатор партнера](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-new-partner-id"></a>Привязка нового идентификатора партнера с помощью PowerShell

1. Установите модуль PowerShell [AzurePartnerRP](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner/0.1.0-preview).

2. Войдите в клиент партнера с помощью учетной записи пользователя или субъекта-службы. Дополнительные сведения см. в статье [Вход с помощью Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Привяжите новый идентификатор партнера. Идентификатор партнера — это идентификатор [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) вашей организации.

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>Получение связанного идентификатора партнера
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>Обновление связанного идентификатора партнера
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>Удаление связанного идентификатора партнера
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>Привязка нового идентификатора партнера с помощью CLI
1.  Установите расширение CLI.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ``` 

2.  Войдите в клиент пользователя с помощью учетной записи пользователя или субъекта-службы. Дополнительные сведения см. в статье [Вход с помощью Azure CLI 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ``` 

3.  Привяжите новый идентификатор партнера. Идентификатор партнера — это идентификатор [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) вашей организации.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Получение связанного идентификатора партнера
```azurecli-interactive
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>Обновление связанного идентификатора партнера
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>Удаление связанного идентификатора партнера
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
``` 

## <a name="next-steps"></a>Дополнительная информация

Присоединитесь к обсуждениям [сообщества партнеров Майкрософт](https://aka.ms/PALdiscussion), чтобы получать обновления или отправлять отзывы.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

**Кто может привязать идентификатор партнера?**

Любой пользователь из партнерской организации, который управляет ресурсами клиента, может привязать идентификатор партнера к учетной записи.

**Можно ли изменить связанный идентификатор партнера?**

Да, связанный идентификатор партнера можно изменить, добавить или удалить.

**Что делать, если у пользователя есть учетная запись в нескольких клиентах пользователя?**

Привязка идентификатора партнера к учетной записи выполняется для каждого клиента.  Необходимо привязать идентификатор партнера в каждом клиенте.

**Может ли другой партнер или клиент изменить или удалить привязку к идентификатору партнера?**

Привязка создана на уровне учетной записи. Только вы можете изменить или удалить привязку к идентификатору партнера. Клиенты и другие партнеры не могут изменять привязку к идентификатору партнера. 
