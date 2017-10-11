---
title: "Используйте набор средств Marketplace создавать и публиковать элементы marketplace | Документы Microsoft"
description: "Узнайте, как быстро создать элементы marketplace с публикацией Toolkit"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: ByronR
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/14/2017
ms.author: helaw
ms.openlocfilehash: 5b2c04d2cbc06e1572dc2e40712f6cf9d886aa1e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2017
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Добавить элементы marketplace, с помощью средства публикации
Добавление содержимого к [Azure Marketplace стека](azure-stack-marketplace.md) доступны для вас и ваших клиентов для развертывания решения.  Набор средств Marketplace создает файлы пакетов Azure Marketplace (.azpkg), исходя из расширения виртуальных Машин или шаблонов диспетчера ресурсов Azure IaaS.  Marketplace Toolkit также можно использовать для публикации .azpkg файлы, созданные с помощью программы или с помощью [вручную](azure-stack-create-and-publish-marketplace-item.md) действия.  В этом разделе поможет загрузить средство, создание marketplace элемента на основе шаблона виртуальной Машины и затем публикации этого элемента стек в Azure Marketplace.     


## <a name="prerequisites"></a>Предварительные требования
 - Необходимо выполнить набор средств на узле стек Azure или иметь [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) подключения на машине, где запускается средство.

 - Загрузить [быстрый запуск Azure стека шаблоны](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) и извлечения.

 - Загрузить [средство упаковки коллекции Azure](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - Публикации в Marketplace необходимо значков и эскизов файла.  Можно использовать собственные или сохранить [пример](azure-stack-marketplace-publisher.md#support-files) файлы локально в этом примере.

## <a name="download-the-tool"></a>Загрузите средство
Может быть Marketplace Toolkit [загружаются из репозитория средства Azure стека](azure-stack-powershell-download.md).


##  <a name="create-marketplace-items"></a>Создание элементов marketplace
В этом разделе использовать Marketplace набор средств для создания пакета элементов marketplace в формате .azpkg.  

### <a name="provide-marketplace-information-with-wizard"></a>Укажите сведения о marketplace с помощью мастера
1. Запустите Marketplace Toolkit из сеанса PowerShell:
```PowerShell
    .\MarketplaceToolkit.ps1
```

2. Нажмите кнопку **решения** вкладки.  Этот экран принимает сведения об элементе marketplace. Введите сведения об элементе, как он отображается на рынке.  Можно также указать [файл параметров](azure-stack-marketplace-publisher.md#use-a-parameters-file) нужно заполнить форму.  
    
    ![Снимок экрана: первый экран Marketplace Toolkit](./media/azure-stack-marketplace-publisher/image7.png)
3. Нажмите кнопку **Обзор** и выберите файл изображения для каждого поля, значок и экрана.  Можно использовать собственные значки или значки в образце [файлы поддержки](azure-stack-marketplace-publisher.md#support-files) раздела.
4. После заполнения всех полей, выберите «Предварительная версия решения» для предварительного просмотра этого решения в Marketplace.  Проверьте и измените текст, изображения и экрана перед нажатием кнопки **Далее**.  

### <a name="import-template-and-create-package"></a>Импорт шаблона и создания пакета
В этом разделе импортировать шаблон и работать с входными данными для вашего решения.

1.  Нажмите кнопку **Обзор** и выберите *azuredeploy.json* из папки 101-Simple-Windows-VM в загруженный шаблон.

    ![Снимок экрана второй экран Marketplace Toolkit](./media/azure-stack-marketplace-publisher/image8.png)
2.  Мастер развертывания не будет заполнен *основные* шаг и входных данных элементов для каждого параметра, указанного в шаблоне.  Можно добавить дополнительные шаги и перемещение между шагами входных данных.  В качестве примера можно привести действия «Конфигурация интерфейсный» и «Конфигурация серверной части» для вашего решения.
3.  Укажите путь к AzureGalleryPackager.exe.  
4.  Нажмите кнопку **создать** и Marketplace Toolkit упаковывает решение в файл .azpkg.  После завершения мастера отображает путь к файлу решения и дают возможность продолжить публикацию пакета стек Azure.


## <a name="publish-marketplace-items"></a>Публикация элементов marketplace
В этом разделе вы опубликовать элемент marketplace стека в Azure Marketplace.

![Снимок экрана: первый экран Marketplace Toolkit](./media/azure-stack-marketplace-publisher/image9.png)

1.  Мастеру необходимы сведения для публикации решения:
    
    |Поле|Описание|
    |-----|-----|
    | Имя администратора службы | Учетная запись администратора службы.  Пример:ServiceAdmin@mydomain.onmicrosoft.com |
    | Пароль | Пароль для учетной записи администратора службы. |
    | Конечная точка API | Конечная точка Azure стека диспетчера ресурсов Azure.  Пример: management.local.azurestack.external |
2.  Нажмите кнопку **публикации** и отображается журнал публикации.
3.  Теперь имеется возможность развернуть опубликованный элемент через портал Azure стека.


## <a name="use-a-parameters-file"></a>Использовать файл параметров
Также можно использовать файл параметров для выполнения сведения об элементе marketplace.  

Включает набор средств Marketplace *solution.parameters.ps1* можно использовать для создания файлов параметров.


## <a name="support-files"></a>Файлы поддержки
| Описание | Образец |
| ----- | ----- |
| значок .png 40 x 40 | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| значок .png 90 x 90 | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| значок .png 115 x 115 | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| значок .png 255 x 115 | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| эскиз .png 533 х 324 | ![](./media/azure-stack-marketplace-publisher/image5.png) |


