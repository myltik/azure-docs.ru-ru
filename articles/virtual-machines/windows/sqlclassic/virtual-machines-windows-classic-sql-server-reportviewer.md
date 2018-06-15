---
title: Использование ReportViewer для веб-сайта | Документация Майкрософт
description: В этом разделе описано, как создать веб-сайт Microsoft Azure с элементом управления ReportViewer Visual Studio, который отображает отчет, хранящийся на виртуальной машине Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 78b76318-d9bf-48ef-9d9e-d1b7d8cf3042
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: af8a4a9c25005925bed3ddb78ced618e669f7f09
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31424624"
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Использование ReportViewer для веб-сайта, размещенного в Azure
> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

Вы можете создать веб-сайт Microsoft Azure с элементом управления ReportViewer Visual Studio, который отображает отчет, хранящийся на виртуальной машине Microsoft Azure. Элемент управления ReportViewer представляет собой веб-приложение, создаваемое с помощью шаблона веб-приложения ASP.NET.

> [!IMPORTANT]
> Шаблоны веб-приложений MVC ASP.NET не поддерживают элемент управления ReportViewer.

Чтобы включить элемент управления ReportViewer в свой веб-сайт Microsoft Azure, необходимо выполнить следующие задачи.

* **Добавить** сборки в пакет развертывания
* **Настроить** проверку подлинности и авторизацию
* **Опубликовать** веб-приложение ASP.NET в Azure

## <a name="prerequisites"></a>предварительным требованиям
Просмотрите раздел "Общие советы и рекомендации" статьи [Бизнес-аналитика SQL Server на виртуальных машинах Azure](../classic/ps-sql-bi.md).

> [!NOTE]
> Элементы управления ReportViewer входят в состав Visual Studio Standard Edition или выше. Если используется Web Developer Express Edition, необходимо установить [MICROSOFT REPORT VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) , чтобы воспользоваться функциями среды выполнения ReportViewer.
> 
> ReportViewer в режиме локальной обработки не поддерживается в Microsoft Azure.

## <a name="adding-assemblies-to-the-deployment-package"></a>Добавление сборок в пакет развертывания
Когда приложение ASP.NET размещено локально, сборки ReportViewer обычно устанавливаются непосредственно в глобальный кэш сборок (GAC) сервера IIS во время установки Visual Studio и приложение может обращаться к этим сборкам напрямую. Однако при размещении приложения ASP.NET в облаке Microsoft Azure не позволяет устанавливать что-либо в глобальный кэш сборок, поэтому необходимо убедиться в том, что сборки ReportViewer доступны для приложения локально. Для этого можно добавить ссылки на них в свой проект и настроить их для копирования локально.

В режиме удаленной обработки элемент управления ReportViewer использует следующие сборки.

* **Microsoft.ReportViewer.WebForms.dll**: содержит код ReportViewer, который необходим для использования ReportViewer на вашей странице. Ссылка на эту сборку добавляется в проект, когда вы помещаете элемент управления ReportViewer на страницу ASP.NET проекта.
* **Microsoft.ReportViewer.Common.dll**: содержит классы, используемые элементом управления ReportViewer во время выполнения. Она не добавляется в проект автоматически.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Добавление ссылки на сборку Microsoft.ReportViewer.Common
* Щелкните узел **Ссылки** вашего проекта правой кнопкой мыши, выберите **Добавить ссылку**, выберите сборку на вкладке .NET и нажмите кнопку **ОК**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Включение локальной доступности сборок для приложения ASP.NET
1. В папке **Ссылки** выберите сборку Microsoft.ReportViewer.Common, чтобы ее свойства появились на панели "Свойства".
2. На панели "Свойства" задайте для свойства **Копировать локально** значение True.
3. Повторите шаги 1 и 2 для сборки Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Получение языкового пакета ReportViewer
1. Установите соответствующий распространяемый пакет Microsoft Report Viewer 2012 Runtime из [Центра загрузки Майкрософт](http://go.microsoft.com/fwlink/?LinkId=317386).
2. Выберите язык из раскрывающегося списка. После этого произойдет переход на соответствующую страницу центра загрузки.
3. Нажмите **Скачать** , чтобы начать загрузку файла ReportViewerLP.exe.
4. После загрузки файла ReportViewerLP.exe нажмите кнопку **Запустить**, чтобы немедленно установить пакет, или кнопку **Сохранить**, чтобы сохранить его на компьютере. При выборе кнопки **Сохранить**запомните название каталога, в который сохраняете файл.
5. Перейдите в каталог, в котором был сохранен файл. Щелкните файл ReportViewerLP.exe правой кнопкой мыши, выберите **Запуск от имени администратора**, а затем нажмите кнопку **Да**.
6. После запуска ReportViewerLP.exe вы увидите, что в папке c:\windows\assembly появились файлы ресурсов **Microsoft.ReportViewer.Webforms.Resources** и **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Настройка локализованного элемента управления ReportViewer
1. Загрузите и установите распространяемый пакет Microsoft Report Viewer 2012 Runtime, следуя приведенным выше инструкциям.
2. Создайте каталог <language> в проекте и скопируйте в него файлы сборок соответствующих ресурсов. Необходимо скопировать следующие файлы сборок ресурсов: **Microsoft.ReportViewer.Webforms.Resources.dll** и **Microsoft.ReportViewer.Common.Resources.dll**. Выберите файлы сборок ресурсов и в области "Свойства" задайте для параметра **Копировать в выходной каталог** значение **Всегда копировать**.
3. Настройте Culture и UI Culture для веб-проекта. Дополнительные сведения о настройке Culture и UI Culture для веб-страницы ASP.NET см. в статье [Практическое руководство. Установка значений Culture и UICulture для глобализации веб-страниц ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Настройка проверки подлинности и авторизации
В ReportViewer должны использоваться правильные учетные данные для проверки подлинности на сервере отчетов. Для доступа к желаемым отчетам учетные данные должны быть авторизованы сервером отчетов. Сведения о проверке подлинности приведены в техническом документе [Элемент управления средства просмотра отчетов Reporting Services и серверы отчетов на основе виртуальной машины Microsoft Azure](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Публикация веб-приложения ASP.NET в Azure
Инструкции по публикации веб-приложения ASP.NET в Azure см. в статьях [Инструкции. Миграция и публикация веб-приложения в облачную службу Azure из среды Visual Studio](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) и [Начало работы с веб-приложениями и ASP.NET](../../../app-service/app-service-web-get-started-dotnet.md).

> [!IMPORTANT]
> Если в контекстном меню в обозревателе решений не появилась команда "Добавить проект развертывания Azure" или "Добавить проект облачной службы Azure", может потребоваться изменить целевую платформу для проекта на .NET Framework 4.
> 
> Эти две команды по сути выполняют одну и ту же функцию. В контекстном меню появится одна или другая команда в зависимости от того, какая версия Microsoft Azure SDK установлена.
> 
> 

## <a name="resources"></a>Ресурсы
[Отчеты Майкрософт](http://go.microsoft.com/fwlink/?LinkId=205399)

[SQL Server Business Intelligence на виртуальных машинах Azure](../classic/ps-sql-bi.md)

[Использование PowerShell для создания виртуальной машины Azure с помощью сервера отчетов, работающего в собственном режиме](../classic/ps-sql-report.md)

