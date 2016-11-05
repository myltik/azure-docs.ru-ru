---
title: Установка Python и пакета SDK — Azure
description: Узнайте, как установить Python и пакет SDK для использования с Azure.
services: ''
documentationcenter: python
author: lmazuel
manager: wpickett
editor: ''

ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/06/2016
ms.author: lmazuel

---
# Установка Python и пакета SDK
Python легко устанавливается в Windows и поставляется предварительно установленным в Mac, Linux и [Bash для Windows](https://msdn.microsoft.com/commandline/wsl/about). В этом руководстве рассматривается установка и подготовка компьютера к использованию с Azure.

## Новые возможности пакета Python Azure SDK
Azure SDK для Python включает компоненты для разработки, развертывания приложений Python в Azure и управления ими. Пакет Azure SDK для Python включает следующие средства:

* **Библиотеки управления**. Эти библиотеки классов предоставляют управляемые через интерфейс ресурсы Azure, такие как учетные записи хранения и виртуальные машины.
* **Библиотеки среды выполнения**. Эти библиотеки классов предоставляют интерфейс для доступа к компонентам Azure, таким как хранилище и служебная шина.

## Какой вариант и какую версию Python использовать
Доступно несколько видов интерпретаторов Python, например:

* CPython — стандартный и наиболее часто используемый интерпретатор Python;
* PyPy — быстродействующий аналог интерпретатора CPython;
* IronPython — интерпретатор Python, работающий на базе .net/CLR;
* Jython — интерпретатор Python, работающий на базе виртуальной машины Java.

**CPython** (версия 2.7 или 3.3 и выше) и PyPy 5.4.0 были протестированы и совместимы с пакетом SDK Azure для Python.

## Где можно получить Python?
Существует несколько способов получить CPython:

* Непосредственно с сайта [www.python.org][www.python.org]
* Из известных дистрибутивов, таких [www.continuum.io][www.continuum.io], [www.enthought.com][www.enthought.com] или [www.activestate.com][www.activestate.com]
* Построение из исходного кода!

При отсутствии особой необходимости мы рекомендуем использовать первые два варианта.

## Установка пакета SDK в Windows, Linux и MacOS (только клиентские библиотеки)
Если вы уже установили Python, вы можете использовать pip для установки пакета всех клиентских библиотек в существующей среде Python 2.7 или Python 3.3+. При этом будут скачаны пакеты из [индекса пакетов Python][индекса пакетов Python] \(PyPI).

Могут потребоваться права администратора:

* В Linux и MacOS используйте команду `sudo`: `sudo pip install azure-mgmt-compute`.
* В Windows откройте окно командной строки или окно PowerShell от имени администратора.

Вы можете установить каждую библиотеку отдельно для каждой службы Azure:

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Предварительные версии пакетов можно установить с помощью флага `--pre`:

```console
   $ pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

Набор библиотек Azure также можно установить одной строкой с помощью пакета метаданных `azure`. Так как не все пакеты в этом пакете метаданных публикуются как стабильные, то и сам пакет метаданных `azure` является предварительным. Однако основные пакеты уже можно считать "стабильными" с точки зрения качества и полноты кода.

* Соответствующая отметка будет добавлена как можно скорее и синхронно с другими языками. До тех пор мы не планируем каких-либо значительных изменений.

Так как это предварительный выпуск, необходимо использовать флаг `--pre`:

```console
   $ pip install --pre azure
```

или напрямую:

```console
   $ pip install azure==2.0.0rc6
```

## Получение дополнительных пакетов
[Индекс пакета Python][Индекс пакета Python] \(PyPI) предлагает широкий выбор библиотек Python. Если вы решили установить дистрибутив, то получите основную часть нужного кода для самых различных сценариев — от веб-разработки до технических вычислений.

## Средства Python для Visual Studio
[Средства Python для Visual Studio][Средства Python для Visual Studio] \(PTVS) представляют собой бесплатно подключаемый модуль с открытым исходным кодом от корпорации Майкрософт, который превращает Visual Studio в полноценную интегрированную среду разработки Python:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Использование средств PTVS необязательно, однако рекомендуется, поскольку они обеспечивают поддержку Python и веб-проектов/решений, отладку, профилирование, интерактивное окно, редактирование шаблонов и Intellisense.

PTVS также упрощает развертывание в Microsoft Azure с поддержкой для развертывания в [облачных службах][облачных службах] и [веб-сайтах][веб-сайтах].

PTVS работает с установленными экземплярами Visual Studio 2013 или 2015. Документацию, скачиваемые материалы и обсуждения см. в разделе [Средства Python для Visual Studio].

## Сценарии Python Azure для Linux и MacOS
Для Linux и MacOS существуют следующие основные поддерживаемые сценарии Azure:

1. Использование служб Azure с помощью клиентских библиотек для Python
2. Запуск приложения на виртуальной машине Linux
3. Разработка и публикация на веб-сайтах Azure с помощью Git

Первый сценарий позволяет создавать полнофункциональные веб-приложения, использующие преимущества возможностей Azure PaaS, такие как [хранилище BLOB-объектов][хранилище BLOB-объектов], [хранилище очередей][хранилище очередей], [хранилище таблиц][хранилище таблиц] и т. д., через оболочки Pythonic для интерфейсов REST API Azure. Они совершенно одинаково работают в Windows, Mac и Linux. Можно также использовать эти клиентские библиотеки из вашей локальной машины или виртуальной машины Linux в Azure.

При сценарии с виртуальной машиной вы просто запускаете выбранную виртуальную машину Linux (Ubuntu, CentOS, Suse), а затем выполняете нужные компоненты и управляете ими. В качестве примера можно запустить [IPython][IPython] REPL/notebook на компьютере Windows/Mac/Linux и указать в браузере мультипроцессную виртуальную машину Linux или Windows с запущенной подсистемой IPython в Azure. Дополнительную информацию см. в учебнике [IPython Notebook в Azure][IPython Notebook в Azure].

Дополнительные сведения о способах настройки виртуальной машины Linux см. в учебнике [Создание виртуальной машины с ОС Linux][Создание виртуальной машины с ОС Linux].

С помощью развертывания Git можно разработать веб-приложение Python и опубликовать его на веб-сайте Azure из любой операционной системы. При принудительной отправке репозитория в Azure автоматически создается виртуальная среда, а pip устанавливает требуемые пакеты.

Дополнительные сведения о разработке и публикации веб-сайтов Azure см. в учебниках [Создание веб-приложений с помощью Django в Azure][Создание веб-приложений с помощью Django в Azure], [Создание веб-приложений с помощью Bottle в Azure][Создание веб-приложений с помощью Bottle в Azure] и [Создание веб-приложений с помощью Flask в Azure][Создание веб-приложений с помощью Flask в Azure]. Дополнительные общие сведения об использовании совместимой с WSGI платформы см. в статье [Настройка Python в веб-приложениях службы приложений Azure][Настройка Python в веб-приложениях службы приложений Azure].

## Дополнительные ресурсы и программное обеспечение:
* [Пакет SDK Azure для Python — ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Пакет SDK Azure для Python — GitHub](https://github.com/Azure/azure-sdk-for-python)
* [Официальные примеры кода Azure для Python](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Распространяемая среда аналитики Python][Распространяемая среда аналитики Python]
* [Распространяемый набор Enthought Python][Распространяемый набор Enthought Python]
* [Распространяемый набор ActiveState Python][Распространяемый набор ActiveState Python]
* [SciPy — набор научных библиотек Python][SciPy — набор научных библиотек Python]
* [NumPy — библиотека числовых значений для Python][NumPy — библиотека числовых значений для Python]
* [Django Project — зрелая веб-платформа/CMS][Django Project — зрелая веб-платформа/CMS]
* [IPython — расширенный интерфейс REPL/Notebook для Python][IPython — расширенный интерфейс REPL/Notebook для Python]
* [IPython Notebook в Azure][IPython Notebook в Azure]
* [Средства Python для Visual Studio на GitHub][Средства Python для Visual Studio на GitHub]
* [Центр по разработке для Python](/develop/python/)

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
[IPython Notebook в Azure]: virtual-machines-linux-jupyter-notebook.md
[облачных службах]: cloud-services-python-ptvs.md
[веб-сайтах]: web-sites-python-ptvs-django-mysql.md
[Средства Python для Visual Studio]: http://aka.ms/ptvs
[Средства Python для Visual Studio на GitHub]: https://github.com/microsoft/ptvs
[Индекс пакета Python]: http://pypi.python.org/pypi
[индекса пакетов Python]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Создание виртуальной машины с ОС Linux]: virtual-machines-linux-quick-create-cli.md
[Создание веб-приложений с помощью Django в Azure]: web-sites-python-create-deploy-django-app.md
[Создание веб-приложений с помощью Bottle в Azure]: web-sites-python-create-deploy-bottle-app.md
[Создание веб-приложений с помощью Flask в Azure]: web-sites-python-create-deploy-flask-app.md
[Настройка Python в веб-приложениях службы приложений Azure]: web-sites-python-configure.md
[хранилище таблиц]: storage-python-how-to-use-table-storage.md
[хранилище очередей]: storage-python-how-to-use-queue-storage.md
[хранилище BLOB-объектов]: storage-python-how-to-use-blob-storage.md

<!---HONumber=AcomDC_0914_2016-->