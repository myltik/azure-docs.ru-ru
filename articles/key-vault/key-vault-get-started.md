---
title: Приступая к работе с хранилищем ключей Azure | Документация Майкрософт
description: Этот учебник поможет вам начать работу с хранилищем ключей Azure. В нем содержится информация о создании зафиксированного контейнера (хранилища), хранении криптографических ключей и секретов, а также об управлении ими в Azure.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 36721e1d-38b8-4a15-ba6f-14ed5be4de79
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2018
ms.author: barclayn
ms.openlocfilehash: 58a283f1ce5bd2fd78c4fa2038c3998aea1598c9
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34070357"
---
# <a name="get-started-with-azure-key-vault"></a>Приступая к работе с хранилищем ключей Azure
Это руководство поможет вам приступить к работе с Azure Key Vault с использованием PowerShell. Здесь также объясняется, как выполнить следующие задачи:
- как создать контейнер (хранилище) с усиленной защитой в Azure;
- как использовать Key Vault для хранения криптографических ключей и секретов, а также управления ими в Azure;
- как приложение может использовать этот ключ или пароль.

Хранилище ключей Azure доступно в большинстве регионов. Дополнительные сведения см. на странице [цен на хранилище ключей](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> В этой статье не приводятся инструкции по созданию приложения Azure. Для выполнения шагов из этой статьи вы можете воспользоваться [примером приложения Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343).

Инструкции по кроссплатформенному интерфейсу командной строки см. в [этом руководстве](key-vault-manage-with-cli2.md).

## <a name="requirements"></a>Требования
Прежде чем перейти к следующим разделам статьи, убедитесь в наличии следующих компонентов:

- **Подписка Azure**. Если у вас нет подписки, вы можете зарегистрироваться для использования [бесплатной учетной записи](https://azure.microsoft.com/pricing/free-trial/).
- **Azure PowerShell** **начиная с версии 1.1.0**. Чтобы установить решение Azure PowerShell и связать его с подпиской Azure, см. статью [Установка и настройка Azure PowerShell](/powershell/azure/overview). Если средство Azure PowerShell у вас установлено, но вы не знаете его версию, в консоли Azure PowerShell введите `(Get-Module azure -ListAvailable).Version`. Если у вас установлено средство Azure PowerShell версий 0.9.1–0.9.8, вы можете использовать это руководство с некоторыми незначительными поправками. Например, вам нужно использовать команду `Switch-AzureMode AzureResourceManager`; также отличаются некоторые команды хранилища ключей Azure. Полный список командлетов хранилища ключей для Azure PowerShell версий 0.9.1–0.9.8 см. в [этой статье](/powershell/module/azurerm.keyvault/#key_vault).
- **Приложение, которое можно настроить для использования Key Vault**. Пример приложения доступен в [Центре загрузки Майкрософт](http://www.microsoft.com/download/details.aspx?id=45343). Инструкции см. в сопутствующем файле **README**.

>[!NOTE]
Для работы с этой статьей необходимо знание основных понятий PowerShell и Azure. Дополнительные сведения о PowerShell см. в статье [Начало работы с Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Чтобы получить подробную справку для любого командлета, встречающегося в этом учебнике, используйте командлет **Get-Help** .

```powershell-interactive
Get-Help <cmdlet-name> -Detailed
```
    
Например, чтобы получить справку по командлету **Connect-AzureRmAccount**, введите следующее:

```PowerShell
Get-Help Connect-AzureRmAccount -Detailed
```

Чтобы ознакомиться с моделью развертывания с помощью Azure Resource Manager в Azure PowerShell, можно также прочитать следующие статьи:

* [Как установить и настроить Azure PowerShell](/powershell/azure/overview)
* [Использование Azure PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md)

## <a id="connect"></a>Подключение к подпискам
Запустите сеанс Azure PowerShell и войдите в учетную запись Azure, используя следующую команду:  

```PowerShell
Connect-AzureRmAccount
```

>[!NOTE]
 При использовании определенного экземпляра Azure примените параметр -Environment. Например:  
 ```powershell
 Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
 ```

Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. Azure PowerShell получает все подписки, связанные с этой учетной записью, и по умолчанию использует первую из них.

Если у вас есть несколько подписок и нужно указать лишь одну из них, которая будет использоваться для хранилища ключей Azure, введите следующую команду, чтобы увидеть подписки своей учетной записи:

```powershell
Get-AzureRmSubscription
```

Далее, чтобы указать подписку, которую нужно использовать, введите:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

Дополнительные сведения о настройке Azure PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

## <a id="resource"></a>Создание новой группы ресурсов
При использовании диспетчера ресурсов Azure все связанные ресурсы создаются внутри группы ресурсов. Для примера мы создадим новую группу ресурсов с именем **ContosoResourceGroup** :

```powershell
New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East US'
```

## <a id="vault"></a>Создание хранилища ключей
Чтобы создать хранилище ключей, используйте командлет [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Этот командлет предусматривает три обязательных параметра: **имя группы ресурсов**, **имя хранилища ключей** и **географическое расположение**.

Допустим, вы используете следующие значения:
- имя хранилища — **ContosoKeyVault**;
- имя группы ресурсов — **ContosoResourceGroup**;
- расположение — **East US** (восточная часть США).

Тогда вам нужно ввести такой код:

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```
![Выходные данные после выполнения команды, создающей хранилище ключей](./media/key-vault-get-started/output-after-creating-keyvault.png)

В выходных данных командлета будут показаны свойства созданного хранилища ключей. Среди всех свойств есть два самых важных:

* **Имя хранилища** — в нашем примере это **ContosoKeyVault**. Вы будете использовать это имя для выполнения других командлетов хранилища ключей;
* **Код URI хранилища**. В данном примере это https://contosokeyvault.vault.azure.net/. Необходимо, чтобы приложения, использующие ваше хранилище через REST API, использовали этот URI.

Теперь ваша учетная запись Azure авторизована, и вы можете выполнять любые операции в этом хранилище ключей. Пока что это недоступно для других учетных записей.

> [!NOTE]
> При попытке создать хранилище ключей может возникнуть ошибка **Подписка не зарегистрирована для использования пространства имен "Microsoft.KeyVault"**. Если отобразится такое сообщение, выполните команду `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"`. После успешного завершения регистрации можно повторно выполнить команду New-AzureRmKeyVault. Дополнительные сведения см. в статье [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider).
>
>

## <a id="add"></a>Добавление ключа или секрета в хранилище ключей
Существует несколько способов, которыми можно воспользоваться для взаимодействия с хранилищем ключей, ключами и секретами.

### <a name="azure-key-vault-generates-a-software-protected-key"></a>Azure Key Vault создает ключ с программной защитой

Чтобы создать ключ с программной защитой для собственного использования с помощью Azure Key Vault, используйте командлет [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) и введите следующее:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'
```
Чтобы просмотреть URI для этого ключа, введите следующее:
```powershell
$key.id
```

Для доступа к ключу, созданному или отправленному в Azure Key Vault, вы можете использовать его URI. Можно использовать **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** для получения текущей версии или **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** для получения этой конкретной версии.  

### <a name="importing-an-existing-pfx-file-into-azure-key-vault"></a>Импорт существующего PFX-файла в Azure Key Vault

Если существующие ключи хранятся в PFX-файле, который требуется отправить в Azure Key Vault, шаги будут отличаться. Например: 
- если у вас есть ключ с программной защитой в PFX-файле;
- PFX-файлу присвоено имя softkey.pfx; 
- файл хранится на диске C.

Введите следующий код:

```powershell
$securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force  // This stores the password 123 in the variable $securepfxpwd
```

Затем введите следующую команду для импорта ключа из PFX-файла, который защищает его с помощью программного обеспечения в службе хранилища ключей:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoImportedPFX' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd
```

Чтобы отобразить URI для этого ключа, введите:

```powershell
$Key.id
```
Чтобы просмотреть свой ключ, введите: 

```powershell
Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'
```
Если вы хотите просмотреть свойства PFX-файла на портале, вы увидите экран, как на изображении ниже:

![Вид сертификата на портале](./media/key-vault-get-started/imported-pfx.png)
### <a name="to-add-a-secret-to-azure-key-vault"></a>Добавление секрета в Azure Key Vault

Чтобы добавить в Azure Key Vault секрет, который представляет собой пароль с именем SQLPassword и значением Pa$$w0rd, сначала преобразуйте значение Pa$$w0rd в защищенную строку. Для этого введите следующее:

```powershell    
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

Затем введите следующее:

```powershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue
```


Теперь пароль, добавленный в хранилище ключей Azure, можно вызвать, используя его URI. Используйте **https://ContosoVault.vault.azure.net/secrets/SQLPassword**, чтобы всегда получать актуальную версию, или **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**, чтобы получить эту конкретную версию.

Чтобы отобразить URI для этого секрета, введите:

```powershell
$secret.Id
```
Чтобы просмотреть свой секрет, введите: `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`. Также вы можете просмотреть секрет на портале.

![secret](./media/key-vault-get-started/secret-value.png)

Чтобы просмотреть содержащееся в секрете значение в виде обычного текста, введите следующее:
```powershell
(get-azurekeyvaultsecret -vaultName "Contosokeyvault" -name "SQLPassword").SecretValueText
```
Теперь хранилище ключей и ключ или секрет готовы для использования в приложениях. Необходимо авторизовать приложения для их использования.  

## <a id="register"></a>Регистрация приложения в Azure Active Directory
Обычно этот шаг выполняет разработчик на отдельном компьютере. Он не относится исключительно к Azure Key Vault. Пошаговые инструкции по регистрации приложения с помощью Azure Active Directory см. в статье [Интеграция приложений с Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) или [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../azure-resource-manager/resource-group-create-service-principal-portal.md)

> [!IMPORTANT]
> Чтобы завершить прохождение этого учебника, учетная запись, хранилище и приложение, которое вы будете регистрировать на этом шаге, должны находиться в одном каталоге Azure.


Приложения, которые используют хранилища ключей, должны пройти аутентификацию с использованием токена Azure Active Directory. Для этого владельцу приложения необходимо сначала зарегистрировать приложение в Azure Active Directory. В конце регистрации владелец приложения получает следующие значения.

- **Идентификатор приложения**. 
- **Ключ проверки подлинности** (также известный как общий секрет). 

Для получения маркера приложение должно предоставить Azure Active Directory оба этих значения. Настроенный способ предоставления зависит от приложения. В [примере приложения, использующего Key Vault](https://www.microsoft.com/download/details.aspx?id=45343), владелец приложения задает эти значения в файле app.config.


Чтобы зарегистрировать приложение в Azure Active Directory:

1. Войдите на [портале Azure](https://portal.azure.com).
2. Слева щелкните **Регистрация приложений**. Если зарегистрированные приложения не отображаются, щелкните **Больше служб** и найдите их там.  
>[!NOTE]
Вам необходимо выбрать каталог, содержащий подписку Azure, которую вы использовали для создания хранилища ключей. 
3. Щелкните **Регистрация нового приложения**.
4. В колонке **Создание** укажите имя своего приложения, а затем выберите **Веб-приложение и/или веб-API** (по умолчанию) и укажите **URL-адрес для входа** для веб-приложения. Если у вас пока нет этих сведений, вы можете указать фиктивное значение для использования на этом этапе (например, можно указать http://test1.contoso.com). Неважно, существуют ли эти сайты. 

    ![Регистрация нового приложения](./media/key-vault-get-started/new-application-registration.png)
    >[!WARNING]
    Убедитесь, что выбран тип **Веб-приложение и/или веб-API**. В противном случае параметр **Ключи** не отобразится в списке параметров.

5. Нажмите кнопку **Создать** .
6. Завершив регистрацию приложения, вы увидите список зарегистрированных приложений. Найдите только что зарегистрированное приложение и щелкните его имя.
7. Щелкните колонку **Зарегистрированное приложение** и скопируйте **идентификатор приложения**.
8. Выберите **Все параметры**.
9. В колонке **Параметры** щелкните **Ключи**.
9. Введите описание в поле **Описание ключа**, задайте срок действия и нажмите кнопку **Сохранить**. Страница обновится и отобразится значение ключа. 
10. **Идентификатор приложения** и сведения о **ключе** вам понадобятся на следующем шаге, чтобы задать разрешения в своем хранилище.

## <a id="authorize"></a>Авторизация приложения для использования ключа или секрета
Авторизовать приложение для получения доступа к ключу или секрету в хранилище можно двумя способами.

### <a name="using-powershell"></a>с использованием PowerShell.
Для работы с PowerShell используйте командлет [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy).

Например, если имя хранилища — **ContosoKeyVault** , идентификатор клиента приложения — 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed и вам нужно авторизовать это приложение для расшифровки и подписи с использованием ключей в вашем хранилище, выполните следующую команду:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Если этому же приложению необходимо разрешить читать секретные данные в хранилище, выполните следующую команду:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get
```
### <a name="using-the-azure-portal"></a>Использование портала Azure
Вы можете изменить параметры авторизации приложения, чтобы использовать ключи или секреты:
1. Выберите **Политики доступа** в колонке ресурсов Key Vault.
2. Нажмите кнопку [+Добавить] в верхней части колонки.
3. Щелкните **Выбор субъекта**, чтобы выбрать приложение, созданное ранее.
4. В раскрывающемся списке **Разрешения ключей** выберите "Расшифровать" и "Подписать", чтобы разрешить приложению выполнять расшифровку и подписывание с помощью ключей в хранилище.
5. В раскрывающемся списке **Разрешения секретов** выберите "Получить", чтобы разрешить приложению считывать секреты в хранилище.

## <a id="HSM"></a>Работа с аппаратным модулем безопасности (HSM)
Чтобы обеспечить более высокий уровень защиты, можно импортировать ключи или создать их в аппаратных модулях безопасности (ключи никогда не покидают их пределов). В качестве аппаратных модулей безопасности используются модули FIPS 140-2 с проверкой уровня 2. Если это требование вас не касается, пропустите этот подраздел и перейдите к подразделу [Удаление хранилища ключей, а также связанных ключей и секретов](#delete).

Чтобы создать ключи, защищенные аппаратным модулем, используйте [хранилище ключей Azure уровня "Премиум", которое поддерживает ключи, защищенные аппаратным модулем](https://azure.microsoft.com/pricing/free-trial/). Обратите внимание, эта функция недоступна для Китая.

При создании хранилища ключей добавьте параметр **-SKU**:

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US' -SKU 'Premium'
```


В это хранилище ключей можно добавлять ключи с программной защитой (как показано выше) и ключи, защищенные аппаратным модулем безопасности. Чтобы создать ключ, защищенный аппаратным модулем безопасности, задайте значение «HSM» для параметра **-Destination** :

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'
```

Вы можете использовать следующую команду для импорта ключа из PFX-файла на своем компьютере. Эта команда импортирует ключ в аппаратные модули безопасности в службе хранилища ключей:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'
```

Следующая команда импортирует пакет собственного ключа клиента (BYOK). Этот сценарий дает возможность создать ключ в локальном аппаратном модуле безопасности и передать его в аппаратные модули безопасности в службе хранилища ключей так, чтобы ключ не покидал их пределы:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'
```

Более подробные инструкции по созданию пакета BYOK см. в статье [Создание ключей, защищенных аппаратным модулем безопасности, и их передача в хранилище ключей Azure](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Удаление хранилища ключей, а также связанных ключей и секретов
Если хранилище ключей (а также содержащийся в нем ключ или секрет) больше не нужно, его можно удалить с помощью командлета [Remove-AzureRmKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault).

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'
```

Как вариант, можно удалить всю группу ресурсов Azure, которая включает в себя хранилище ключей и другие ресурсы, которые вы добавили в эту группу:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'
```

## <a id="other"></a>Другие командлеты Azure PowerShell
Другие команды, которые могут быть полезны при управлении хранилищем ключей Azure.

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'` — эта команда отображает все ключи и выбранные свойства в виде таблицы;
- `$Keys[0]` — эта команда отображает полный список свойств для указанного ключа;
- `Get-AzureKeyVaultSecret` — эта команда перечисляет все имена секретов и выбранные свойства в виде таблицы;
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'` — пример того, как удалить конкретный ключ;
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'` — пример того, как удалить конкретный секрет.

## <a name="next-steps"></a>Дополнительная информация

- Общие сведения о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](key-vault-whatis.md)
- Сведения об использовании хранилища ключей см. в статье [Ведение журнала хранилища ключей Azure](key-vault-logging.md).
- Подробное руководство по использованию хранилища ключей Azure в веб-приложении см. в [этой статье](key-vault-use-from-web-application.md).
- Справочные материалы по программированию см. в статье [Руководство разработчика хранилища ключей Azure](key-vault-developers-guide.md).
- Полный список актуальных командлетов Azure PowerShell для хранилища ключей Azure см. в [этой статье](/powershell/module/azurerm.keyvault/#key_vault).
