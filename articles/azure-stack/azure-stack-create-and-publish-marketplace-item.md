---
title: Создание и публикация элемента Marketplace в Azure Stack | Документация Майкрософт
description: Создание и публикация элемента Marketplace в Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 5e0349d6bae9295e7a0ba9f366f84753ebd838c2
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
---
# <a name="create-and-publish-a-marketplace-item"></a>Создание и публикация элемента Marketplace

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

## <a name="create-a-marketplace-item"></a>Создание элемента Marketplace
1. [Скачайте](http://www.aka.ms/azurestackmarketplaceitem) средство упаковщика коллекции Azure и пример элемента Azure Stack Marketplace.
2. Откройте пример элемента Marketplace и переименуйте папку **SimpleVMTemplate**. (Новое имя должно совпадать с именем элемента Marketplace, например **Contoso.TodoList**.) Эта папка содержит следующее:
   
       /Contoso.TodoList/
       /Contoso.TodoList/Manifest.json
       /Contoso.TodoList/UIDefinition.json
       /Contoso.TodoList/Icons/
       /Contoso.TodoList/Strings/
       /Contoso.TodoList/DeploymentTemplates/
3. [Создайте шаблон Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) или выберите шаблон на портале GitHub. Элемент Marketplace использует этот шаблон для создания ресурса.
4. Проверьте, можно ли с помощью API-интерфейсов Microsoft Azure Stack развернуть ресурс из этого шаблона.
5. Если шаблон использует образ виртуальной машины, [добавьте этот образ виртуальной машины в Azure Stack](azure-stack-add-vm-image.md).
6. Сохраните шаблон Azure Resource Manager в папке **/Contoso.TodoList/DeploymentTemplates/**.
7. Выберите значки и текст для элемента Marketplace. Значки добавляются в папку **Icons**, а текст — в файл **resources**, расположенный в папке **Strings**. Для значков следуйте соглашению об именовании мелких, обычных, крупных и огромных значков. Подробное описание см. в [справочнике по пользовательскому интерфейсу элемента Marketplace](#reference-marketplace-item-ui).
   
   > [!NOTE]
   > Для правильной сборки элемента Marketplace нужны значки всех четырех размеров (малый, средний, крупный и широкий).
   > 
   > 
8. В файле **manifest.json** измените значение параметра **name**, указав имя элемента Marketplace. Кроме того, для параметра **publisher** укажите ваше имя или название компании.
9. В разделе **artefacts** укажите в параметрах **name** и **path** правильные данные для включаемого шаблона Azure Resource Manager.
   
         "artifacts": [
            {
                "name": "Type your template name",
                "type": "Template",
                "path": "DeploymentTemplates\\Type your path",
                "isDefault": true
            }
10. Замените **My Marketplace Items** списком категорий, в которых должен отображаться этот элемент Marketplace.
    
             "categories":[
                 "My Marketplace Items"
              ],
11. Остальные параметры, которые вы можете изменить в файле manifest.json для элемента Marketplace, описаны в [этом разделе](#reference-marketplace-item-manifestjson).
12. Чтобы упаковать папки в AZPKG-файл, откройте командную строку и выполните следующую команду:
    
        AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    
    > [!NOTE]
    > Следует использовать уже существующий путь к пакету выходных данных. Например, если указан выходной путь C:\MarketPlaceItem\yourpackage.azpkg, должна существовать папка C:\MarketPlaceItem.
    > 
    > 

## <a name="publish-a-marketplace-item"></a>Публикация элемента Marketplace
1. Передайте созданный элемент Marketplace (файл AZPKG) в хранилище больших двоичных объектов Azure с помощью PowerShell или обозревателя службы хранилища Azure. Вы можете использовать локальное хранилище Azure Stack или службу хранилища Azure. (Это временное расположение для файла пакета.) Убедитесь, что используемый большой двоичный объект является общедоступным.
2. На клиентской виртуальной машине в среде Microsoft Azure Stack проверьте, настроены ли учетные данные администратора службы для сеанса PowerShell. Инструкции по аутентификации PowerShell в Azure Stack можно найти в статье [Deploy templates in Azure Stack using PowerShell](user/azure-stack-deploy-template-powershell.md) (Развертывание шаблонов в Azure Stack с помощью PowerShell).
3. Используйте командлет PowerShell **Add-AzureRMGalleryItem**, чтобы опубликовать элемент Marketplace в Azure Stack. Например: 
   
       Add-AzureRMGalleryItem -GalleryItemUri `
       https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   
   | Параметр | ОПИСАНИЕ |
   | --- | --- |
   | SubscriptionID |Идентификатор подписки администратора. Его можно получить с помощью PowerShell. Если вы предпочитаете получить его на портале, найдите подписку поставщика и скопируйте идентификатор этой подписки. |
   | GalleryItemUri |URI большого двоичного объекта для пакета коллекции, который был ранее отправлен в хранилище. |
   | Apiversion |Установите значение **2015-04-01**. |
4. Вернитесь на портал. Теперь вы увидите этот элемент Marketplace на портале, войдя как оператор или пользователь.
   
   > [!NOTE]
   > Может пройти несколько минут, прежде чем пакет появится в списке.
   > 
   > 
5. Поздравляем, ваш элемент Marketplace успешно сохранен в Azure Stack Marketplace. Теперь его можно удалить из хранилища больших двоичных объектов.
6. Чтобы удалить элемент Marketplace, используйте командлет **Remove-AzureRMGalleryItem**. Пример:
   
        Remove-AzureRMGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   
   > [!NOTE]
   > После удаления элемента пользовательский интерфейс Marketplace может отобразить ошибку. Чтобы устранить эту ошибку, щелкните **Параметры** на портале. Затем щелкните **Отменить изменения** в разделе **Настройка портала**.
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>Справочные материалы: файл Manifest.json для элемента Marketplace
### <a name="identity-information"></a>Сведения об удостоверении
| ИМЯ | Обязательно | type | Ограничения | ОПИСАНИЕ |
| --- | --- | --- | --- | --- |
| ИМЯ |X |Строка |[A–Z, a–z, 0–9] + | |
| ИЗДАТЕЛЬ |X |Строка |[A–Z, a–z, 0–9] + | |
| Version (версия) |X |Строка |[SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>Метаданные
| ИМЯ | Обязательно | type | Ограничения | ОПИСАНИЕ |
| --- | --- | --- | --- | --- |
| DisplayName |X |Строка |Рекомендуется использовать 80 символов. |Портал может некорректно отображать имя элемента, если его длина превышает 80 символов. |
| PublisherDisplayName |X |Строка |Рекомендуется использовать 30 символов. |Портал может некорректно отображать имя издателя, если его длина превышает 30 символов. |
| PublisherLegalName |X |Строка |Не более 256 символов | |
| Сводка |X |Строка |60–100 знаков. | |
| LongSummary |X |Строка |140–256 знаков. |Пока не применяется в Azure Stack. |
| ОПИСАНИЕ |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |От 500 до 5000 символов. | |

### <a name="images"></a>Образы
В Marketplace используются следующие значки:

| ИМЯ | Ширина | Высота: | Заметки |
| --- | --- | --- | --- |
| Широкий |255 пикселей |115 пикселей |Обязательный |
| большой |115 пикселей |115 пикселей |Обязательный |
| Средний |90 пикселей |90 пикселей |Обязательный |
| Малый |40 пикселей |40 пикселей |Обязательный |
| Снимок экрана |533 пикселя |32 пикселя |Необязательно |

### <a name="categories"></a>Категории
Каждый элемент Marketplace должен снабжаться тегами категорий, которые определяют, где отображается этот элемент в пользовательском интерфейсе портала. Вы можете выбрать любую из существующих категорий Azure Stack (вычисления, данные и хранилище, и т. д.) или создать новую.

### <a name="links"></a>Ссылки
Каждый элемент Marketplace может содержать разные ссылки на дополнительное содержимое. Ссылки указываются в виде списка имен и универсальных кодов ресурса (URI).

| ИМЯ | Обязательно | type | Ограничения | ОПИСАНИЕ |
| --- | --- | --- | --- | --- |
| DisplayName |X |Строка |Не более 64 символов | |
| URI |X |URI | | |

### <a name="additional-properties"></a>Дополнительные свойства
Помимо приведенных выше метаданных авторы Marketplace могут предоставить пользовательские данные в формате "ключ-значение", как показано далее.

| ИМЯ | Обязательно | type | Ограничения | ОПИСАНИЕ |
| --- | --- | --- | --- | --- |
| DisplayName |X |Строка |Не более 25 символов | |
| Значение |X |Строка |Не более 30 символов | |

### <a name="html-sanitization"></a>Очистка HTML
Для всех полей, поддерживающих HTML, допускаются следующие элементы и атрибуты.

h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i

## <a name="reference-marketplace-item-ui"></a>Справочные материалы. Пользовательский интерфейс элемента Marketplace
Для элемента Marketplace на портале Azure Stack используются следующие значки и текстовые данные.

### <a name="create-blade"></a>Колонка "Создание"
![Колонка "Создание"](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Колонка информации об элементе Marketplace
![Колонка информации об элементе Marketplace](media/azure-stack-marketplace-item-ui-reference/image3.png)

