<properties linkid="develop-python-cloud-services-with-ptvs" urlDisplayName="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" pageTitle="Веб-роли и рабочие роли Python с помощью Python Tools 2.1 для Visual Studio" metaKeywords="Azure python, web role, worker role, PTVS, cloud service" description="Обзор использования Python Tools в Visual Studio для создания облачных служб Azure, включая веб-роли и рабочие роли." metaCanonical="" services="" documentationCenter="Python" title="Веб-роли и рабочие роли Python с помощью Python Tools 2.1 для Visual Studio" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />

# Веб-роли и рабочие роли Python с помощью Python Tools 2.1 для Visual Studio

В данном руководстве предоставлен обзор способов использования веб-ролей и рабочих ролей Python с помощью [Python Tools для Visual Studio][Python Tools для Visual Studio].

-   [Предварительные требования][Предварительные требования]
-   [Что такое веб-роли и рабочие роли Python?][Что такое веб-роли и рабочие роли Python?]
-   [Создание проекта][Создание проекта]
-   [Локальный запуск][Локальный запуск]
-   [Публикация в Azure][Публикация в Azure]
-   [Дальнейшие действия][Дальнейшие действия]

## <a name="prerequisites"></a>Предварительные требования

-   Visual Studio 2012 или 2013
-   [Средства Python 2.1 для Visual Studio][Средства Python 2.1 для Visual Studio]
-   [Пакет инструментов Azure SDK для VS 2013][Пакет инструментов Azure SDK для VS 2013] или [Пакет инструментов Azure SDK для VS 2012][Пакет инструментов Azure SDK для VS 2012]
-   [Python 2.7, 32-разрядная версия][Python 2.7, 32-разрядная версия] или [Python 3.4, разрядная версия][Python 3.4, разрядная версия]

