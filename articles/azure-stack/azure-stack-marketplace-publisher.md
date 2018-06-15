---
title: Использование набора средств Marketplace для создания и публикации элементов Marketplace | Документация Майкрософт
description: Узнайте, как быстро создать элементы Marketplace с помощью набора средств для публикации
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/14/2017
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 61ee3296429f9641643f1c9268ae89e3691fcfa1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29386883"
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Добавление элементов Marketplace с помощью средства публикации
Добавляя содержимое в [Azure Stack Marketplace](azure-stack-marketplace.md), вы предоставляете себе и своим клиентам возможность развертывать ваши решения.  Набор средств Marketplace создает файлы пакетов Azure Marketplace (AZPKG), основанные на шаблонах IaaS Azure Resource Manager или расширениях виртуальных машин.  Также набор средств Marketplace позволяет публиковать AZPKG-файлы, созданные с помощью этого средства или [вручную](azure-stack-create-and-publish-marketplace-item.md).  Из этой статьи вы узнаете, как скачать это средство, как создать элемент Marketplace на основе шаблона виртуальной машины и как опубликовать этот элемент в Azure Stack Marketplace.     


## <a name="prerequisites"></a>предварительным требованиям
 - Для запуска набора средств следует использовать узел Azure Stack или создать [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)-подключение к нему с того компьютера, где будет выполняться набор средств.

 - Скачайте [шаблоны быстрого запуска Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) и извлеките их содержимое.

 - Скачайте [средство упаковки для коллекции Azure](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - Для публикации в Marketplace вам потребуется файл значков и эскизов.  Вы можете создать свой файл или использовать [файл из примера](azure-stack-marketplace-publisher.md#support-files).

## <a name="download-the-tool"></a>Скачивание средства
Набор средств Marketplace можно скачать [из репозитория средств Azure Stack](azure-stack-powershell-download.md).


##  <a name="create-marketplace-items"></a>Создание элементов Marketplace
В этом разделе вы создадите пакет элементов Marketplace в формате AZPKG с помощью набора средств Marketplace.  

### <a name="provide-marketplace-information-with-wizard"></a>Предоставление сведений о Marketplace с помощью мастера
1. Запустите набор средств Marketplace в сеансе PowerShell:
```PowerShell
    .\MarketplaceToolkit.ps1
```

2. Щелкните вкладку **Решения**.  Этот экран предлагает ввести сведения об элементе Marketplace. Введите данные в том виде, в котором вы хотите опубликовать их в Marketplace.  Вы можете использовать [файл параметров](azure-stack-marketplace-publisher.md#use-a-parameters-file) для предварительного заполнения форм.  
    
    ![Снимок экрана: первый экран набора средств Marketplace](./media/azure-stack-marketplace-publisher/image7.png)
3. Нажмите кнопку **Обзор** и выберите файл изображения для каждого поля со значками и снимками экрана.  Вы можете использовать собственные значки или образцы, предложенные в разделе [файлов поддержки](azure-stack-marketplace-publisher.md#support-files).
4. Когда вы заполните все поля, выберите действие "Предварительный просмотр решения", чтобы просмотреть страницу решения в Marketplace.  Здесь вы можете проверить и (или) изменить тексты, изображения и снимки экранов. Когда все будет готово, щелкните **Далее**.  

### <a name="import-template-and-create-package"></a>Импорт шаблона и создание пакета
В этом разделе вы импортируете шаблон и опишете входные данные для вашего решения.

1.  Щелкните **Обзор** и выберите файл *azuredeploy.json* из папки 101-Simple-Windows-VM в скачанном ранее шаблоне.

    ![Снимок экрана: второй экран набора средств Marketplace](./media/azure-stack-marketplace-publisher/image8.png)
2.  В мастере развертывания указываются *основные данные* (шаги) и входные данные для каждого параметра, указанного в шаблоне.  Вы можете добавлять дополнительные шаги и перемещать входные данные между ними.  Например, можно создать для решения действия "Настройка интерфейсной части" и "Настройка серверной части".
3.  Укажите путь к файлу AzureGalleryPackager.exe.  
4.  Щелкните **Создать**. Набор средств Marketplace упакует предложенное решение в AZPKG-файл.  Когда мастер завершит работу, он отобразит путь к созданному файлу решения и предложит опубликовать этот пакет в Azure Stack.


## <a name="publish-marketplace-items"></a>Публикация элементов Marketplace
В этом разделе вы опубликуете элемент Marketplace в Azure Stack Marketplace.

![Снимок экрана: первый экран набора средств Marketplace](./media/azure-stack-marketplace-publisher/image9.png)

1.  Мастеру нужны следующие сведения для публикации решения:
    
    |Поле|ОПИСАНИЕ|
    |-----|-----|
    | Имя администратора службы | Учетная запись администратора службы.  Пример: ServiceAdmin@mydomain.onmicrosoft.com |
    | Пароль | Пароль для учетной записи администратора службы. |
    | Конечная точка API | Конечная тока Azure Resource Manager для Azure Stack.  Пример: management.local.azurestack.external |
2.  Нажмите кнопку **Опубликовать**. Отобразится журнал публикации.
3.  Теперь вы можете развертывать опубликованный элемент с помощью портала Azure Stack.


## <a name="use-a-parameters-file"></a>Использование файла параметров
Вы можете использовать файл параметров для заполнения сведений об элементе Marketplace.  

Набор средств Marketplace содержит файл *solution.parameters.ps1*, с помощью которого вы можете создать собственный файл параметров.


## <a name="support-files"></a>Файлы поддержки
| ОПИСАНИЕ | Образец |
| ----- | ----- |
| значок 40×40 в формате PNG | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| значок 90×90 в формате PNG | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| значок 115×115 в формате PNG | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| значок 255×115 в формате PNG | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| эскиз 533×324 в формате PNG | ![](./media/azure-stack-marketplace-publisher/image5.png) |


