<properties linkid="develop-python-install-python" urlDisplayName="Установка Python" pageTitle="Установка Python и пакета SDK - Azure" metaKeywords="Пакет Azure Python SDK" description="Узнайте, как установить Python и пакет SDK с помощью Azure." metaCanonical="" services="" documentationCenter="Python" title="Установка Python и пакета SDK" authors="" solutions="" manager="" editor="" />




# Установка Python и пакета SDK
Python довольно легко устанавливается в Windows и поставляется предварительно установленным в Mac и Linux.  В этом руководстве рассматривается установка и подготовка компьютера к использованию с Azure.  Это руководство поможет вам изучить следующие темы:

* Новые возможности пакета Python Azure SDK
* Какой вариант и какую версию Python использовать
* Установка в Windows
* Установка в Mac и Linux

## Новые возможности пакета Python Azure SDK

Azure SDK для Python включает компоненты для разработки, развертывания приложений Python в Azure и управления ими. Пакет Azure SDK для Python включает следующие средства:

* **Клиентские библиотеки Python для Azure**. Эти библиотеки классов предоставляют интерфейс для доступа к функциям Azure, таким как службы управления данными и облачные службы.  
* **Средства командной строки Azure для Mac и Linux**. Это программы командной строки для развертывания служб Azure, таких как веб-сайты Azure и виртуальные машины Azure, и управления ими. Эти средства работают на любой платформе, включая Windows, Linux и Mac.
* **PowerShell для Azure (только для Windows)**. Это набор командлетов PowerShell для развертывания служб Azure, таких как облачные службы и виртуальные машины, и управления ими.
* **Эмуляторы Azure (только для Windows)**. Эмуляторы вычислений и хранения — это локальные эмуляторы облачных служб и служб управления данными, которые позволяют тестировать приложение локально. Эмуляторы Azure работают только в Windows.


Для основных сценариев для этого выпуска:

* **Windows**: облачные службы - например, сайт Django с использованием веб-ролей
* **Mac/Linux**: IaaS - запускайте в виртуальной машине все, что захотите; используйте службы Azure с помощью Python

## Какой вариант и какую версию Python использовать

Доступно несколько видов интерпретаторов Python - например:

* CPython - стандартный и наиболее часто используемый интерпретатор Python
* IronPython - интерпретатор Python, работающей на базе .net/CLR
* Jython - интерпретатор Python, работающей на базе JVM

Для данного выпуска протестирован и поддерживается только **CPython**. Кроме того, рекомендуется использовать версию не ниже 2.7. Поддержка **IronPython** будет добавлена в ближайшем будущем.

## Где можно получить Python?

Существует несколько способов получить CPython:

* Непосредственно с сайта www.python.org
* Из известных дистрибутивов, таких как www.enthought.com или www.ActiveState.com
* Построение из исходного кода!

При отсутствии особой необходимости мы рекомендуем использовать первые два варианта в соответствии с приведенным ниже описанием.

## Установка в Windows

Для Windows можно использовать предоставленный главным центром разработчиков Python [установщик WebPI], который упрощает установку (он загружает CPython с сайта www.python.org).

**Примечание.** На сервере Windows Server для загрузки установщика WebPI может потребоваться настроить параметры конфигурации усиленной безопасности Internet Explorer (выберите "Пуск"/"Администрирование"/"Диспетчер серверов", щелкните элемент **Настроить конфигурацию усиленной безопасности Internet Explorer** и установите значение "Выкл.")

![how-to-install-python-webpi-1](./media/python-how-to-install/how-to-install-python-webpi-1.png)

Установщик WebPI предоставляет все необходимое для приложений Python Azure, а также определенный уровень поддержки для приложений Django:


![how-to-install-Python-webpi-2](./media/python-how-to-install/how-to-install-python-webpi-2.png)
 

По завершении вы увидите этот экран подтверждения параметров установки:


![how-to-install-python-webpi-3](./media/python-how-to-install/how-to-install-python-webpi-3.png)


После завершения установки введите python в командной строке, чтобы убедиться, что все прошло успешно.  В зависимости от способа установки вам может потребоваться задать переменную "path", чтобы найти (правильную версию) Python:

![how-to-install-python-win-run](./media/python-how-to-install/how-to-install-python-win-run.png)


Хотя этот выпуск ориентирован в первую очередь на веб-приложения с Django, вы можете просмотреть каталог [Python Package Index (PyPI)][], где доступен обширный выбор другого программного обеспечения.  Если решили установить дистрибутив, то получите основную часть нужного кода для самых различных сценариев — от веб-разработки до технических вычислений.

Для просмотра установленных пакетов Python введите следующую команду в поле **site-packages**, чтобы найти расположение:

![how-to-install-python-win-site](./media/python-how-to-install/how-to-install-python-win-site.png)

При этом выводится список установленных в системе компонентов.

После установки вам должны быть доступны компоненты Python, Django, клиентские библиотеки, находящиеся в папке по умолчанию:

		C:\Python27\Lib\site-packages\windowsazure
		C:\Python27\Lib\site-packages\django

