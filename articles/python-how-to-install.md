<properties 
	pageTitle="Установка Python и SDK - Azure" 
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

Python довольно просто устанавливается в Windows и поставляется предварительно установленным в Mac и Linux.  В этом руководстве рассматривается установка и подготовка компьютера к использованию с Azure.  Это руководство поможет вам изучить следующие темы:

* Новые возможности пакета Python Azure SDK
* Какой вариант и какую версию Python использовать
* Установка в Windows
* Установка в Mac и Linux

## Новые возможности пакета Python Azure SDK

Azure SDK для Python включает компоненты для разработки, развертывания и управления приложениями Python для Azure. Пакет Azure SDK для Python включает следующие средства:

* **Клиентские библиотеки Python для Azure**. Эти библиотеки классов предоставляют интерфейс для доступа к функциям Azure, например хранилища и service bus и управления ресурсами Azure, например учетных записей хранилища, виртуальные машины, и т. д.
* **Эмуляторы Azure (только Windows)**. Эмуляторы вычислений и хранения - это локальные эмуляторы облачных служб и служб управления данными, которые позволяют тестировать приложение локально. Эмуляторы Azure работают только в Windows.

## Какой вариант и какую версию Python использовать

Доступно несколько видов интерпретаторов Python, например:

* CPython - стандартный и наиболее часто используемый интерпретатор Python
* IronPython - интерпретатор Python, работающей на .net/CLR
* Jython - интерпретатор Python, работающей на базе JVM

Только **CPython** протестирован и поддерживается для служб Python Azure SDK и Azure, как веб-сайтов и облачных служб.  Рекомендуется использовать версию 2.7 или 3.4.

## Где можно получить Python?

Существует несколько способов получить CPython:

* Непосредственно из [www.python.org][]
* Из известных дистрибутивов, таких как [www.continuum.io][], [www.enthought.com][] или [www.activestate.com][]
* Построение из исходного кода!

При отсутствии особой необходимости мы рекомендуем использовать первые два варианта в соответствии с приведенным ниже описанием.

## Установка в Windows

Для Windows можно использовать установщик веб-платформы, чтобы упростить установку. К ним относятся CPython из [www.python.org][].

* [Microsoft Azure SDK для Python 2.7][]
* [Microsoft Azure SDK для Python 3.4][]

**Примечание:** на Windows Server для загрузки установщика WebPI может потребоваться настроить параметры IE ESC (Пуск/Администрирование средства/Manager или локальный сервер, нажмите кнопку **Конфигурация усиленной безопасности**, задайте значение Off)


### Python 2.7

Установщик WebPI предоставляет все, что нужно для разработки приложений Python Azure.

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

По завершении вы увидите этот экран подтверждения параметров установки:

![how-to-install-python-webpi-27-2](./media/python-how-to-install/how-to-install-python-webpi-27-2.png)

После завершения установки введите `python` в командной строке, чтобы убедиться, что все прошло успешно.  В зависимости от способа установки вам может потребоваться задать переменную "path", чтобы найти (правильную версию) Python:

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

После установки вам должны быть доступны компоненты Python и клиентские библиотеки, находящиеся в папке по умолчанию.

		C:\Python27\Lib\site-packages\azure


### Python 3.4

Установщик WebPI предоставляет все, что нужно для разработки приложений Python Azure.

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)
 
По завершении вы увидите этот экран подтверждения параметров установки:

![how-to-install-python-webpi-34-2](./media/python-how-to-install/how-to-install-python-webpi-34-2.png)

После завершения установки введите в командной строке, чтобы убедиться, что все прошло успешно.  В зависимости от способа установки вам может потребоваться задать переменную "path", чтобы найти (правильную версию) Python:

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

После установки вам должны быть доступны компоненты Python и клиентские библиотеки, находящиеся в папке по умолчанию.

		C:\Python34\Lib\site-packages\azure


### Получение дополнительных пакетов

[Индекс пакета Python][] (PyPI) имеется широкий выбор библиотек Python.  Если решили установить дистрибутив, то получите основную часть нужного кода для самых различных сценариев - от веб-разработки до технических вычислений.


### Средства Python для Visual Studio

[Средства Python для Visual Studio][] (PTVS) является подключаемый модуль освободить ОС корпорации Майкрософт, которая превращает VS в полноценные интегрированной среды разработки Python:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

С помощью PTVS является обязательным, но рекомендуется, поскольку обеспечивает поддержку Python и решения веб-проекта и, отладки, профилирования, интерактивное окно, редактирование шаблонов и Intellisense.

PTVS также упрощает развертывание Microsoft Azure с поддержкой для развертывания [облачных служб][] и [веб-узлы][].

PTVS работает с существующей установки Visual Studio 2010, 2012 или 2013.  Документация, загрузки и обсуждения в разделе [средства Python для Visual Studio на CodePlex][].  

## Удаление из Windows

**Azure SDK для Python** продукты WebPI не приложений в обычном смысле а коллекцией различных продуктов, таких как 32-разрядных Python 2.7/3.4, клиентские библиотеки Azure для Python, т. д., которые могут быть объединены.  Поэтому он не имеет собственной программы удаления, и вам потребуется по отдельности удалять установленные им программы из панели управления Windows.  

Если вы захотите переустановить **пакет Azure SDK для Python**, просто откройте окно командной строки PowerShell с правами администратора и выполните следующую команду:

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

а затем перезапустите WebPI.

## Установка в Linux и MacOS

Python скорее всего уже установлен на компьютере разработчика.  Это можно проверить, введя следующую команду:

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

