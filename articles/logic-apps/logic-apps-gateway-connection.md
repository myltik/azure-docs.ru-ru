---
title: "Подключение приложений логики к локальному шлюзу данных | Документация Майкрософт"
description: "Сведения о том, как создать подключение к локальному шлюзу данных из приложения логики."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 0a16f22b6e3bb60091409c64b631afcba3d6db10


---
# <a name="connect-to-the-on-premises-data-gateway-for-logic-apps"></a>Подключение к локальному шлюзу данных для приложений логики
Поддерживаемые соединители приложений логики позволяют настроить подключение для доступа к локальным данным через локальный шлюз данных.  Приведенные ниже пошаговые инструкции помогут вам узнать, как установить и настроить локальный шлюз данных для работы с приложением логики.

## <a name="prerequisites"></a>Предварительные требования
  * Чтобы связать локальный шлюз данных с вашей учетной записью (на основе Azure Active Directory), необходимо использовать рабочий или учебный электронный адрес в Azure.
  * Если вы используете учетную запись Майкрософт (например, @outlook.com,, @live.com)), то можно использовать учетную запись Azure, чтобы создать рабочий или учебный электронный адрес, выполнив [эти указания](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal). 
  * Локальный шлюз данных должен быть [установлен на локальном компьютере](logic-apps-gateway-install.md).
  * Не должно быть заявок на доступ к этому шлюзу от другого локального шлюза данных Azure ([заявка осуществляется во время создания на шаге 2, приведенном ниже](#2-create-an-azure-on-premises-data-gateway-resource)). Установка может быть связана только с одним ресурсом шлюза.

## <a name="installing-and-configuring-the-connection"></a>Установка и настройка подключения
### <a name="1-install-the-on-premises-data-gateway"></a>1. Установка локального шлюза данных
Сведения об установке локального шлюза данных см. [в этой статье](logic-apps-gateway-install.md).  Шлюз нужно установить на локальном компьютере, прежде чем можно будет продолжить процедуру.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. Создание ресурса локального шлюза данных Azure
После установки необходимо связать локальный шлюз данных с подпиской Azure.

1. Войдите в Azure с помощью рабочего или учебного электронного адреса, который использовался во время установки шлюза.
2. Нажмите кнопку **Новый ресурс** .
3. Найдите и выберите **Локальный шлюз данных**
4. Введите информацию, которая нужна для связывания шлюза с учетной записью, включая соответствующее **имя установки**
   
    ![Подключение к локальному шлюзу данных][1]
5. Нажмите кнопку **Создать** , чтобы создать ресурс.

### <a name="3-create-a-logic-app-connection-in-the-designer"></a>3. Создание подключения приложения логики в конструкторе
Теперь, когда подписка Azure связана с экземпляром локального шлюза данных, можно создать подключение к нему из приложения логики.

1. Откройте приложение логики и выберите соединитель, который поддерживает локальные подключения (на момент написания этой статьи — SQL Server).
2. Установите флажок **Connect via on-premises data gateway**
   
    ![Создание подключения к шлюзу в конструкторе приложений логики][2]
3. Выберите **шлюз** для подключения и введите другие необходимые сведения.
4. Нажмите кнопку **Создать** , чтобы создать подключение.

Теперь подключение должно быть настроено для использования в приложении логики.  

## <a name="next-steps"></a>Дальнейшие действия
* [Распространенные примеры и сценарии использования приложений логики](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Возможности интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-gateway-connection/createblade.png
[2]: ./media/logic-apps-gateway-connection/blankconnection.png
[3]: ./media/logic-apps-logic-gateway-connection/checkbox.png



<!--HONumber=Jan17_HO3-->


