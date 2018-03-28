---
title: Добавление элемента Azure Stack Marketplace из Azure | Документация Майкрософт
description: В этой статье объясняется, как добавить образ виртуальной машины Azure под управлением Windows Server в Azure Stack Marketplace.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 25f179f825526e20377c0e94ccb38a788cadd898
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-azure"></a>Руководство. Добавление элемента Azure Stack Marketplace из Azure

Чтобы пользователи могли развернуть виртуальную машину, вам как оператору Azure Stack нужно добавить образ виртуальной машины в Azure Stack Marketplace. По умолчанию в Azure Stack Marketplace ничего не публикуется, но вы можете загрузить элементы из [проверенного списка элементов Azure Marketplace](.\.\azure-stack-marketplace-azure-items.md), которые предварительно протестированы для запуска в Azure Stack. Используйте этот вариант, если вы работаете в сценарии с подключением и экземпляр Azure Stack зарегистрирован в Azure.

Следуя инструкциям этого руководства, вы добавите образ виртуальной машины Windows Server 2016 из Azure Marketplace в Azure Stack Marketplace.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * добавление элемента Azure Stack Marketplace "Виртуальная машина Windows Server";
> * проверка доступности образа виртуальной машины; 
> * тестирование образа виртуальной машины.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством:

- Установите [совместимые с Azure Stack модули Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell).
- Скачайте [средства Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools).
- [Зарегистрируйте ASDK](asdk-register.md) в подписке Azure.

## <a name="add-a-windows-server-vm-image"></a>Добавление образа виртуальной машины Windows Server
Чтобы добавить образ Windows Server 2016 в Azure Stack Marketplace, загрузите его из Azure Marketplace. Используйте этот вариант, если вы работаете режиме подключения и [экземпляр Azure Stack зарегистрирован](asdk-register.md) в Azure.

1. Войдите на [портал администратора ASDK](https://adminportal.local.azurestack.external).

2. Щелкните **Больше служб** > **Marketplace Management** (Управление Marketplace) > **Add from Azure** (Добавить из Azure). 

   ![Добавление из Azure](media/asdk-marketplace-item/azs-marketplace.png)

3. Найдите образ **Windows Server 2016 Datacenter**.

4. Выберите образ **Windows Server 2016 Datacenter** и выберите **Загрузить**.

   ![Скачивание образа из Azure](media/asdk-marketplace-item/azure-marketplace-ws2016.png)


> [!NOTE]
> Скачивание образа виртуальной машины и его публикация в Azure Stack Marketplace займет около часа. 

По завершении загрузки этот образ будет опубликован и станет доступен в разделе **Marketplace Management** (Управление Marketplace). Кроме того, этот образ доступен в разделе **Вычисления** для создания виртуальных машин.

## <a name="verify-the-marketplace-item-is-available"></a>Проверка доступности элемента Marketplace
Чтобы проверить доступность нового образа виртуальной машины в Azure Stack Marketplace, выполните указанные ниже действия.

1. Войдите на [портал администратора ASDK](https://adminportal.local.azurestack.external).

2. Щелкните **Больше служб** > **Marketplace Management (Управление Marketplace)**. 

3. Убедитесь, что образ виртуальной машины Windows Server 2016 Datacenter успешно добавлен.

   ![Скачанный из Azure образ](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>Тестирование образа виртуальной машины
Как оператор Azure Stack, вы можете создать тестовую виртуальную машину на [портале администратора](https://adminportal.local.azurestack.external), чтобы убедиться, что образ стал доступен. 

1. Войдите на [портал администратора ASDK](https://adminportal.local.azurestack.external).

2. Последовательно выберите **Создать** > **Вычисления** > **Windows Server 2016 Datacenter** > **Создать**.  
 ![Создание виртуальной машины из образа Marketplace](media/asdk-marketplace-item/new-compute.png)

3. В колонке **Основные сведения** задайте следующие параметры и нажмите кнопку **OK**:
  - **Имя** — test-vm-1;
  - **Имя пользователя** — AdminTestUser;
  - **Пароль** — AzS-TestVM01;
  - **Подписка** — оставьте подписку поставщика по умолчанию;
  - **Группа ресурсов** — test-vm-rg;
  - **Расположение** — local.

4. В колонке **Выбор размера** выберите **A1 Standard**, а затем щелкните **Выбрать**.  

5. В колонке **Параметры** оставьте значения по умолчанию и нажмите кнопку **ОК**.

6. В колонке **Сводка** нажмите кнопку **ОК**, чтобы создать виртуальную машину.  
> [!NOTE]
> Развертывание виртуальной машины занимает несколько минут.

7. Чтобы просмотреть данные новой виртуальной машины, выберите раздел **Виртуальные машины**, выполните поиск по имени **test-vm-1** и щелкните его.
    ![Отображение данных первой тестовой виртуальной машины](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>Очистка ресурсов
Когда вы войдете на виртуальную машину и убедитесь, что тестовый образ работает правильно, удалите тестовую группу ресурсов. Таким образом вы освободите ограниченные ресурсы, доступные для установки ASDK на одном узле.

Когда группа ресурсов, виртуальная машина и все связанные ресурсы будут не нужны, удалите их. Для этого выполните следующие действия:

1. Войдите на [портал администратора ASDK](https://adminportal.local.azurestack.external).
2. Выберите **Группы ресурсов** > **test-vm-rg** > **Удалить группу ресурсов**.
3. Введите имя группы ресурсов **test-vm-rg** и нажмите кнопку **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * добавление элемента Azure Stack Marketplace "Виртуальная машина Windows Server";
> * проверка доступности образа виртуальной машины; 
> * тестирование образа виртуальной машины.

Перейдите к следующему руководству, чтобы узнать, как создать предложение и план Azure Stack.

> [!div class="nextstepaction"]
> [Предложение служб IaaS для Azure Stack](asdk-offer-services.md)