---
title: "Установка Python и пакета SDK — Azure"
description: "Узнайте, как установить Python и пакет SDK для использования с Azure."
services: 
documentationcenter: python
author: lmazuel
manager: wpickett
editor: 
ms.assetid: f36294be-daeb-4caf-9129-fce18130f552
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/06/2016
ms.author: lmazuel
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: be8fed81757d3a7467707e4e0fdf1fd5734e0f07
ms.lasthandoff: 04/03/2017


---
# <a name="installing-python-and-the-sdk"></a>Установка Python и пакета SDK
Python легко устанавливается в Windows и поставляется предварительно установленным в Mac, Linux и [Bash для Windows](https://msdn.microsoft.com/commandline/wsl/about). В этом руководстве рассматривается установка и подготовка компьютера к использованию с Azure.

## <a name="whats-in-the-python-azure-sdk"></a>Новые возможности пакета Python Azure SDK
Azure SDK для Python включает компоненты для разработки, развертывания приложений Python в Azure и управления ими. Пакет Azure SDK для Python включает следующие средства:

* **Библиотеки управления**. Эти библиотеки классов предоставляют управляемые через интерфейс ресурсы Azure, такие как учетные записи хранения и виртуальные машины.
* **Библиотеки среды выполнения**. Эти библиотеки классов предоставляют интерфейс для доступа к компонентам Azure, таким как хранилище и служебная шина.

## <a name="which-python-and-which-version-to-use"></a>Какой вариант и какую версию Python использовать
Доступно несколько видов интерпретаторов Python, например:

* CPython — стандартный и наиболее часто используемый интерпретатор Python;
* PyPy — быстродействующий аналог интерпретатора CPython;
* IronPython — интерпретатор Python, работающий на базе .net/CLR;
* Jython — интерпретатор Python, работающий на базе виртуальной машины Java.

**CPython** (версия 2.7 или 3.3 и выше) и PyPy 5.4.0 были протестированы и совместимы с пакетом SDK Azure для Python.

## <a name="where-to-get-python"></a>Где можно получить Python?
Существует несколько способов получить CPython:

* непосредственно с сайта [www.python.org][www.python.org];
* у известного поставщика дистрибутивов, например [www.continuum.io][www.continuum.io], [www.enthought.com][www.enthought.com] или [www.activestate.com][www.activestate.com].
* Построение из исходного кода!

При отсутствии особой необходимости мы рекомендуем использовать первые два варианта.

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>Установка пакета SDK в Windows, Linux и MacOS (только клиентские библиотеки)
Если вы уже установили Python, вы можете использовать pip для установки пакета всех клиентских библиотек в существующей среде Python 2.7 или Python 3.3+. При этом будут скачаны пакеты из [индекса пакетов Python][Python Package Index] (PyPI).

Могут потребоваться права администратора:

* В Linux и MacOS используйте команду `sudo`: `sudo pip install azure-mgmt-compute`.
* В Windows откройте окно командной строки или окно PowerShell от имени администратора.

Вы можете установить каждую библиотеку отдельно для каждой службы Azure:

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Предварительные версии пакетов можно установить с помощью флага `--pre` :

```console
   $ pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

Набор библиотек Azure также можно установить одной строкой с помощью пакета метаданных `azure` . Так как не все пакеты в этом пакете метаданных публикуются как стабильные, то и сам пакет метаданных `azure` является предварительным.
Однако основные пакеты уже можно считать "стабильными" с точки зрения качества и полноты кода.

* Соответствующая отметка будет добавлена как можно скорее и синхронно с другими языками.
  До тех пор мы не планируем каких-либо значительных изменений.

Так как это предварительный выпуск, необходимо использовать флаг `--pre` :

```console
   $ pip install --pre azure
```

или напрямую:

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>Получение дополнительных пакетов
[Индекс пакета Python][Python Package Index] (PyPI) предлагает широкий выбор библиотек Python.  Если вы решили установить дистрибутив, то получите основную часть нужного кода для самых различных сценариев — от веб-разработки до технических вычислений.

## <a name="python-tools-for-visual-studio"></a>Средства Python для Visual Studio
[Инструменты Python для Visual Studio][Инструменты Python для Visual Studio] (PTVS) представляют собой бесплатный подключаемый модуль с открытым исходным кодом от корпорации Майкрософт, который превращает Visual Studio в полноценную интегрированную среду разработки Python.

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Использование средств PTVS необязательно, однако рекомендуется, поскольку они обеспечивают поддержку Python и веб-проектов/решений, отладку, профилирование, интерактивное окно, редактирование шаблонов и Intellisense.

PTVS также упрощает развертывание в Microsoft Azure с поддержкой для развертывания в [облачных службах](cloud-services/cloud-services-python-ptvs.md) и на [веб-сайтах](app-service-web/web-sites-python-ptvs-django-mysql.md).

PTVS работает с установленными экземплярами Visual Studio 2013 или 2015.  Документацию, скачиваемые материалы и обсуждения см. на странице [Инструменты Python для Visual Studio].  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>Сценарии Python Azure для Linux и MacOS
Для Linux и MacOS существуют следующие основные поддерживаемые сценарии Azure:

1. Использование служб Azure с помощью клиентских библиотек для Python
2. Запуск приложения на виртуальной машине Linux
3. Разработка и публикация на веб-сайтах Azure с помощью Git

Первый сценарий позволяет создавать полнофункциональные веб-приложения, использующие преимущества возможностей Azure PaaS, такие как [хранилище BLOB-объектов](virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [хранилище очередей](storage/storage-python-how-to-use-queue-storage.md), [Хранилище таблиц](storage/storage-python-how-to-use-table-storage.md) и т. д., через оболочки Pythonic для интерфейсов REST API Azure. Они совершенно одинаково работают в Windows, Mac и Linux.  Можно также использовать эти клиентские библиотеки из вашей локальной машины или виртуальной машины Linux в Azure.

При сценарии с виртуальной машиной вы просто запускаете выбранную виртуальную машину Linux (Ubuntu, CentOS, Suse), а затем выполняете нужные компоненты и управляете ими.  В качестве примера можно запустить REPL или Notebook [IPython][IPython] на компьютере Windows, Mac либо Linux и указать в браузере многопроцессорную виртуальную машину Linux или Windows с запущенной подсистемой IPython в Azure. Дополнительные сведения см. в руководстве по [IPython Notebook в Azure](virtual-machines/linux/jupyter-notebook.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Дополнительные сведения о способах настройки виртуальной машины Linux см. в руководстве по [созданию виртуальной машины Linux](virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

С помощью развертывания Git можно разработать веб-приложение Python и опубликовать его на веб-сайте Azure из любой операционной системы.  При принудительной отправке репозитория в Azure автоматически создается виртуальная среда, а pip устанавливает требуемые пакеты.

Дополнительные сведения о разработке и публикации Веб-сайтов Azure см. в руководствах по [созданию Веб-сайтов с помощью Django](app-service-web/web-sites-python-create-deploy-django-app.md), [Bottle](app-service-web/web-sites-python-create-deploy-bottle-app.md) и [Flask](app-service-web/web-sites-python-create-deploy-flask-app.md). Дополнительные сведения об использовании совместимой с WSGI платформы см. в статье [Настройка Python в веб-приложениях службы приложений Azure](app-service-web/web-sites-python-configure.md).

## <a name="additional-software-and-resources"></a>Дополнительные ресурсы и программное обеспечение:
* [Пакет SDK Azure для Python — ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Пакет SDK Azure для Python — GitHub](https://github.com/Azure/azure-sdk-for-python)
* [Официальные примеры кода Azure для Python](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Распространяемая среда аналитики Python][Continuum Analytics Python Distribution]
* [Распространяемый набор Enthought Python][Enthought Python Distribution]
* [Распространяемый набор ActiveState Python][ActiveState Python Distribution]
* [SciPy — набор научных библиотек Python][SciPy - A suite of Scientific Python libraries]
* [NumPy — библиотека числовых значений для Python][NumPy - A numerics library for Python]
* [Django Project — зрелая веб-платформа или CMS][Django Project - A mature web framework/CMS]
* [IPython — расширенное использование REPL и Notebook для Python][IPython - an advanced REPL/Notebook for Python]
* [IPython Notebook в Azure](virtual-machines/linux/jupyter-notebook.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Средства Python для Visual Studio на сайте GitHub][Python Tools for Visual Studio on GitHub]
* [Центр по разработке для Python](/develop/python/)

[Continuum Analytics Python Distribution]: http://continuum.io
[Enthought Python Distribution]: http://www.enthought.com
[ActiveState Python Distribution]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - A suite of Scientific Python libraries]: http://www.scipy.org
[NumPy - A numerics library for Python]: http://www.numpy.org
[Django Project - A mature web framework/CMS]: http://www.djangoproject.com
[IPython - an advanced REPL/Notebook for Python]: http://ipython.org
[IPython]: http://ipython.org
[IPython Notebook on Azure]: virtual-machines-linux-jupyter-notebook.md
[Cloud Services]: cloud-services-python-ptvs.md
[Websites]: web-sites-python-ptvs-django-mysql.md
[Инструменты Python для Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio on GitHub]: https://github.com/microsoft/ptvs
[Python Package Index]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-quick-create-cli.md
[Creating Websites with Django]: web-sites-python-create-deploy-django-app.md
[Creating Websites with Bottle]: web-sites-python-create-deploy-bottle-app.md
[Creating Websites with Flask]: web-sites-python-create-deploy-flask-app.md
[Configuring Python with Azure Websites]: web-sites-python-configure.md
[table storage]: storage-python-how-to-use-table-storage.md
[queue storage]: storage-python-how-to-use-queue-storage.md
[blob storage]: storage-python-how-to-use-blob-storage.md

