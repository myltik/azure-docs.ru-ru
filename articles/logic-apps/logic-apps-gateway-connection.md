---
title: "Доступ к локальным данным в Azure Logic Apps | Документация Майкрософт"
description: "Сведения о том, как приложениям логики получить доступ к локальным данным с помощью подключения к локальному шлюзу данных."
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
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: ce0be184fe11a7e5873639d79961e98de730ec86
ms.lasthandoff: 03/10/2017


---
# <a name="connect-to-on-premises-data-from-logic-apps"></a>Подключение к локальным данным из приложений логики

Чтобы получить доступ к локальным данным, вы можете настроить подключение к локальному шлюзу данных для поддерживаемых соединителей Azure Logic Apps. Приведенные ниже пошаговые инструкции помогут вам узнать, как установить и настроить локальный шлюз данных для работы с приложениями логики.
Локальный шлюз данных поддерживает подключения к следующим источникам данных:

*   BizTalk Server
*    DB2  
*   Файловая система
*   Informix
*   Магический квадрант
*    База данных Oracle 
*   сервер приложений SAP; 
*   сервер сообщений SAP;
*    SQL Server

Дополнительные сведения о подключениях, приведенных выше, см. в статье [Список соединителей](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="requirements"></a>Требования

* Чтобы связать локальный шлюз данных со своей учетной записью (на основе Azure Active Directory), у вас должен быть рабочий или учебный электронный адрес в Azure.

* Если вы используете учетную запись Майкрософт, например @outlook.com, вы можете использовать учетную запись Azure для [создания рабочего или учебного электронного адреса](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal).

* [Локальный шлюз данных должен быть установлен на локальном компьютере](logic-apps-gateway-install.md).

* Вы можете связать установку только с одним ресурсом шлюза. Не должно быть заявок на доступ к этому шлюзу от другого локального шлюза данных Azure Заявка осуществляется во время [создания на шаге 2, приведенном ниже](#2-create-an-azure-on-premises-data-gateway-resource).

## <a name="install-and-configure-the-connection"></a>Установка и настройка подключения

### <a name="1-install-the-on-premises-data-gateway"></a>1. Установка локального шлюза данных

Чтобы установить локальный шлюз данных, следуйте инструкциям [здесь](logic-apps-gateway-install.md). Перед тем как продолжить, убедитесь, что шлюз данных установлен на локальном компьютере.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. Создание ресурса локального шлюза данных Azure

После установки шлюза необходимо связать его с подпиской Azure.

1. Войдите в Azure с помощью рабочего или учебного электронного адреса, который использовался во время установки шлюза.
2. Выберите **Создать**.
3. Найдите и выберите **локальный шлюз данных**.
4. Введите информацию, которая нужна для связывания шлюза с учетной записью, включая соответствующее **имя установки**.
   
    ![Подключение к локальному шлюзу данных][1]

5. Выберите **Создать**, чтобы создать группу ресурсов.

### <a name="3-create-a-logic-app-connection-in-logic-app-designer"></a>3. Создание подключения приложения логики в конструкторе приложений логики

Теперь, когда подписка Azure связана с экземпляром локального шлюза данных, вы можете создать подключение к нему из приложения логики.

1. Откройте приложение логики и выберите соединитель, поддерживающий локальные подключения, например SQL Server.
2. Установите флажок **Connect via on-premises data gateway** (Подключение через локальный шлюз данных).
   
    ![Создание подключения к шлюзу в конструкторе приложений логики][2]

3. Выберите **Шлюз**, к которому нужно подключиться, и введите нужные для подключения сведения.
4. Нажмите кнопку **Создать**, чтобы создать подключение.

Теперь приложение логики может использовать настроенное подключение.

## <a name="next-steps"></a>Дальнейшие действия

* [Распространенные примеры и сценарии использования приложений логики](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Возможности интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-gateway-connection/createblade.png
[2]: ./media/logic-apps-gateway-connection/blankconnection.png
[3]: ./media/logic-apps-logic-gateway-connection/checkbox.png

