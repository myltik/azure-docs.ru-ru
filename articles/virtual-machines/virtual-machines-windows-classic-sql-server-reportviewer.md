<properties 
	pageTitle="Использование ReportViewer для веб-сайта | Microsoft Azure"
	description="В этом разделе описано, как создать веб-сайт Microsoft Azure с элементом управления ReportViewer Visual Studio, который отображает отчет, хранящийся на виртуальной машине Microsoft Azure."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="guyinacube"
	manager="jhubbard"
	editor="monicar" 
	tags="azure-service-management" />
<tags 
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/14/2016"
	ms.author="asaxton" />

# Использование ReportViewer для веб-сайта, размещенного в Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Вы можете создать веб-сайт Microsoft Azure с элементом управления ReportViewer Visual Studio, который отображает отчет, хранящийся на виртуальной машине Microsoft Azure. Элемент управления ReportViewer представляет собой веб-приложение, создаваемое с помощью шаблона веб-приложения ASP.NET.

>[AZURE.IMPORTANT] Шаблоны веб-приложений MVC ASP.NET не поддерживают элемент управления ReportViewer.

Чтобы включить элемент управления ReportViewer в свой веб-сайт Microsoft Azure, необходимо выполнить следующие задачи.

- **Добавить** сборки в пакет развертывания

- **Настроить** проверку подлинности и авторизацию

- **Опубликовать** веб-приложение ASP.NET в Azure

## Предварительные требования

Просмотрите раздел "Общие советы и рекомендации" статьи [Бизнес-аналитика SQL Server на виртуальных машинах Azure](virtual-machines-windows-classic-ps-sql-bi.md).

>[AZURE.NOTE] Элементы управления ReportViewer входят в состав Visual Studio Standard Edition или выше. Если используется Web Developer Express Edition, необходимо установить [MICROSOFT REPORT VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747), чтобы воспользоваться функциями среды выполнения ReportViewer.
>
>ReportViewer в режиме локальной обработки не поддерживается в Microsoft Azure.

Просмотрите технический документ [Элемент просмотра отчетов Reporting Services и серверы отчетов на основе виртуальных машин Microsoft Azure](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx).

## Добавление сборок в пакет развертывания

При локальном размещении приложения ASP.NET сборки ReportViewer обычно устанавливаются непосредственно в глобальный кэш сборок (GAC) сервера IIS во время установки Visual Studio и приложение может обращаться к сборкам напрямую. Однако при размещении приложения ASP.NET в облаке Microsoft Azure не позволяет устанавливать что-либо в глобальный кэш сборок, поэтому необходимо убедиться в том, что сборки ReportViewer доступны для приложения локально. Для этого можно добавить ссылки на них в свой проект и настроить их для копирования локально.

В режиме удаленной обработки элемент управления ReportViewer использует следующие сборки.

- **Microsoft.ReportViewer.WebForms.dll**: содержит код ReportViewer, который необходим для использования ReportViewer на вашей странице. Ссылка на эту сборку добавляется в проект, когда вы помещаете элемент управления ReportViewer на страницу ASP.NET проекта.

- **Microsoft.ReportViewer.Common.dll**: содержит классы, используемые элементом управления ReportViewer во время выполнения. Она не добавляется в проект автоматически.

### Добавление ссылки на сборку Microsoft.ReportViewer.Common

- Щелкните узел **Ссылки** вашего проекта правой кнопкой мыши, нажмите **Добавить ссылку**, выберите сборку на вкладке .NET и нажмите кнопку **ОК**.

### Включение локальной доступности сборок для приложения ASP.NET

1. В папке **Ссылки** выберите сборку Microsoft.ReportViewer.Common, чтобы ее свойства появились на панели "Свойства".

1. На панели "Свойства" задайте для свойства **Копировать локально** значение True.

1. Повторите шаги 1 и 2 для сборки Microsoft.ReportViewer.WebForms.

### Получение языкового пакета ReportViewer

1. Установите соответствующий распространяемый пакет Microsoft Report Viewer 2012 Runtime из [Центра загрузки Майкрософт](http://go.microsoft.com/fwlink/?LinkId=317386).

1. Выберите язык из раскрывающегося списка. После этого произойдет переход на соответствующую страницу центра загрузки.

1. Нажмите **Скачать**, чтобы начать загрузку файла ReportViewerLP.exe.

1. После загрузки файла ReportViewerLP.exe нажмите кнопку **Запустить**, чтобы немедленно установить пакет, или кнопку **Сохранить**, чтобы сохранить его на компьютере. При выборе кнопки **Сохранить** запомните название каталога, в который сохраняете файл.

1. Перейдите в каталог, в котором был сохранен файл. Щелкните файл ReportViewerLP.exe правой кнопкой мыши, выберите **Запуск от имени администратора**, а затем нажмите кнопку **Да**.

1. После запуска ReportViewerLP.exe вы увидите, что в папке c:\\windows\\assembly появились файлы ресурсов **Microsoft.ReportViewer.Webforms.Resources** и **Microsoft.ReportViewer.Common.Resources**.

### Настройка локализованного элемента управления ReportViewer

1. Загрузите и установите распространяемый пакет Microsoft Report Viewer 2012 Runtime, следуя приведенным выше инструкциям.

1. Создайте каталог <название\_языка> в проекте и скопируйте в него файлы сборок соответствующих ресурсов. Необходимо скопировать следующие файлы сборок ресурсов: **Microsoft.ReportViewer.Webforms.Resources.dll** и **Microsoft.ReportViewer.Common.Resources.dll**. Выберите файлы сборок ресурсов и в области "Свойства" задайте для параметра **Копировать в выходной каталог** значение **Всегда копировать**.

1. Настройте Culture и UI Culture для веб-проекта. Дополнительные сведения о настройке Culture и UI Culture для веб-страницы ASP.NET см. в статье [Практическое руководство. Установка значений Culture и UICulture для глобализации веб-страниц ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## Настройка проверки подлинности и авторизации

В ReportViewer должны использоваться правильные учетные данные для проверки подлинности на сервере отчетов. Для доступа к желаемым отчетам учетные данные должны быть авторизованы сервером отчетов. Сведения о проверке подлинности приведены в техническом документе [Элемент управления средства просмотра отчетов Reporting Services и серверы отчетов на основе виртуальной машины Microsoft Azure](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## Публикация веб-приложения ASP.NET в Azure

Инструкции по публикации веб-приложения ASP.NET в Azure см. в статьях [Инструкции. Миграция и публикация веб-приложения в облачную службу Azure из среды Visual Studio](../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) и [Начало работы с веб-приложениями и ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).

>[AZURE.IMPORTANT] Если в контекстном меню в обозревателе решений не появилась команда "Добавить проект развертывания Azure" или "Добавить проект облачной службы Azure", может потребоваться изменить целевую платформу для проекта на .NET Framework 4.
>
>Эти две команды по сути выполняют одну и ту же функцию. В контекстном меню появится одна или другая команда в зависимости от того, какая версия Microsoft Azure SDK установлена.

## Ресурсы

[Отчеты Майкрософт](http://go.microsoft.com/fwlink/?LinkId=205399)

[SQL Server Business Intelligence на виртуальных машинах Azure](virtual-machines-windows-classic-ps-sql-bi.md)

[Использование PowerShell для создания виртуальной машины Azure с помощью сервера отчетов, работающего в собственном режиме](virtual-machines-windows-classic-ps-sql-report.md)

[Элемент управления средства просмотра отчетов Reporting Services и серверы отчетов на основе виртуальных машин Microsoft Azure](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)

<!---HONumber=AcomDC_0629_2016-->