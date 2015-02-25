<properties pageTitle="IPython Notebook - учебник по Azure" description="В этом учебнике показано, как развернуть IPython Notebook в Azure с использованием виртуальных машин Linux или Windows." services="virtual-machines" documentationCenter="python" authors="huguesv" manager="wpickett" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="python" ms.topic="article" ms.date="09/25/2014" ms.author="huvalo"/>





# IPython Notebook в Azure

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Этот <a href="http://ipython.org">проект IPython</a> предоставляет набор инструментов для научных вычислений, включая мощную интерактивную среду, высокопроизводительные и удобные в использовании библиотеки параллельных вычислений, а также веб-среду под названием IPython Notebook. Notebook содержит рабочую среду для интерактивных вычислений, которая сочетает в себе выполнение кода с созданием документа вычислений в реальном времени. Эти файлы записной книжки могут содержать произвольный текст, математические формулы, код ввода, результаты, графику, видео и любые другие виды носителей, которые может отображать современный веб-браузер.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/python/ipy-youtube2.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизвести видео</span></a> <span class="time">5:22</span></div>
</div>

Независимо от того, кем вы являетесь - новичком в Python, который хочет изучить этот язык в интересной интерактивной среде, или специалистом, которому требуется выполнить какие-то серьезные параллельные или технические вычисления, IPython Notebook станет великолепным выбором. В качестве иллюстрации его возможностей на следующем снимке экрана показано использование IPython Notebook в сочетании с пакетами SciPy и matplotlib для анализа структуры записи звука:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png)

В этом документе показано, как развернуть IPython Notebook в MicrosoftAzure с использованием виртуальных машин Linux или Windows.  С помощью IPython Notebook в Azure можно с легкостью предоставить веб-интерфейс для масштабируемых вычислительных ресурсов в сочетании со всеми возможностями Python и его многочисленных библиотек.  Так как вся установка проводится в облаке, пользователи могут получать доступ к этим ресурсам без необходимости выполнять настройку локально вне современного браузера.

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Создание и настройка виртуальной машины в Azure

Первым шагом является создание виртуальной машины (VM) в Azure.
Эта виртуальная машина является полноценной операционной системой в облаке и будет использоваться для запуска IPython Notebook. В среде Azure можно размещать виртуальные машины Linux и Windows. Мы рассмотрим установку IPython на обоих типах виртуальных машин.

### Виртуальная машина Linux

Следуйте приведенным [здесь][portal-vm-linux] указаниям, чтобы создать виртуальную машину дистрибутива  *OpenSUSE* или  *Ubuntu*. В этом учебнике используются OpenSUSE 13.1 и Ubuntu Server 14.04 LTS. Предположим, что имя пользователя по умолчанию -  *azureuser*.

### Виртуальная машина Windows

Следуйте приведенным [здесь][portal-vm-windows] указаниям, чтобы создать виртуальную машину дистрибутива  *Windows Server 2012 R2 Datacenter*. В этом учебнике предполагается, что имя пользователя -  *azureuser*.

## Создание конечной точки для IPython Notebook

Этот шаг применяется для виртуальной машины Windows и Linux. Позже мы настроим IPython для запуска сервера Notebook в порте 9999. Чтобы сделать этот порт общедоступным, необходимо создать конечную точку на портале управления Azure. Эта конечная точка открывает порт в брандмауэре Azure и сопоставляет общий порт (HTTPS443) с частным портом на виртуальной машине (9999).

Чтобы создать конечную точку, перейдите на панель мониторинга виртуальной машины и щелкните "Конечные точки", а затем - "Добавить конечную точку", чтобы создать новую конечную точку (в этом примере это  `ipython_nb`). Выберите протокол TCP, общий порт 443 и частный порт 9999:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png)

После этого действия вкладка панели мониторинга "Конечные точки" будет иметь следующий вид:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png)

## Установка необходимого программного обеспечения в виртуальной машине

