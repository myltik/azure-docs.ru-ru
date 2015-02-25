<properties urlDisplayName="Install Python" pageTitle="Установка Python и пакета SDK - Azure" metaKeywords="Azure Python SDK" description="Узнайте, как установить Python и пакет SDK для использования с Azure." metaCanonical="" services="" documentationCenter="Python" title="Installing Python and the SDK" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="11/10/2014" ms.author="huvalo" />




# Установка Python и пакета SDK
Python довольно просто устанавливается в Windows и поставляется предварительно установленным в Mac и Linux.  В этом руководстве рассматривается установка и подготовка компьютера к использованию с Azure.  Это руководство поможет изучить следующие темы:

* Новые возможности пакета Python Azure SDK
* Какой вариант и какую версию Python использовать
* Установка в Windows
* Установка в Mac и Linux

## Новые возможности пакета Python Azure SDK

Azure SDK для Python содержит компоненты для разработки, развертывания приложений Python в Azure и управления ими. В частности, пакет Azure SDK для Python располагает следующими средствами:

* **Клиентские библиотеки Python для Azure**. Эти библиотеки классов предоставляют интерфейс для доступа к функциям Azure, таким как службы управления данными и облачные службы.  
* **Эмуляторы Azure (только для Windows)**. Эмуляторы вычислений и хранения - это локальные эмуляторы облачных служб и служб управления данными, которые позволяют тестировать приложение локально. Эмуляторы Azure работают только в Windows.


Для основных сценариев для этого выпуска:

* **Windows**: облачная служба, веб-сайт, например сайт Django, использующий веб-роли.
* **Mac/Linux**: IaaS - запускайте в виртуальной машине все, что захотите, используйте службы Azure с помощью Python.

## Какой вариант и какую версию Python использовать

Доступно несколько видов интерпретаторов Python, например:

* CPython - стандартный и наиболее часто используемый интерпретатор Python;
* IronPython - интерпретатор Python, работающий на базе .net/CLR;
* Jython - интерпретатор Python, работающий на базе JVM.

Для данного выпуска протестирован и поддерживается только **CPython**.  Рекомендуется использовать версию 2.7 или 3.4.

## Где можно получить Python?

Существует несколько способов получить CPython:

* Непосредственно с сайта www.python.org.
* Из надежных дистрибутивов, таких как www.enthought.com, www.ActiveState.com, www.continuum.io.
* Построение из исходного кода!

При отсутствии особой необходимости мы рекомендуем использовать первые два варианта в соответствии с приведенным ниже описанием.

## Установка в Windows

Для Windows можно использовать установщик веб-платформы, чтобы упростить установку. К ним относятся CPython из www.python.org.

* [Microsoft Azure SDK для Python 2.7][]
* [Microsoft Azure SDK для Python 3,4][]

**Примечание.** На сервере Windows Server для загрузки установщика WebPI может потребоваться настроить параметры конфигурации усиленной безопасности Internet Explorer (выберите "Пуск/Администрирование/Диспетчер серверов", щелкните элемент **Настроить конфигурацию усиленной безопасности Internet Explorer** и установите значение "Выкл").


### Python 2,7

Установщик WebPI предоставляет все, что нужно для разработки приложений Python Azure.

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

По завершении вы увидите этот экран подтверждения параметров установки:

![how-to-install-python-webpi-27-2](./media/python-how-to-install/how-to-install-python-webpi-27-2.png)

После завершения установки введите "python" в командной строке и убедитесь, что все прошло успешно.  В зависимости от способа установки вам может потребоваться задать переменную "path", чтобы найти (нужную версию) Python:

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

После установки вам должны быть доступны компоненты Python и клиентские библиотеки, находящиеся в папке по умолчанию.

		C:\Python27\Lib\site-packages\azure


### Python 3.4

Установщик WebPI предоставляет все, что нужно для разработки приложений Python Azure.

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)
 
По завершении вы увидите этот экран подтверждения параметров установки:

![how-to-install-python-webpi-34-2](./media/python-how-to-install/how-to-install-python-webpi-34-2.png)

После завершения установки введите "python" в командной строке и убедитесь, что все прошло успешно.  В зависимости от способа установки вам может потребоваться задать переменную "path", чтобы найти (нужную версию) Python:

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

После установки вам должны быть доступны компоненты Python и клиентские библиотеки, находящиеся в папке по умолчанию.

		C:\Python34\Lib\site-packages\azure


### Получение дополнительных пакетов

Этот выпуск ориентирован в первую очередь на веб-приложения, но вы можете просмотреть каталог [Python Package Index (PyPI)][], где доступен обширный выбор другого программного обеспечения.  Если решили установить дистрибутив, то получите основную часть нужного кода для самых различных сценариев, от веб-разработки до технических вычислений.


### Средства Python для Visual Studio

Средства Python для Visual Studio (PTVS) представляют собой бесплатно подключаемый модуль с открытым исходным кодом от корпорации Майкрософт, который превращает Visual Studio в полноценную интегрированную среду разработки Python:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Использование PTVS необязательно, однако рекомендуется, поскольку он обеспечивает поддержку Python и проектов/решений Django, отладку, профилирование, интерактивное окно, редактирование шаблонов и Intellisense.

