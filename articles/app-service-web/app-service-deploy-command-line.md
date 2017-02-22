---
title: "Автоматизация развертывания приложения Azure с помощью программ командной строки | Документация Майкрософт"
description: "Получите сведения о развертывании приложения Azure из командной строки"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 8b65980c-eb75-44a2-8e0f-f9eb9e617d16
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 283b1cfda82b4f96ad5148c522a4c9833cb4c381
ms.openlocfilehash: e0e2e65557911bcac06d4dc355f47e9331934f8a


---
# <a name="automate-deployment-of-your-azure-app-with-command-line-tools"></a>Автоматизация развертывания приложения Azure с помощью программ командной строки
Вы можете автоматизировать развертывание приложений Azure с помощью программ командной строки. В этой статье указаны доступные программы и полезные ссылки на ресурсы, из которых можно узнать об использовании этих программ при развертывании. 

## <a name="a-namemsbuildaautomate-deployment-with-msbuild"></a><a name="msbuild"></a>Автоматизация развертывания с помощью MSBuild
Если для разработки используется [интегрированная среда разработки Visual Studio](#vs), то для автоматизации всех действий в ней можно использовать [MSBuild](http://msbuildbook.com/). Можно настроить MSBuild для использования [веб-развертывания](#webdeploy) или [FTP/FTPS](#ftp) для копирования файлов. Веб-развертывание может также автоматизировать множество других связанных с развертыванием задач, таких как развертывание баз данных.

Для получения дополнительных сведений об использовании командной строки для развертывания с помощью MSBuild см. следующие ресурсы:

* [Веб-развертывание ASP.NET с помощью Visual Studio: развертывание с использованием командной строки](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Десятая часть в серии учебников по развертыванию в Azure с использованием Visual Studio. Здесь показано, как использовать командную строку для развертывания после настройки профилей публикации в Visual Studio.
* [Microsoft Build Engine: использование MSBuild и службы сборки Team Foundation](http://msbuildbook.com/). Печатная копия книги, содержащая главы по использованию MSBuild для развертывания.

## <a name="a-namepowershellaautomate-deployment-with-windows-powershell"></a><a name="powershell"></a>Автоматизация развертывания с помощью Windows PowerShell
Можно выполнять функции развертывания с использованием MSBuild или FTP из [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). В этом случае можно также использовать набор командлетов Windows PowerShell, упрощающих вызов API-интерфейса управления Azure REST.

Для получения дополнительных сведений см. следующие ресурсы:

* [Развертывание веб-приложения, связанного с репозиторием GitHub](app-service-web-arm-from-github-provision.md)
* [Подготовка веб-приложения к работе с базой данных SQL](app-service-web-arm-with-sql-database-provision.md)
* [Предсказуемые подготовка и развертывание микрослужб в Azure](app-service-deploy-complex-application-predictably.md)
* [Создание реальных облачных приложений в Azure — полная автоматизация](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything) Глава электронной книги, в которой объясняется, как пример приложения, показанный в электронной книге, использует скрипты Windows PowerShell для создания тестовой среды Azure и выполнения в ней развертывания. Ссылки на дополнительную документацию по Azure PowerShell см. в разделе [Ресурсы](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources).
* [Использование скриптов Windows PowerShell для публикации в средах разработки и тестирования](../vs-azure-tools-publishing-using-powershell-scripts.md). Здесь объясняется, как использовать сценарии развертывания Windows PowerShell, которые создает Visual Studio.

## <a name="a-nameapiaautomate-deployment-with-net-management-api"></a><a name="api"></a>Автоматизация развертывания с помощью API управления .NET
Вы можете написать на C# код, выполняющий функции MSBuild или FTP для развертывания. В этом случае можно вызвать API-интерфейс управления REST в Azure для выполнения функций управления сайтом.

Для получения дополнительных сведений см. следующий ресурс:

* [Полная автоматизация с использованием библиотек управления Azure и .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Введение в API-интерфейс управления .NET и ссылки на дополнительную документацию.

## <a name="a-namecliadeploy-from-azure-command-line-interface-azure-cli"></a><a name="cli"></a>Развертывание из интерфейса командной строки Azure (Azure CLI)
Командную строку на машинах Windows, Mac или Linux можно использовать, чтобы выполнить развертывание с помощью FTP. В этом случае можно также вызвать API управления Azure REST, используя интерфейс командной строки Azure.

Для получения дополнительных сведений см. следующий ресурс:

* [Программы командной строки Azure](https://azure.microsoft.com/downloads/). Страница портала на сайте Azure.com с информацией о программе командной строки.

## <a name="a-namewebdeployadeploy-from-web-deploy-command-line"></a><a name="webdeploy"></a>Развертывание из командной строки веб-развертывания
[Веб-развертывание](http://www.iis.net/downloads/microsoft/web-deploy) — это программное обеспечение корпорации Майкрософт для развертывания в IIS, которое не только предоставляет интеллектуальные функции для синхронизации файлов, но также может выполнять или координировать множество других связанных с развертыванием задач, которые невозможно автоматизировать при использовании FTP. Например, веб-развертывание позволяет развернуть новую базу данных или обновления базы данных вместе с веб-приложением. Веб-развертывание может также минимизировать время, необходимое для обновления существующего сайта, поскольку интеллектуальная функция копирования позволяет копировать только измененные файлы. В Microsoft Visual Studio и Team Foundation Server предусмотрена встроенная поддержка веб-развертывания, но веб-развертывание можно использовать и непосредственно из командной строки для автоматизации развертывания. Команды веб-развертывания открывают множество возможностей, но их освоение может оказаться нелегкой задачей.

## <a name="more-resources"></a>Дополнительные ресурсы
Еще один вариант развертывания для автоматизации с помощью командной строки — использование облачной службы, например [Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy). Дополнительную информацию см. в статье [Развертывание веб-приложений ASP.NET на веб-сайтах Azure](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

Дополнительные сведения о программах командной строки см. в следующих ресурсах:

* [Простые веб-приложения: развертывание](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Блог Дэвида Эббо (David Ebbo) о средстве, которое он разработал, чтобы упростить использование веб-развертывания.
* [Средство веб-развертывания](http://technet.microsoft.com/library/dd568996). Официальная документация на сайте Microsoft TechNet. Устарела, но по-прежнему отлично подходит для начинающих.
* [Использование веб-развертывания](http://www.iis.net/learn/publish/using-web-deploy). Официальная документация на сайте Microsoft IIS.NET. Также устарела, но по-прежнему отлично подходит для начинающих.
* [Веб-развертывание ASP.NET с помощью Visual Studio: развертывание с использованием командной строки](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Подсистема сборки MSBuild используется в Visual Studio и может также использоваться из командной строки для развертывания веб-приложений в веб-приложениях. Этот учебник входит в серию учебных материалов, посвященных главным образом развертыванию в Visual Studio.




<!--HONumber=Jan17_HO1-->