Чтобы запустить IPython Notebook на своей виртуальной машине, необходимо предварительно установить IPython и его зависимости.

### Linux (OpenSUSE)

Чтобы установить IPython и его зависимости, войдите по протоколу SSH в виртуальную машину Linux и выполните следующее.

Установите [NumPy][numpy], [Matplotlib][matplotlib], [Tornado][tornado] и другие зависимости IPython, выполнив следующее:

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install python-jinja2
    sudo zypper install ipython

### Linux (Ubuntu)

Чтобы установить IPython и его зависимости, войдите по протоколу SSH в виртуальную машину Linux и выполните следующее.

Установите [NumPy][numpy], [Matplotlib][matplotlib], [Tornado][tornado] и другие зависимости IPython, выполнив следующее:

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

Чтобы установить IPython и его зависимости на виртуальной машине Windows подключитесь к этой ВМ с использованием удаленного рабочего стола. Затем выполните следующее в оболочке Windows PowerShell, чтобы выполнить все действия командной строки.

**Примечание**. Для скачивания данных с помощью Internet Explorer необходимо изменить некоторые параметры безопасности.  В **Диспетчере серверов** щелкните **Локальный сервер**, а затем - **Расширенная настройка безопасности IE** и отключите ее для администраторов.  Можно включить ее снова после завершения установки IPython.

