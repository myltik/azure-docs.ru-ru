<properties
	pageTitle="Установка Python и пакета SDK — Azure"
	description="Узнайте, как установить Python и пакет SDK для использования с Azure."
	services=""
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="02/05/2015"
	ms.author="huvalo"/>




# Установка Python и пакета SDK

Python довольно легко устанавливается в Windows и поставляется предварительно установленным в Mac и Linux. В этом руководстве рассматривается установка и подготовка компьютера к использованию с Azure. Это руководство поможет вам изучить следующие темы:

* Новые возможности пакета Python Azure SDK
* Какой вариант и какую версию Python использовать
* Установка в Windows
* Установка в Mac и Linux

## Новые возможности пакета Python Azure SDK

Azure SDK для Python включает компоненты для разработки, развертывания приложений Python в Azure и управления ими. Пакет Azure SDK для Python включает следующие средства:

* **Клиентские библиотеки Python для Azure**. Эти библиотеки классов предоставляют интерфейс для доступа к компонентам Azure, например хранилищу и служебной шине, а также обеспечивают управление ресурсами Azure, например учетными записями хранения, виртуальными машинами, и т. д.
* **Эмуляторы Azure (только для Windows)**. Эмуляторы вычислений и хранения — это локальные эмуляторы облачных служб и служб управления данными, которые позволяют тестировать приложение локально. Эмуляторы Azure работают только в Windows.

## Какой вариант и какую версию Python использовать

Доступно несколько видов интерпретаторов Python, например:

* CPython — стандартный и наиболее часто используемый интерпретатор Python;
* IronPython — интерпретатор Python, работающий на базе .net/CLR;
* Jython — интерпретатор Python, работающий на базе JVM

Только **CPython** протестирован и поддерживается для Python Azure SDK и служб Azure, таких как веб-сайты и облачные службы. Рекомендуется использовать версию 2.7 или 3.4.

## Где можно получить Python?

Существует несколько способов получить CPython:

* Непосредственно с сайта [www.python.org][]
* Из известных дистрибутивов, таких [www.continuum.io][], [www.enthought.com][] или [www.activestate.com][]
* Построение из исходного кода!

При отсутствии особой необходимости мы рекомендуем использовать первые два варианта в соответствии с приведенным ниже описанием.

## Установка в Windows

Для Windows можно использовать установщик веб-платформы, чтобы упростить установку. К ним относится CPython из [www.python.org][].

* [Microsoft Azure SDK для Python 2.7][]
* [Microsoft Azure SDK для Python 3.4][]

**Примечание.** На сервере Windows Server для скачивания установщика WebPI может потребоваться настроить параметры конфигурации усиленной безопасности Internet Explorer (выберите «Пуск/Администрирование/Диспетчер серверов/Локальный сервер», затем щелкните **Конфигурация усиленной безопасности Internet Explorer** и установите значение Off (Выкл.))


### Python 2.7

Установщик WebPI предоставляет все, что нужно для разработки приложений Python Azure.

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

По завершении вы увидите этот экран подтверждения параметров установки:

![how-to-install-python-webpi-27-2](./media/python-how-to-install/how-to-install-python-webpi-27-2.png)

После завершения установки введите `python` в командной строке и убедитесь, что все прошло успешно. В зависимости от способа установки вам может потребоваться задать переменную path, чтобы найти (правильную версию) Python:

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

После установки вам должны быть доступны компоненты Python и клиентские библиотеки, находящиеся в папке по умолчанию.

		C:\Python27\Lib\site-packages\azure


### Python 3.4

Установщик WebPI предоставляет все, что нужно для разработки приложений Python Azure.

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)

По завершении вы увидите этот экран подтверждения параметров установки:

![how-to-install-python-webpi-34-2](./media/python-how-to-install/how-to-install-python-webpi-34-2.png)

После завершения установки введите python в командной строке и убедитесь, что все прошло успешно. В зависимости от способа установки вам может потребоваться задать переменную path, чтобы найти (правильную версию) Python:

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

После установки вам должны быть доступны компоненты Python и клиентские библиотеки, находящиеся в папке по умолчанию.

		C:\Python34\Lib\site-packages\azure


### Получение дополнительных пакетов

[Индекс пакета Python][] (PyPI) предлагает широкий выбор библиотек Python. Если вы решили установить дистрибутив, то получите основную часть нужного кода для самых различных сценариев — от веб-разработки до технических вычислений.


### Средства Python для Visual Studio

[Средства Python для Visual Studio][] (PTVS) представляют собой бесплатно подключаемый модуль с открытым исходным кодом от корпорации Майкрософт, который превращает Visual Studio в полноценную интегрированную среду разработки Python:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Использование PTVS необязательно, однако рекомендуется, поскольку он обеспечивает поддержку Python и веб-проектов/решений, отладку, профилирование, интерактивное окно, редактирование шаблонов и Intellisense.

PTVS также упрощает развертывание в Microsoft Azure с поддержкой для развертывания в [облачных службах][] и [веб-сайтах][].

PTVS работает с установленными экземплярами Visual Studio 2010, 2012 или 2013. Документацию, скачиваемые материалы и обсуждения см. в разделе [Средства Python для Visual Studio на CodePlex][].

## Удаление из Windows

Продукты WebPI из **пакета Azure SDK для Python** не являются приложениями в обычном смысле слова, но коллекцией различных продуктов, таких как 32-разрядный Python 2.7/3.4, клиентских API Azure для Python и т. п., объединенных в пакет. Поэтому он не имеет собственной программы удаления, и вам потребуется по отдельности удалять установленные им программы из панели управления Windows.