[WACOM.INCLUDE [создать-учетная запись-и-веб-сайт-примечание](../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>Что такое веб-роли и рабочие роли Python?

Azure предоставляет три вычислительные модели, необходимые для запуска приложений: [Веб-сайты Azure][Веб-сайты Azure], [Виртуальные машины Azure][Виртуальные машины Azure] и [Облачные службы Azure][Облачные службы Azure]. Все три модели поддерживают Python. Облачные службы, которые включают веб-роли и рабочие роли, предоставляют *платформу как службу (PaaS)*. В рамках облачной службы веб-роль предоставляет выделенный веб-сервер IIS для размещения веб-приложений переднего плана, тогда как рабочая роль может выполнять асинхронные, долгосрочные или постоянные задачи, независимые от пользовательских действий и пользовательского ввода.

Дополнительные сведения см. в разделе [Что такое облачная служба?][Что такое облачная служба?].

<div class="dev-callout"><strong>Требуется собрать простой веб-сайт?</strong>
<p>Если ваш сценарий включает только простой интерфейс веб-сайта, следует рассмотреть возможность использования упрощенного веб-сайта Azure. По мере роста веб-сайта и изменения требований можно легко выполнить обновление к облачной службе. В разделе <a href="/ru-ru/develop/python/">Центр разработчиков Python</a> можно найти статьи, посвященные разработке Веб-сайтов Azure.</p>
</div>

## <a name="project-creation"></a>Создание проекта

В Visual Studio можно выбрать пункт **Облачная служба Azure** в диалоговом окне **Новый проект** из меню **Python**.

![Диалоговое окно "Новый проект"][Диалоговое окно "Новый проект"]

В мастере настройки Облачной службы Azure можно выбрать соответствующий пункт для создания новой веб-роли или рабочей роли.

![Диалоговое окно Облачной службы Azure][Диалоговое окно Облачной службы Azure]

Шаблон рабочей роли поставляется вместе со стандартным кодом для реализации подключения к учетной записи хранения Azure или к шине обслуживания.

![Решение для Облачной службы][Решение для Облачной службы]

Добавить веб-роль или рабочую роль к уже существующей облачной службе можно в любое время. Также можно добавить к собственному решению существующий проект или создать новые проекты.

![Команда добавления роли][Команда добавления роли]

В облачной службе могут присутствовать роли, реализованные на различных языках программирования. Например, может существовать веб-роль на Python, реализованная с помощью Django, одновременно с рабочими ролями на Python и C\#. Можно без труда обмениваться данными между ролями с помощью очередей шины обслуживания или очередей хранилища.

## <a name="run-locally"></a>Запуск в локальном эмуляторе

Если настроить проект облачной службы в качестве запускаемого при включении и нажать клавишу F5, тогда эта облачная служба запустится в локальном эмуляторе Azure.

Хотя PTVS поддерживает возможность запуска в эмуляторе, процедура отладки (расстановка точек прерывания и т. д.) в таком режиме не работает.

Чтобы отладить веб-роли и рабочие роли, необходимо установить в настройках проекта роли параметр, определяющий его запуск при включении, и выполнить отладку таким образом. В качестве запускаемых при включении можно указать несколько проектов. Щелкните правовой кнопкой мыши на решении и выберите пункт **Настроить запускаемые при включении проекты**.

![Свойства запускаемого при включении проекта в решении][Свойства запускаемого при включении проекта в решении]

## <a name="publish-to-azure"></a>Публикация в Azure

Чтобы выполнить публикацию, щелкните правой кнопкой мыши на проекте облачной службы в решении и выберите пункт **Опубликовать**.

![Вход для публикации в Microsoft Azure][Вход для публикации в Microsoft Azure]

На странице настроек отметьте облачную службу, предназначенную для выполнения публикации.

![Настройки публикации в Microsoft Azure][Настройки публикации в Microsoft Azure]

Можно создать новую облачную службу при ее отсутствии.

![Диалоговое окно создания Облачной службы][Диалоговое окно создания Облачной службы]

Также рекомендуется разрешить устанавливать сетевые подключения через удаленные рабочие столы к машинам для выполнения отладки сбоев.

![Диалоговое окно настройки удаленного рабочего стола][Диалоговое окно настройки удаленного рабочего стола]

После завершения настройки параметров щелкните **Опубликовать**.

В окне вывода можно будет наблюдать выполнение некоторых операций, а затем появится окно журнала действий Microsoft Azure.

![Окно журнала действий Microsoft Azure][Окно журнала действий Microsoft Azure]

Для развертывания потребуется несколько минут, после чего веб-роли и рабочие роли будут запущены на платформе Azure.

## <a name="next-steps"></a>Дальнейшие действия

С дополнительной информацией о работе с веб-ролями и рабочими ролями в Python Tools для Visual Studio можно ознакомиться в документации PTVS:

-   [Проекты для Облачной службы][Проекты для Облачной службы]

Для получения более подробной информации об использовании служб Azure из веб-ролей или рабочих ролей, например об использовании хранилища Azure или шины обслуживания, рекомендуется обратиться к следующим руководствам:

-   [Служба BLOB-объектов][Служба BLOB-объектов]
-   [Служба таблиц][Служба таблиц]
-   [Служба очередей][Служба очередей]
-   [Очереди шины обслуживания][Очереди шины обслуживания]
-   [Разделы шины обслуживания][Разделы шины обслуживания]

<!--Link references-->\r\n<!--External Link references-->

  [Python Tools для Visual Studio]: http://pytools.codeplex.com
  [Предварительные требования]: #prerequisites
  [Что такое веб-роли и рабочие роли Python?]: #what-are-python-web-and-worker-roles
  [Создание проекта]: #project-creation
  [Локальный запуск]: #run-locally
  [Публикация в Azure]: #publish-to-azure
  [Дальнейшие действия]: #next-steps
  [Средства Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
  [Пакет инструментов Azure SDK для VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
  [Пакет инструментов Azure SDK для VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
  [Python 2.7, 32-разрядная версия]: http://go.microsoft.com/fwlink/?LinkId=517190
  [Python 3.4, разрядная версия]: http://go.microsoft.com/fwlink/?LinkId=517191
  [Веб-сайты Azure]: /ru-ru/documentation/articles/fundamentals-application-models/#WebSites
  [Виртуальные машины Azure]: /ru-ru/documentation/articles/fundamentals-application-models/#VMachine
  [Облачные службы Azure]: /ru-ru/documentation/articles/fundamentals-application-models/#CloudServices
  [Что такое облачная служба?]: /ru-ru/manage/services/cloud-services/what-is-a-cloud-service/
  [Диалоговое окно "Новый проект"]: ./media/cloud-services-python-ptvs/new-project-cloud-service.png
  [Диалоговое окно Облачной службы Azure]: ./media/cloud-services-python-ptvs/new-service-wizard.png
  [Решение для Облачной службы]: ./media/cloud-services-python-ptvs/worker.png
  [Команда добавления роли]: ./media/cloud-services-python-ptvs/add-new-or-existing-role.png
  [Свойства запускаемого при включении проекта в решении]: ./media/cloud-services-python-ptvs/startup.png
  [Вход для публикации в Microsoft Azure]: ./media/cloud-services-python-ptvs/publish-sign-in.png
  [Настройки публикации в Microsoft Azure]: ./media/cloud-services-python-ptvs/publish-settings.png
  [Диалоговое окно создания Облачной службы]: ./media/cloud-services-python-ptvs/publish-create-cloud-service.png
  [Диалоговое окно настройки удаленного рабочего стола]: ./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png
  [Окно журнала действий Microsoft Azure]: ./media/cloud-services-python-ptvs/publish-activity-log.png
  [Проекты для Облачной службы]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
  [Служба BLOB-объектов]: /ru-ru/documentation/articles/storage-python-how-to-use-blob-storage/
  [Служба таблиц]: /ru-ru/documentation/articles/storage-python-how-to-use-table-storage/
  [Служба очередей]: /ru-ru/documentation/articles/storage-python-how-to-use-queue-storage/
  [Очереди шины обслуживания]: /ru-ru/documentation/articles/service-bus-python-how-to-use-queues/
  [Разделы шины обслуживания]: /ru-ru/documentation/articles/service-bus-python-how-to-use-topics-subscriptions/
