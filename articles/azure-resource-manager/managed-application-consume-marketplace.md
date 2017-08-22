---
title: "Использование управляемого приложения Azure в Marketplace | Документация Майкрософт"
description: "В этой статье объясняется, как создать управляемое приложение Azure, которое будет доступно в Marketplace."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/11/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: baf456740bddd562391ed64d707f990c8921d710
ms.contentlocale: ru-ru
ms.lasthandoff: 07/13/2017

---

# <a name="consume-azure-managed-applications-in-the-marketplace"></a>Использование управляемых приложений Azure в Marketplace

Как обсуждалось в статье [Обзор управляемых приложений Azure](managed-application-overview.md), существует два сценария работы с управляемыми приложениями: первый — для издателя или поставщика, желающего создать управляемое приложение и предоставить его пользователям, и второй — для пользователя, использующего управляемое приложение. Эта статья посвящена второму сценарию. В ней описывается, как можно развернуть управляемое приложение из Microsoft Azure Marketplace.

## <a name="create-from-the-marketplace"></a>Создание из Marketplace

Управляемое приложение развертывается из Marketplace аналогично развертыванию любого типа ресурсов из Marketplace. 

На портале выберите **+ Создать** и найдите тип решения, которое следует развернуть. Из доступных вариантов выберите тот, который вам нужен.

![Поиск решений](./media/managed-application-consume-marketplace/search-apps.png)

Просмотрите сводку приложения и выберите **Создать**.

![Создание управляемого приложения](./media/managed-application-consume-marketplace/create-marketplace-managed-app.png)

Как и в любом другом решении, отобразятся поля, в которых следует указать значения. Эти поля различаются в зависимости от типа управляемого приложения, которое создается. 

## <a name="view-support-information"></a>Просмотр сведений о поддержке

После того как управляемое приложение будет развернуто, просмотрите сведения о его поддержке. Сведения о поддержке перечисляются в колонке управляемого приложения.

![support](./media/managed-application-consume-marketplace/support.png)

## <a name="view-publisher-permissions"></a>Просмотр разрешений издателя

Поставщику, который управляет вашим приложением, предоставляется доступ к вашим ресурсам. Чтобы просмотреть эти разрешения, выберите **Авторизации**.

![Авторизации](./media/managed-application-consume-marketplace/authorizations.png)

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о публикации управляемого приложения в Marketplace см. в статье [Azure managed applications in the Marketplace](managed-application-author-marketplace.md) (Управляемые приложения Azure в Marketplace).
* Сведения о публикации управляемых приложений, которые доступны только пользователям вашей организации, см. в статье [Создание и публикация управляемого приложения Azure](managed-application-publishing.md).
* Сведения об использовании управляемых приложений, которые доступны только пользователям вашей организации, см. в статье [Использование управляемого приложения Azure](managed-application-consumption.md).