Здесь мы видим, что в этой виртуальной машине с Ubuntu Server 14.04 LTS, работающей на Azure, установлен CPython 2.7.6. Если необходимо выполнить обновление, следуйте указаниям своей ОС по обновлению пакета.

Чтобы установить клиентские библиотеки Python Azure, используйте **pip** для извлечения его из **PyPI**.

Если у вас нет **pip** установлен, эта команда используется для установки:

	curl https://bootstrap.pypa.io/get-pip.py | sudo python
	
Приведенная выше команда автоматически запрашивает пароль пользователя root. Введите его и нажмите клавишу ВВОД.  

Затем с помощью `pip` команду, чтобы установить клиентские библиотеки Python Azure:
	
	sudo pip install azure

Теперь вы увидите установленные клиентские библиотеки в области **site-packages**.  В MacOS:

![how-to-install-python-mac-site](./media/python-how-to-install/how-to-install-python-mac-site.png)

## Python Azure сценарии для Linux и MacOS

Для Linux и MacOS существуют следующие основные Azure сценариев, поддерживаемых:

1. Использование служб Azure с помощью клиентских библиотек для Python

2. Запуск приложения на виртуальной машине Linux

3. Разработка и публикация на веб-сайтах Azure с помощью Git

Первый сценарий позволяет создавать полнофункциональные веб-приложения, использующие преимущества возможностей Azure PaaS например [хранилища BLOB-объектов][], [хранилище очередей][], [хранилище таблиц][] и т.д., через оболочки Pythonic для Azure REST API.  Они совершенно одинаково работают в Windows, Mac и Linux.  Можно также использовать эти клиентские библиотеки из вашей локальной машины или виртуальной Машины Linux в Azure.

При сценарии с виртуальной машиной вы просто запускаете выбранную виртуальную машину Linux (Ubuntu, CentOS, Suse), а затем выполняете нужные компоненты и управляете ими.  В качестве примера можно запустить [IPython][] REPL/notebook на компьютере Windows/Mac/Linux и указать в браузере мультипроцессную виртуальную машину Linux или Windows с запущенной подсистемой IPython в Azure. В разделе [IPython Notebook в Azure][] учебника для получения дополнительных сведений.

Дополнительные сведения о способах настройки виртуальной Машины Linux см. в разделе [Создание виртуальной машины с ОС Linux][] учебника.

Развертывание Git можно разработать веб-приложение Python и опубликовать его на веб-сайт Azure из любой операционной системе.  При принудительной репозитория в Azure, автоматически создаст виртуальную среду и pip установить необходимые пакеты.

Дополнительные сведения о разработке и публикации веб-сайтов Azure см. в учебниках для [Создание веб-сайты с Django][], [Создание веб-сайтов с бутылка][] и [Создание веб-сайтов с термосе][]. Дополнительные общие сведения об использовании все совместимые платформы WSGI разделе [Настройка Python с веб-сайтами Azure][].


## Дополнительные ресурсы и программное обеспечение:

* [Среда аналитики Python Distribution][]
* [Распространяемый набор Enthought Python Distribution][]
* [Набор ActiveState Python Distribution][]
* [SciPy - набор научных библиотек Python][]
* [NumPy - библиотека числовых значений для Python][]
* [Django Project - зрелая веб-платформа/CMS][]
* [IPython - расширенный интерфейс REPL/Notebook для Python][]
* [IPython Notebook в Azure][]
* [Средства Python для Visual Studio на CodePlex][]


[Среда аналитики Python Distribution]: http://continuum.io
[Распространяемый набор Enthought Python Distribution]: http://www.enthought.com
[Набор ActiveState Python Distribution]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - набор научных библиотек Python]: http://www.scipy.org
[NumPy - библиотека числовых значений для Python]: http://www.numpy.org
[Django Project - зрелая веб-платформа/CMS]: http://www.djangoproject.com 
[IPython - расширенный интерфейс REPL/Notebook для Python]: http://ipython.org
[IPython]: http://ipython.org
[IPython Notebook в Azure]: ../virtual-machines-python-ipython-notebook/
[Облачные службы]: ../cloud-services-python-ptvs/
[Веб-сайты]: ../web-sites-python-ptvs-django-mysql
[Средства Python для Visual Studio]: http://aka.ms/ptvs
[Средства Python для Visual Studio на CodePlex]: http://pytools.codeplex.com 
[Индекс пакета Python]: http://pypi.python.org/pypi
[Microsoft Azure SDK для Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Microsoft Azure SDK для Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409
[Настройка виртуальной Машины Linux через портал Azure]: ../../../shared/chunks/create-and-configure-opensuse-vm-in-portal
[Использование средств командной строки Azure для Mac и Linux]: ../../shared/chunks/crossplat-cmd-tools
[Создание виртуальной машины под управлением Linux]: ../virtual-machines-linux-tutorial/
[Создание веб-сайтов с помощью Django]: ../web-sites-python-create-deploy-django-app/
[Создание веб-сайтов с помощью Bottle]: ../web-sites-python-create-deploy-bottle-app/
[Создание веб-сайтов с помощью Flask]: ../web-sites-python-create-deploy-flask-app/
[Настройка Python для веб-сайтов Azure]: ../web-sites-python-configure/
[Хранилище таблиц]: ../storage-python-how-to-use-table-storage/
[хранилище очередей]: ../storage-python-how-to-use-queue-storage/
[Хранилище BLOB-объектов]: ../storage-python-how-to-use-blob-storage/

<!--HONumber=47-->
