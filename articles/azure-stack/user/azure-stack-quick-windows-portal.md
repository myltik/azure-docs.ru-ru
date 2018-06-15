---
title: Краткое руководство по Azure Stack. Создание виртуальной машины Windows
description: Краткое руководство по Azure Stack. Создание виртуальной машины Windows с помощью портала
services: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 5776fc472483018eb2c9e4f8962d0b1e8bce8081
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32155587"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Краткое руководство. Создание виртуальной машины Windows Server с помощью портала Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Вы можете создать виртуальную машину Windows Server 2016 с помощью портала Azure Stack. Выполните описанные в этой статье действия, чтобы создать и использовать виртуальную машину.

## <a name="sign-in-to-the-azure-stack-portal"></a>Вход на портал Azure Stack

Войдите на портал Azure Stack. Адрес портала Azure Stack зависит от того, к какому продукту Azure Stack вы подключаетесь:

* Если вам нужен Пакет средств разработки Azure Stack (ASDK), перейдите по адресу https://portal.local.azurestack.external.
* При работе с интегрированной системой Azure Stack используйте URL-адрес, предоставленный оператором Azure Stack.

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

1. Щелкните **Создать** > **Вычисления** > **Windows Server 2016 Datacenter Eval** > **Создать**. Если вы не видите вариант **Windows Server 2016 Datacenter Eval**, обратитесь к оператору Azure Stack. Этот вариант необходимо добавить в Marketplace, как описано в руководстве по [добавлению образа виртуальной машины Windows Server 2016 в Azure Stack Marketplace](../azure-stack-add-default-image.md).

    ![Создание виртуальной машины Windows с помощью портала](media/azure-stack-quick-windows-portal/image01.png)
2. В колонке **Основные сведения** введите **имя**, **имя пользователя** и **пароль**. Выберите **подписку**. Создайте **группу ресурсов** или выберите существующую, укажите **расположение**, а затем нажмите **ОК**.

    ![Настройка основных параметров.](media/azure-stack-quick-windows-portal/image02.png)
3. В разделе **Выбор размера**, щелкните **D1 Standard** > **Выбрать**.
    ![Выбор размера виртуальной машины](media/azure-stack-quick-windows-portal/image03.png)
4. В разделе **Параметры** оставьте значения по умолчанию и нажмите кнопку **ОК**.
    ![Настройка параметров виртуальной машины](media/azure-stack-quick-windows-portal/image04.png)
5. В колонке **Сводка** нажмите кнопку **ОК**, чтобы создать виртуальную машину.
    ![Просмотр сводки и создание виртуальной машины](media/azure-stack-quick-windows-portal/image05.png)
6. Чтобы просмотреть новую виртуальную машину, щелкните **Все ресурсы**, а затем найдите и щелкните в результатах поиска имя виртуальной машины.
    ![Просмотр виртуальной машины](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Завершив работу с виртуальной машиной, удалите ее и все связанные ресурсы. Для этого выберите группу ресурсов на странице виртуальной машины и нажмите кнопку **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

С помощью этого краткого руководства вы развернули простую виртуальную машину Windows Server. Дополнительные сведения о виртуальных машинах Azure Stack см. в [рекомендациях по работе с виртуальными машинами в Azure Stack](azure-stack-vm-considerations.md).