1.  Установите Python 2.7.8 (32-разрядную версию) с сайта [python.org](http://www.python.org/download). 
    Также вам потребуется добавить  `C:\Python27` и `C:\Python27\Scripts` в переменную среды  `PATH`
    .

1.  Установите pip и setuptools. Для этого скачайте файл **get-pip.py**
    с сайта [https://pip.pypa.io/en/latest/installing.html](https://pip.pypa.io/en/latest/installing.html) и выполните следующую
    команду:

        python get-pip.py

1.  Установите [Tornado][tornado], [PyZMQPyZMQ][pyzmq] и другие зависимости IPython, выполнив следующее:

        easy_install tornado
        easy_install pyzmq
        easy_install jinja2
        easy_install six
        easy_install python-dateutil
        easy_install pyparsing

1.  Скачайте и установите [NumPy][numpy] с помощью
   установщика двоичных файлов ( `.exe`), доступного на веб-сайте этого проекта.  На момент написания статьи последняя версия - **numpy-1.9.0-win32-superpack-python2.7.exe**.

1.  Скачайте и установите [Matplotlib][matplotlib] с помощью
   установщика двоичных файлов ( `.exe`), доступного на веб-сайте этого проекта.  На момент написания статьи последняя версия - **matplotlib-1.4.0.win32-py2.7.exe**.

1.  Скачайте и установите OpenSSL. Версии OpenSSL для Windows можно найти на сайте [http://slproweb.com/products/Win32OpenSSL.html](http://slproweb.com/products/Win32OpenSSL.html).

	* Если будет установлена версия **Light**, необходимо также установить **Visual C++ 2008 Redistributable** (также доступно на этой странице).

	* Вам нужно будет добавить  `C:\OpenSSL-Win32\bin` в переменную среды  `PATH`.

	> [AZURE.NOTE] При установке OpenSSL используйте версию 1.0.1g или более позднюю, так как они включают в себя исправление для уязвимости безопасности Heartbleed.

1.  Установите IPython с помощью следующей команды:

        easy_install ipython

1.  Откройте порт в брандмауэре Windows.  В Windows Server 2012 брандмауэр по умолчанию блокирует входящие подключения.  Чтобы открыть порт 9999, выполните следующие действия:

    - Запустите **брандмауэр Windows в режиме повышенной безопасности** на начальном экране.

    - На левой панели щелкните **Правила для входящих подключений**.

	- На панели "Действия" щелкните **Создать правило...**.

	- В мастере создания правил для входящих подключений выберите **Порт**.

	- На следующем экране выберите **TCP** и введите **9999** в поле **Определенные локальные порты**.

	- Примите значения по умолчанию, присвойте правилу имя и нажмите кнопку "Готово".

### Настройка IPython Notebook

Затем следует настроить IPython Notebook. Первый шаг - это создать пользовательский
профиль конфигурации IPython для инкапсуляции информации о конфигурации:

    ipython profile create nbserver

Далее мы выполним команду  `cd`, чтобы перейти к папке профиля для создания своего SSL-сертификата и изменения файла конфигурации профилей.

В Linux (OpenSUSE):

    cd ~/.config/ipython/profile_nbserver/

В Linux (Ubuntu):

    cd ~/.ipython/profile_nbserver/

В Windows:

    cd \users\azureuser\.ipython\profile_nbserver

На обеих платформах создайте SSL-сертификат, как показано ниже:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Обратите внимание - так как создается самозаверяющий SSL-сертификат, при подключении к записной книжке в браузере будет выведено предупреждение системы безопасности.  Для долгосрочного использования в рабочей среде потребуется должным образом подписанный сертификат, связанный с вашей организацией.  Так как управление сертификатами выходит за рамки этой демонстрации, в данном случае мы ограничимся самозаверяющим сертификатом.

Помимо сертификата, необходимо также указать пароль для защиты записной книжки от несанкционированного использования.  Из соображений безопасности IPython использует зашифрованные пароли в своем файле конфигурации, поэтому сначала необходимо зашифровать пароль.  IPython предоставляет для этого соответствующую утилиту; в командной строке выполните следующую команду:

    python -c "import IPython;print IPython.lib.passwd()"

Будет предложено ввести пароль и подтверждение, а затем напечатать пароль следующим образом:

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)
    
Далее предстоит отредактировать файл конфигурации профиля, то есть файл
`ipython_notebook_config.py` в текущей папке профиля. Возможно, этот файл не существует - создайте его.   Этот файл содержит несколько полей, которые по умолчанию закомментированы.  Этот файл можно открыть в любом текстовом редакторе. Необходимо убедиться, что в нем есть по крайней мере следующее содержимое:

    c = get_config()
    
    # This starts plotting support always with matplotlib
    c.IPKernelApp.pylab = 'inline'
    
    # You must give the path to the certificate file.
    
    # If using a Linux VM (OpenSUSE):
    c.NotebookApp.certfile = u'/home/azureuser/.config/ipython/profile_nbserver/mycert.pem'

    # If using a Linux VM (Ubuntu):
    c.NotebookApp.certfile = u'/home/azureuser/.ipython/profile_nbserver/mycert.pem'
    
    # And if using a Windows VM:
    c.NotebookApp.certfile = r'C:\Users\azureuser\.ipython\profile_nbserver\mycert.pem'
    
    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '
    
    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### Запустите IPython Notebook

Теперь можно запустить IPython Notebook. Для этого перейдите в каталог, в котором предполагается хранить записные книжки, и запустите сервер IPython Notebook:

    ipython notebook --profile=nbserver

Теперь можно получить доступ к IPython Notebook по адресу:
`https://[Your Chosen Name Here].cloudapp.net`.

При первом обращении к записной книжке, на странице входа будет запрошен пароль:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png)

После входа в систему вы увидите "Панель мониторинга IPython Notebook", которая является центром управления для всех операций с записными книжками.  На этой странице можно создать новые записные книжки, открыть существующие и т. д.:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png)

Если нажать кнопку "Создать записную книжку", будет открыта страница, похожая на следующую:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png)

Область, отмеченная запросом  `In []:` , является областью ввода: здесь можно
ввести любой допустимый код Python и он будет исполнен, если нажать клавиши  `Shift-Enter` или щелкнуть значок "Воспроизведение" (направленный вправо треугольник на панели инструментов).

Так как для записной книжки настроен запуск с автоматической поддержкой NumPy и matplotlib, можно даже создавать рисунки, например:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png)