Если вы захотите переустановить **пакет Azure SDK для Python**, просто откройте окно командной строки PowerShell с правами администратора и выполните следующую команду:

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

а затем перезапустите WebPI.

## Установка в Linux и MacOS

Скорее всего, Python уже установлен на вашем компьютере разработчика. Это можно проверить, введя следующую команду:

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

Здесь мы видим, что в этой виртуальной машине с Ubuntu Server 14.04 LTS, работающей на Azure, установлен CPython 2.7.6. Если необходимо выполнить обновление, следуйте указаниям своей ОС по обновлению пакета.

Чтобы установить клиентские библиотеки Python Azure, используйте **pip** для его извлечения из **PyPI**.

Если у вас не установлен **pip**, используйте следующую команду для установки:

	curl https://bootstrap.pypa.io/get-pip.py | sudo python

Приведенная выше команда автоматически запрашивает пароль пользователя root. Введите его и нажмите клавишу ВВОД.

Далее используйте команду `pip` для установки клиентских библиотек Python Azure:

	sudo pip install azure

Теперь вы увидите установленные клиентские библиотеки в области **site-packages**. В MacOS:

![how-to-install-python-mac-site](./media/python-how-to-install/how-to-install-python-mac-site.png)

## Сценарии Python Azure для Linux и MacOS

Для Linux и MacOS существуют следующие основные поддерживаемые сценарии Azure:

1. Использование служб Azure с помощью клиентских библиотек для Python

2. Запуск приложения на виртуальной машине Linux

3. Разработка и публикация на веб-сайтах Azure с помощью Git

Первый сценарий позволяет создавать полнофункциональные веб-приложения, использующие преимущества возможностей Azure PaaS, такие как [хранилища BLOB-объектов][], [хранилища очередей][], [табличные хранилища][] и т. д. через оболочки Pythonic для API REST Azure. Они совершенно одинаково работают в Windows, Mac и Linux. Можно также использовать эти клиентские библиотеки из вашей локальной машины или виртуальной машины Linux в Azure.

При сценарии с виртуальной машиной вы просто запускаете выбранную виртуальную машину Linux (Ubuntu, CentOS, Suse), а затем выполняете нужные компоненты и управляете ими. В качестве примера можно запустить [IPython][] REPL/notebook на компьютере Windows/Mac/Linux и указать в браузере мультипроцессную виртуальную машину Linux или Windows с запущенной подсистемой IPython в Azure. Дополнительную информацию см. в учебнике [IPython Notebook в Azure][].

Дополнительные сведения о способах настройки виртуальной машины Linux см. в учебнике [Создание виртуальной машины с ОС Linux][].

С помощью развертывания Git можно разработать веб-приложение Python и опубликовать его на веб-сайте Azure из любой операционной системы. При принудительной отправке репозитория в Azure автоматически создается виртуальная среда, а pip устанавливает требуемые пакеты.

Дополнительные сведения о разработке и публикации веб-сайтов Azure см. в учебниках [Создание веб-сайтов с помощью Django][], [Создание веб-сайтов с помощью Bottle][] и [Создание веб-сайтов с помощью Flask][]. Дополнительные общие сведения об использовании совместимой с WSGI платформы см. в разделе [Настройка Python с веб-сайтами Azure][].


## Дополнительные ресурсы и программное обеспечение:

* [Распространяемая среда аналитики Python][]
* [Распространяемый набор Enthought Python][]
* [Распространяемый набор ActiveState Python][]
* [SciPy — набор научных библиотек Python][]
* [NumPy — библиотека числовых значений для Python][]
* [Django Project — зрелая веб-платформа/CMS][]
* [IPython — расширенный интерфейс REPL/Notebook для Python][]
* [IPython Notebook в Azure][]
* [Средства Python для Visual Studio на CodePlex][]


[Распространяемая среда аналитики Python]: http://continuum.io
[Распространяемый набор Enthought Python]: http://www.enthought.com
[Распространяемый набор ActiveState Python]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy — набор научных библиотек Python]: http://www.scipy.org
[NumPy — библиотека числовых значений для Python]: http://www.numpy.org
[Django Project — зрелая веб-платформа/CMS]: http://www.djangoproject.com
[IPython — расширенный интерфейс REPL/Notebook для Python]: http://ipython.org
[IPython]: http://ipython.org
[IPython Notebook в Azure]: virtual-machines-python-ipython-notebook.md
[облачных службах]: cloud-services-python-ptvs.md
[веб-сайтах]: web-sites-python-ptvs-django-mysql.md
[Средства Python для Visual Studio]: http://aka.ms/ptvs
[Средства Python для Visual Studio на CodePlex]: http://pytools.codeplex.com
[Индекс пакета Python]: http://pypi.python.org/pypi
[Microsoft Azure SDK для Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Microsoft Azure SDK для Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Создание виртуальной машины с ОС Linux]: virtual-machines-linux-tutorial.md
[Создание веб-сайтов с помощью Django]: web-sites-python-create-deploy-django-app.md
[Создание веб-сайтов с помощью Bottle]: web-sites-python-create-deploy-bottle-app.md
[Создание веб-сайтов с помощью Flask]: web-sites-python-create-deploy-flask-app.md
[Настройка Python с веб-сайтами Azure]: web-sites-python-configure.md
[табличные хранилища]: storage-python-how-to-use-table-storage.md
[хранилища очередей]: storage-python-how-to-use-queue-storage.md
[хранилища BLOB-объектов]: storage-python-how-to-use-blob-storage.md

<!---HONumber=July15_HO1-->