### Средства Python 2.0 для Visual Studio

Средства Python для Visual Studio представляют собой бесплатный подключаемый модуль с открытым исходным кодом от корпорации Майкрософт, который превращает Visual Studio в полноценную интегрированную среду разработки Python:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Использование средств Python для Visual Studio не является обязательным, однако рекомендуется, поскольку обеспечивает поддержку проектов и решений Python и Django, отладки, профилирования, редактирования шаблонов и Intellisense, развертывания в облаке и т. п. Эта надстройка совместима с вашей текущей установкой Visual Studio 2010.  При отсутствии Visual Studio 2010 WebPI устанавливает бесплатную интегрированную оболочку и PTVS, что дает вам **совершенно бесплатную** интегрированную среду разработки "VS Express Python".  Дополнительные сведения см. в разделе [Средства Python для Visual Studio на CodePlex][].  

Примечание. Хотя подключаемый модуль PTVS имеет небольшой размер, на загрузку интегрированной оболочки потребуется больше времени. Версия интегрированной оболочки в настоящее время не поддерживает возможность добавления проекта развертывания Azure.

## Удаление из Windows

Продукта WebPI из **пакета Azure SDK для Python за июнь 2012 года** является не приложением в обычном смысле, а коллекцией различных продуктов, таких как 32-разрядный Python 2.7, клиентские API Azure для Python, Django и т. п., объединенных в пакет.  Поэтому он не имеет собственной программы удаления, и вам потребуется по отдельности удалять установленные им программы из панели управления Windows.  

Если вы захотите переустановить **пакет Azure SDK для Python**, просто откройте окно командной строки PowerShell с правами администратора и выполните следующую команду:

	rm -force "HKLM:\SOFTWARE\Microsoft\Windows Azure SDK for Python - June 2012"

а затем перезапустите WebPI.

## Установка в Linux и MacOS

Скорее всего, Python уже установлен на вашем компьютере разработчика.  Это можно проверить, введя следующую команду:

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

Здесь мы видим, что в этой виртуальной машине Suse Azure установлен CPython 2.7.2, что отлично подходит для запуска учебников по Azure и примеров Django. Если необходимо выполнить обновление, следуйте указаниям своей ОС по обновлению пакета.  Обратите внимание, что в общем случае лучше не трогать присутствующий в системе продукт Python (от этой версии могут зависеть другие компоненты) и установить новую версию через [Virtualenv][].

Чтобы установить клиентские библиотеки Python Azure, используйте **pip** для извлечения его из **PyPI**:

	curl https://raw.github.com/pypa/pip/master/contrib/get-pip.py | sudo python
	
Приведенная выше команда автоматически запрашивает пароль пользователя root. Введите его и нажмите клавишу ВВОД.  Далее:
	
	sudo /usr/local/bin/pip-2.7 install azure

Теперь вы увидите установленные клиентские библиотеки в области **site-packages**.  В MacOS:

![MacOS site-packages](./media/python-how-to-install/how-to-install-python-mac-site.png)

При разработке в mac/linux существует два основных сценария, поддерживаемых в данном выпуске:

1. Использование служб Azure с помощью клиентских библиотек для Python

2. Запуск приложения на виртуальной машине Linux

Первый сценарий позволяет создавать полнофункциональные веб-приложения, использующие преимущества возможностей Azure PaaS, такие как хранилища BLOB-объектов, очереди и т. д., через оболочки Pythonic для API REST Azure.  Они совершенно одинаково работают в Windows, Mac и Linux.  Примеры см. в учебниках и практических руководствах.  Эти клиентские библиотеки также можно использовать в виртуальной машине Linux.

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
* [Средства Python для Visual Studio на CodePlex][]
* [Virtualenv][]


[Распространяемый набор Enthought Python Distribution]: http://www.enthought.com 
[Распространяемый набор ActiveState Python Distribution]: http://www.activestate.com
[SciPy - набор научных библиотек Python]: http://www.scipy.org
[NumPy - библиотека числовых значений для Python]: http://www.numpy.org
[Django Project - зрелая веб-платформа/CMS]: http://www.djangoproject.com 
[IPython - расширенный интерфейс REPL/Notebook для Python]: http://ipython.org
[IPython Notebook в Azure]: http://windowsazure.com/ru-ru/documentation/articles/virtual-machines-python-ipython-notebook
[Средства Python для Visual Studio на CodePlex]: http://pytools.codeplex.com 
[Каталог Python Package Index (PyPI)]: http://pypi.python.org/pypi
[Virtualenv]: http://pypi.python.org/pypi/virtualenv 
[Установщик WebPI]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Настройка виртуальной Машины Linux через портал Azure]: ../../../shared/chunks/create-and-configure-opensuse-vm-in-portal
[Использование средств командной строки Azure для Mac и Linux]: ../../shared/chunks/crossplat-cmd-tools