## Мощная парадигма: документы с вычислениями в режиме реального времени и богатыми возможностями мультимедиа

Сама работа с записной книжкой должна быть интуитивно понятна для любого, кто использовал Python и текстовый процессор, так как это является в некотором смысле сочетанием обоих вариантов: здесь можно выполнять блоки кода Python, но можно также сохранять заметки и другой текст, изменив стиль в ячейки с "Код" на "Разметка" в раскрывающемся меню на панели инструментов:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png)


Но это гораздо больше, чем текстовый процессор, так как записная книжка IPython позволяет совмещать вычисления и широкие возможности мультимедиа (текст, графику, видео и практически любые данные, которые способен отображать современный браузер). Например, можно сочетать пояснительные видео с расчетами для использования в образовательных целях:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png)
 или встраивать внешние веб-сайты, которые остаются рабочими и пригодными для использования, в файл записной книжки:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png)

И с помощью множества великолепных библиотек Python, предназначенных для научных и технических вычислений, можно с легкостью выполнять и простые вычисления, и анализ сложных сетей в единой среде:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png)

Эта парадигма сочетания мощи современных веб-технологий с вычислениями в режиме реального времени предоставляет множество возможностей и идеально подходит для облака. Notebook можно использовать для следующих целей:

* как вычислительный электронный блокнот для регистрации исследовательской работы над проблемой;

* для совместного использования результатов с коллегами либо в форме вычислений в режиме реального времени,
  либо в формате печатной копии (HTML, PDF);

* для распространения и представления материалов интерактивного обучения, включающих вычисления,
  что позволит слушателям сразу же приступить к экспериментам с реальным кодом, изменять его
  и повторно выполнять в интерактивном режиме;

* для предоставления "исполняемых документов", в которых представлены результаты исследования таким образом,
  что оно может быть немедленно воспроизведено, проверено и дополнено другими;

* как платформа для совместной работы: несколько пользователей могут войти на один
  сервер Notebook для совместной работы в интерактивном сеансе вычислений;

* и многое другое...

В репозитории исходного кода IPython доступен весь каталог с [примерами записных книжек](https://github.com/ipython/ipython/tree/master/examples/notebooks), которые можно скачать и использовать в виртуальной машине IPython для Azure для экспериментов.
Просто скачайте `IPYNB`-файлы с сайта и передайте их на панель мониторинга виртуальной машины Azure для записной книжки (или скачайте их непосредственно в виртуальную машину).

## Заключение

IPython Notebook предоставляет богатый возможностями интерфейс для интерактивной работы с мощной экосистемой Python в Azure.  Он охватывает широкий диапазон вариантов использования, включая простой просмотр и изучения Python, анализ данных и визуализацию, моделирование и параллельные вычисления. Итоговые документы Notebook содержат полную запись выполненных вычислений и могут использоваться совместно с другими пользователями IPython.  Приложение IPython Notebook можно использовать в качестве локального приложения, однако оно идеально подходит для развертываний в облаке Azure

Основные функции IPython также доступны в Visual Studio в 
[инструментах Python для Visual Studio](http://pytools.codeplex.com) (PTVS). PTVS является бесплатным подключаемым модулем с открытым кодом от корпорации Майкрософт, который превращает Visual Studio в среду разработки Python с расширенными возможностями, включающую редактор с интеграцией IntelliSense, средств отладки, профилирования и параллельных вычислений.



[tornado]:      http://www.tornadoweb.org/          "Tornado"
[pyzmq]:        https://github.com/zeromq/pyzmq     "PyZMQ"
[numpy]:        http://www.numpy.org/               "NumPy"
[matplotlib]:   http://matplotlib.sourceforge.net/  "Matplotlib"

[portal-vm-windows]: /ru-ru/manage/windows/tutorials/virtual-machine-from-gallery/
[portal-vm-linux]: /ru-ru/manage/linux/tutorials/virtual-machine-from-gallery/

<!--HONumber=42-->
