---
title: "Развертывание в службах приложений Azure с помощью Visual Studio 2015 | Документация Майкрософт"
description: "В этой статье описывается, как развернуть веб-приложение, приложение API или мобильное приложение в Azure для государственных организаций с помощью Visual Studio 2015 и пакета Azure SDK."
services: azure-government
cloud: gov
documentationcenter: 
author: sdubeymsft
manager: zakramer
ms.assetid: 8f9a3700-b9ee-43b7-b64d-2e6c3b57d4c0
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/03/2016
ms.author: sdubeymsft
translationtype: Human Translation
ms.sourcegitcommit: 40e2eea5c1a11dadb232c6ac7c224ca5fc847c02
ms.openlocfilehash: 9a5efdd4c94c7cfc7a63c478f1f5b8124a8cb5c6


---
# <a name="deploy-to-azure-app-services-using-visual-studio-2015"></a>Развертывание в службах приложений Azure с помощью Visual Studio 2015
В этой статье описывается развертывание приложений служб приложений Azure (приложения API, веб-приложения, мобильного приложения) в Azure для государственных организаций с помощью Visual Studio 2015.

## <a name="prerequisites"></a>Предварительные требования
* Чтобы установить и настроить Visual Studio 2015 и пакет Azure SDK, см. сведения в разделе [Предварительные требования] (../app-service-api/app-service-api-dotnet-get-started.md#prerequisites).
* Чтобы настроить Visual Studio для подключения к учетной записи Azure для государственных организаций, выполните [следующие инструкции] (documentation-government-manage-subscriptions.md#connecting-via-visual-studio).

## <a name="open-app-project-in-visual-studio"></a>Открытие проекта приложения в Visual Studio
* Откройте существующее приложение или проект приложения в Visual Studio, создайте проект, следуя [этим инструкциям] (../app-service-web/web-sites-dotnet-get-started.md#create-a-web-application), или скачайте пример приложения, используя [эти инструкции] (../app-service-api/app-service-api-dotnet-get-started.md#download-the-sample-application).
* Запустите приложение в Visual Studio, чтобы убедиться, что оно работает локально.

## <a name="deploy-to-azure-government"></a>Развертывание в Azure для государственных организаций
* Если **Visual Studio настроена для подключения к учетной записи Azure для государственных организаций** (инструкции приведены в разделе предварительных требований), процедура развертывания в службах приложений выполняется так же, как и для общедоступной версии Azure.
* Чтобы развернуть приложение, выполните [эти действия] (../app-service-api/app-service-api-dotnet-get-started.md#a-idcreateapiappa-create-an-api-app-in-azure-and-deploy-code-to-it).

### <a name="references"></a>Ссылки
* [Развертывание веб-приложения ASP.NET в службе приложений Azure с помощью Visual Studio] (../app-service-web/web-sites-dotnet-get-started.md)
* Другие способы развертывания см. в статье [Развертывание приложения в службе приложений Azure] (../app-service-web/web-sites-deploy.md)
* Общую документацию по службе приложений см. в разделе [Служба приложений. Документация по приложениям API] (../app-service-api/index.md)

## <a name="next-steps"></a>Дальнейшие действия
Чтобы получать дополнительные сведения и обновления, подпишитесь на [блог Microsoft Azure для государственных организаций](https://blogs.msdn.microsoft.com/azuregov/).



<!--HONumber=Jan17_HO1-->


