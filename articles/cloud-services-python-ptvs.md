<properties
	pageTitle="Веб-роли и рабочие роли Python с помощью средств Python 2.2 для Visual Studio | Microsoft Azure"
	description="Обзор использования Python Tools в Visual Studio для создания облачных служб Azure, включая веб-роли и рабочие роли."
	services=""
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.date="08/30/2015"
	ms.author="huvalo"/>




# Веб-роли и рабочие роли Python с использованием средств Python 2.2 для Visual Studio

В данном руководстве предоставлен обзор способов использования веб-ролей и рабочих ролей Python с помощью [Средств Python для Visual Studio][].

## Предварительные требования

 - Visual Studio 2013 или 2015
 - [Средства Python 2.2 для Visual Studio][] (PTVS)
 - [Пакет SDK Azure для VS 2013][] или [Пакет SDK Azure для VS 2015][]
 - [Python 2.7 (32-разрядный)][] или [Python 3.4 (32-разрядный)][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Что такое веб-роли и рабочие роли Python?

Azure предоставляет три вычислительных модели запуска приложений: [веб-приложения в службе приложений Azure][execution model-web sites], [виртуальные машины Azure][execution model-vms] и [облачные службы Azure][execution model-cloud services]. Все три модели поддерживают Python. Облачные службы, которые включают веб-роли и рабочие роли, предоставляют *платформу как службу (PaaS)*. В рамках облачной службы веб-роль предоставляет выделенный веб-сервер IIS для размещения веб-приложений переднего плана, тогда как рабочая роль может выполнять асинхронные, долгосрочные или постоянные задачи, независимые от пользовательских действий и пользовательского ввода.

Дополнительные сведения см. в разделе [Что такое облачная служба?].

> [AZURE.NOTE]*Требуется собрать простой веб-сайт?* Если вам необходимо создать сайт с простым интерфейсом, воспользуйтесь легкими веб-приложениями в службе приложений Azure. По мере роста веб-сайта и изменения требований можно легко выполнить обновление к облачной службе. В <a href="/develop/python/">Центре разработчиков Python</a> можно найти статьи, посвященные разработке веб-приложений в службе приложений Azure. <br />


## Создание проекта

В Visual Studio можно выбрать пункт **Облачная служба Azure** в диалоговом окне **Новый проект** из меню **Python**.

![Диалоговое окно "Новый проект"](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

В мастере настройки облачной службы Azure можно создать новую веб-роль и рабочие роли.

![Диалоговое окно Облачной службы Azure](./media/cloud-services-python-ptvs/new-service-wizard.png)

Шаблон рабочей роли входит в состав стандартного кода для подключения к учетной записи хранения Azure или к служебной шине Azure.

![Решение для Облачной службы](./media/cloud-services-python-ptvs/worker.png)

Добавить веб-роль или рабочую роль к уже существующей облачной службе можно в любое время. Также можно добавить к собственному решению существующий проект или создать новые проекты.

![Команда добавления роли](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

В облачной службе могут присутствовать роли, реализованные на различных языках программирования. Например, это может быть веб-роль на Python, реализованная с помощью Django, с рабочими ролями на Python или C#. С помощью очередей служебной шины или очередей хранилища можно легко обмениваться данными между ролями.

## Локальный запуск

Если настроить проект облачной службы в качестве запускаемого при включении и нажать клавишу F5, тогда эта облачная служба запустится в локальном эмуляторе Azure.

Хотя PTVS поддерживает возможность запуска в эмуляторе, процедура отладки (например, установка точек останова) в этом режиме не работает.

Чтобы отладить веб-роли и рабочие роли, необходимо установить в настройках проекта роли параметр, определяющий его запуск при включении, и выполнить отладку таким образом. В качестве запускаемых при включении можно указать несколько проектов. Щелкните правовой кнопкой мыши на решении и выберите пункт **Настроить запускаемые при включении проекты**.

![Свойства запускаемого при включении проекта в решении](./media/cloud-services-python-ptvs/startup.png)

## Публикация в Azure

Чтобы выполнить публикацию, щелкните правой кнопкой мыши на проекте облачной службы в решении и выберите пункт **Опубликовать**.

![Вход для публикации в Microsoft Azure](./media/cloud-services-python-ptvs/publish-sign-in.png)

На странице настроек отметьте облачную службу, которую хотите опубликовать.

![Настройки публикации в Microsoft Azure](./media/cloud-services-python-ptvs/publish-settings.png)

Можно создать новую облачную службу при ее отсутствии.

![Диалоговое окно создания Облачной службы](./media/cloud-services-python-ptvs/publish-create-cloud-service.png)

Также рекомендуется разрешить устанавливать сетевые подключения через удаленные рабочие столы к машинам для выполнения отладки сбоев.

![Диалоговое окно настройки удаленного рабочего стола](./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png)

После завершения настройки параметров щелкните **Опубликовать**.

В окне вывода можно будет наблюдать выполнение некоторых операций, а затем появится окно журнала действий Microsoft Azure.

![Окно журнала действий Microsoft Azure](./media/cloud-services-python-ptvs/publish-activity-log.png)

Для развертывания потребуется несколько минут, после чего веб-роли и рабочие роли будут запущены на платформе Azure.

## Дальнейшие действия

С дополнительной информацией о работе с веб-ролями и рабочими ролями в средствах Python для Visual Studio можно ознакомиться в документации PTVS:

- [Проекты для облачной службы][]

Для получения более подробной информации об использовании служб Azure из веб-ролей или рабочих ролей, например об использовании хранилища Azure или служебной шины, рекомендуется обратиться к следующим руководствам:

- [Служба больших двоичных объектов][]
- [Служба таблиц][]
- [Служба очередей][]
- [Очереди служебной шины][]
- [Разделы служебной шины][]


<!--Link references-->

[Что такое облачная служба?]: /manage/services/cloud-services/what-is-a-cloud-service/
[execution model-web sites]: fundamentals-application-models.md#WebSites
[execution model-vms]: fundamentals-application-models.md#VMachine
[execution model-cloud services]: fundamentals-application-models.md#CloudServices
[Python Developer Center]: /develop/python/

[Служба больших двоичных объектов]: storage-python-how-to-use-blob-storage.md
[Служба очередей]: storage-python-how-to-use-queue-storage.md
[Служба таблиц]: storage-python-how-to-use-table-storage.md
[Очереди служебной шины]: service-bus-python-how-to-use-queues.md
[Разделы служебной шины]: service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Средств Python для Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: http://aka.ms/ptvsdocs
[Проекты для облачной службы]: http://go.microsoft.com/fwlink/?LinkId=624028
[Средства Python 2.2 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Пакет SDK Azure для VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Пакет SDK Azure для VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 (32-разрядный)]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 (32-разрядный)]: http://go.microsoft.com/fwlink/?LinkId=517191

<!---HONumber=Oct15_HO3-->