PTVS также упрощает развертывание в Microsoft Azure с поддержкой для [развертывания в облачных службах и веб-сайтах][].

PTVS работает с установленными экземплярами Visual Studio 2010, 2012 или 2013.  Дополнительные сведения и информацию о загрузке см. в разделе [Средства Python для Visual Studio на CodePlex][].  

## Удаление из Windows

Продукты WebPI из **пакета Azure SDK для Python** являются не приложениями в обычном смысле слова, а коллекцией различных продуктов, таких как 32-разрядный Python 2.7/3.4, клиентских API Azure для Python и т. п., объединенных в пакет.  Поэтому он не имеет собственной программы удаления, и вам потребуется по отдельности удалять установленные им программы из панели управления Windows.  

Если вы захотите переустановить **пакет Azure SDK для Python**, просто откройте окно командной строки PowerShell с правами администратора и выполните следующую команду:

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

а затем перезапустите WebPI.

## Установка в Linux и MacOS

Скорее всего, Python уже установлен на вашем компьютере разработчика.  Это можно проверить, введя следующую команду:

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

Здесь мы видим, что в этой виртуальной машине с Ubuntu Server 14.04 LTS, работающей на Azure, установлен CPython 2.7.6. Если необходимо выполнить обновление, следуйте указаниям своей ОС по обновлению пакета.

Чтобы установить клиентские библиотеки Python Azure, используйте **pip** для извлечения его из **PyPI**:

	curl https://bootstrap.pypa.io/get-pip.py | sudo python
	
Приведенная выше команда автоматически запрашивает пароль пользователя root. Введите его и нажмите клавишу ВВОД.  Далее:
	
	sudo pip install azure

Теперь вы увидите установленные клиентские библиотеки в области **site-packages**.  В MacOS:

![how-to-install-python-mac-site](./media/python-how-to-install/how-to-install-python-mac-site.png)

При разработке в mac/linux существует два основных поддерживаемых сценария:

1. Использование служб Azure с помощью клиентских библиотек для Python

2. Запуск приложения на виртуальной машине Linux

Первый сценарий позволяет создавать полнофункциональные веб-приложения, использующие преимущества возможностей Azure PaaS, такие как хранилища BLOB-объектов, очереди и т. д., через оболочки Pythonic для API REST Azure. Они совершенно одинаково работают в Windows, Mac и Linux.  Примеры см. в учебниках и практических руководствах.  Эти клиентские библиотеки также можно использовать в виртуальной машине Linux.

При сценарии с виртуальной машиной вы просто запускаете выбранную виртуальную машину Linux (Ubuntu, CentOS, Suse), а затем выполняете нужные компоненты и управляете ими.  В качестве примера можно запустить [IPython](http://ipython.org) REPL/notebook на компьютере Windows/Mac/Linux и указать в браузере мультипроцессную виртуальную машину Linux или Windows с запущенной подсистемой IPython в Azure. Дополнительную информацию по установке IPython можно найти в соответствующем учебнике.

Сведения о способах настройки виртуальной машины Linux см. в разделе [Управление Linux.](/ru-ru/manage/linux/)

## Дополнительные ресурсы и программное обеспечение:

* [Распространяемый набор Enthought Python Distribution][]
* [Распространяемый набор ActiveState Python Distribution][]
* [SciPy - набор научных библиотек Python][]
* [NumPy - библиотека числовых значений для Python][]
* [Django Project - зрелая веб-платформа/CMS][]
* [IPython - расширенный интерфейс REPL/Notebook для Python][]
* [IPython Notebook в Azure][]
* [Средства Python 2.0 для Visual Studio на CodePlex][]
* [Virtualenv][]


[Распространяемый набор Enthought Python Distribution]: http://www.enthought.com 
[Распространяемый набор ActiveState Python Distribution]: http://www.activestate.com
[SciPy - набор научных библиотек Python]: http://www.scipy.org
[NumPy - библиотека числовых значений для Python]: http://www.numpy.org
[Django Project - зрелая веб-платформа/CMS]: http://www.djangoproject.com 
[IPython - расширенный интерфейс REPL/Notebook для Python]: http://ipython.org
[IPython Notebook в Azure]: /ru-ru/documentation/articles/virtual-machines-python-ipython-notebook
[развертывания в облачных службах и веб-сайтах]: /ru-ru/documentation/articles/cloud-services-web-sites-python-django-app-with-ptvs/
[Средства Python 2.0 для Visual Studio на CodePlex]: http://pytools.codeplex.com 
[Средства Python для Visual Studio на CodePlex]: http://pytools.codeplex.com 
[Каталог Python Package Index (PyPI)]: http://pypi.python.org/pypi
[Python Package Index (PyPI)]: http://pypi.python.org/pypi
[Virtualenv]: http://pypi.python.org/pypi/virtualenv 
[Microsoft Azure SDK для Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Microsoft Azure SDK для Python 3,4]: http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409
[Настройка виртуальной Машины Linux через портал Azure]: ../../../shared/chunks/create-and-configure-opensuse-vm-in-portal
[Использование средств командной строки Azure для Mac и Linux]: ../../shared/chunks/crossplat-cmd-tools

