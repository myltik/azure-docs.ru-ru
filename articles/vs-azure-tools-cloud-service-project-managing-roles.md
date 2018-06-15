---
title: Управление ролями в облачных службах Azure с помощью Visual Studio | Документация Майкрософт
description: Узнайте, как добавлять и удалять роли в облачных службах Azure с помощью Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 5ec9ae2e-8579-4e5d-999e-8ae05b629bd1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: d0a2148274cd41654eb789772b3ea46bc4ed01aa
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793976"
---
# <a name="managing-roles-in-azure-cloud-services-with-visual-studio"></a>Управление ролями в облачных службах Azure с помощью Visual Studio
После создания облачной службы Azure можно добавить в нее новые роли или удалить из нее существующие. Вы можете импортировать существующий проект и преобразовать его в роль. Например, можно импортировать веб-приложение ASP.NET и использовать его в качестве веб-роли.

## <a name="adding-a-role-to-an-azure-cloud-service"></a>Добавление роли в облачную службу Azure
Ниже приведены пошаговые инструкции по добавлению веб-роли или рабочей роли в проект облачной службы Azure в Visual Studio.

1. Создайте или откройте проект облачной службы Azure в среде Visual Studio.

1. В **обозревателе решений** разверните узел проекта.

1. Щелкните правой кнопкой мыши узел **Роли**, чтобы отобразилось контекстное меню. В контекстном меню выберите команду **Добавить**, а затем выберите существующую веб-роль или рабочую роль в текущем решении либо создайте проект веб-роли или рабочей роли. Вы также можете выбрать соответствующий проект, например проект веб-приложения ASP.NET, и связать его с проектом роли.

    ![Параметры меню для добавления роли в проект облачной службы Azure](media/vs-azure-tools-cloud-service-project-managing-roles/add-role.png)

## <a name="removing-a-role-from-an-azure-cloud-service"></a>Удаление роли из облачной службы Azure
Ниже приведены пошаговые инструкции по удалению веб-роли или рабочей роли из проекта облачной службы Azure в Visual Studio.

1. Создайте или откройте проект облачной службы Azure в среде Visual Studio.

1. В **обозревателе решений** разверните узел проекта.

1. Разверните узел **Роли**.

1. Щелкните правой кнопкой мыши узел, который необходимо удалить, и в контекстном меню выберите **Удалить**. 

    ![Параметры меню для добавления роли в облачную службу Azure](media/vs-azure-tools-cloud-service-project-managing-roles/remove-role.png)

## <a name="readding-a-role-to-an-azure-cloud-service-project"></a>Повторное добавление роли в проект облачной службы Azure
Если вы удалите роль из своего проекта облачной службы, но позже захотите снова добавить ее в проект, то будут добавлены только объявления роли и ее основные атрибуты, такие как сведения диагностики и информация о конечных точках. В файлы `ServiceDefinition.csdef` или `ServiceConfiguration.cscfg` не добавляются другие ресурсы и ссылки. Если вы хотите добавить эту информацию в файлы, то придется сделать это вручную.

Предположим, вы удалили роль веб-службы, а затем решили вернуть ее обратно в решение. Если это сделать, то произойдет ошибка. Чтобы ее избежать, необходимо добавить элемент `<LocalResources>`, показанный в следующем XML-блоке, обратно в файл `ServiceDefinition.csdef`. Используйте имя роли веб-службы, добавленной в проект, в составе атрибута name в элементе **<LocalStorage>** . В этом примере используется роль веб-службы с именем **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>Дополнительная информация
- [Настройка ролей для облачной службы Azure в среде Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md)
