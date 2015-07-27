<properties 
	pageTitle="Создание веб-приложений с помощью Flask в Azure" 
	description="В учебнике описывается, как запустить веб-приложение на языке Python в Azure." 
	services="app-service\web" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="huguesv"/>




# Создание веб-приложений с помощью Flask в Azure

В этом учебнике описывается, как начать работу с языком Python в [веб-приложениях службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Служба веб-приложений предоставляет ограниченное бесплатное размещение приложений, а также возможности их быстрого развертывания и использования языка Python. По мере роста приложения его можно перевести на платное размещение и интегрировать во все другие службы Azure.

Вы создадите приложение с помощью веб-платформы Flask (см. другие версии этого учебника для платформ [Django](web-sites-python-create-deploy-django-app.md) и [Bottle](web-sites-python-create-deploy-bottle-app.md)). Затем вы создадите веб-сайт из коллекции Azure, настроите развертывание через Git и локально клонируете репозиторий. После этого вы запустите приложение локально, внесете в него изменения, зафиксируете их и отправите в Azure. В этом учебнике показано, как выполнить вышеуказанные действия в Windows, Mac или Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

## Предварительные требования

- Windows, Mac или Linux
- Python 2.7 или 3.4
- setuptools, pip, virtualenv (только для Python 2.7)
- Git
- [Средства Python 2.1 для Visual Studio](optional)

**Примечание**. Публикация TFS в настоящее время для проектов Python не поддерживается.

### Windows

Если у вас еще не установлен Python 2.7 или 3.4 (32-разрядная версия), рекомендуется сначала установить [пакет SDK для Azure для Python 2.7] или [пакет SDK для Azure для Python 3.4] с помощью установщика веб-платформы. При этом выполняется установка 32-разрядной версии Python (для установки на хост-компьютерах Azure), setuptools, pip, virtualenv, и т. д. Python также можно скачать с веб-сайта [python.org].

Для Git рекомендуется использовать [Git для Windows] или [GitHub для Windows]. При использовании Visual Studio можно использовать интегрированную поддержку Git.

Мы также рекомендуем установить [средства Python 2.1 для Visual Studio]. Это необязательно, но, если у вас есть [Visual Studio], например бесплатная версия Visual Studio Community 2013 или Visual Studio Express 2013 для Web, вы получите отличную интегрированную среду разработки Python.

### Mac/Linux

У вас должны быть установлены Python и Git. Убедитесь, что у вас установлена версия Python 2.7 или 3.4.


## Создание веб-приложения на портале Azure

Первым делом нужно создать веб-приложение на [портале Azure](https://portal.azure.com).

1. Войдите на портал Azure и нажмите кнопку **СОЗДАТЬ** в нижнем левом углу. 
2. Щелкните **Веб + мобильное** > **Azure Marketplace** > **Веб-приложения**.
3. В поле поиска введите "python".
4. В результатах поиска выберите **Flask**, а затем нажмите кнопку **Создать**.
5. Настройте новое приложение Flask, например создайте новый план службы приложений и новую группу ресурсов для него. Затем щелкните **Создать**.
6. Настройте публикацию Git для вновь созданного веб-приложения, следуя инструкциям из раздела [Непрерывное развертывание с помощью GIT в службе приложений Azure](web-sites-publish-source-control.md).


## Обзор приложений

### Содержимое репозитория Git

Ниже приведен обзор файлов, которые можно найти в исходном репозитории Git. Мы клонируем его в следующем разделе.

    \FlaskWebProject__init__.py
    \FlaskWebProject\views.py
    \FlaskWebProject\static\content\
    \FlaskWebProject\static\fonts\
    \FlaskWebProject\static\scripts\
    \FlaskWebProject\templates\about.html
    \FlaskWebProject\templates\contact.html
    \FlaskWebProject\templates\index.html
    \FlaskWebProject\templates\layout.html

Основные источники для приложения. Состоит из 3 страниц (index, about, contact) с макетом главной страницы. Использует такое статическое содержимое и сценарии, как bootstrap, jquery, modernizr и respond.

    \runserver.py

Поддержка локального сервера разработки. Используется для локального запуска приложения.

    \FlaskWebProject.pyproj
    \FlaskWebProject.sln

Файлы проекта для использования со [средствами Python для Visual Studio](http://pytools.codeplex.com).

    \ptvs_virtualenv_proxy.py

Прокси-сервер IIS для виртуальных сред и поддержки удаленной отладки PTVS.

    \requirements.txt

Внешние пакеты, необходимые для этого приложения. Сценарий развертывания установит пакеты, указанные в этом файле, с помощью pip.
 
    \web.2.7.config
    \web.3.4.config

Файлы конфигурации IIS. Сценарий развертывания использует соответствующий web.x.y.config и скопирует его с именем web.config.

### Дополнительные файлы — настройка развертывания

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../../includes/web-sites-python-customizing-deployment.md)]

### Дополнительные файлы — среда выполнения Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### Дополнительные файлы на сервере

Некоторые файлы размещены на сервере, но не добавляются в репозиторий git. Такие файлы создаются с помощью сценария развертывания.

    \web.config

Файл конфигурации IIS. Создается из файла web.x.y.config при каждом развертывании.

    \env\

Виртуальная среда Python. Создается при развертывании, если совместимая виртуальная среда еще не создана в приложении. Пакеты, указанные в файле requirements.txt, устанавливаются с помощью pip. Однако если они уже установлены, pip не будет выполнять установку.

В следующих 3 разделах описывается разработка веб-приложения в 3 разных средах:

- Windows, со средствами Python для Visual Studio
- Windows с командной строкой;
- Mac и Linux с командной строкой.


## Развертывание веб-приложений — Windows — средства Python для Visual Studio

### Клонирование репозитория

Сначала клонируйте репозиторий, используя URL-адрес на портале Azure. Дополнительные сведения см. в разделе [Непрерывное развертывание с использованием GIT в службе приложений Azure](web-sites-publish-source-control.md).

Откройте файл решения (SLN-файл), который содержится в корневой папке репозитория.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-solution-flask.png)

### Создание виртуальной среды

Теперь мы создадим виртуальную среду для локальной разработки веб-сайта. Щелкните правой кнопкой мыши **Среды Python** и выберите **Добавить виртуальную среду...**.

- Убедитесь, что имя среды — `env`.

- Выберите базовый интерпретатор. Убедитесь, что используется та же версия Python, которая выбрана для веб-приложения (в файле runtime.txt или в колонке **Параметры приложения** веб-приложения на портале Azure).

- Убедитесь, что установлен флажок для скачивания и установки пакетов.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-add-virtual-env-27.png)

Щелкните **Создать**. Таким образом будет создана виртуальная среда и установлены зависимости из файла requirements.txt.

### Запуск приложения на сервере разработки

Нажмите клавишу F5, чтобы начать отладку, и браузер автоматически откроет страницу, запущенную локально.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

Можно установить точки останова в источниках, использовать окна контрольных значений и т. д. Дополнительные сведения о различных функциях см. в [документации PTVS].

### Внесение изменений

Теперь вы можете поэкспериментировать, внося изменения в исходные коды и/или шаблоны приложений.

После тестирования изменений их необходимо сохранить в репозитории Git:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-commit-flask.png)

### Установка дополнительных пакетов

Зависимости приложений могут не ограничиваться Python и Flask.

С помощью pip можно установить дополнительные пакеты. Чтобы установить пакет, щелкните правой кнопкой мыши в виртуальной среде и выберите **Установить пакет Python**.

Например, чтобы установить пакет SDK для Azure для Python, который предоставляет доступ к службе хранилища Azure, служебной шине и другим службам Azure, введите `azure`:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-install-package-dialog.png)

Щелкните правой кнопкой мыши в виртуальной среде и выберите **Создать файл requirements.txt**, чтобы обновить файл requirements.txt.

Затем сохраните внесенные в этот файл изменения в репозитории Git.

### Развертывание в Azure

Чтобы запустить развертывание, щелкните **Синхронизировать** или **Отправить**. При синхронизации выполняется как принудительная отправка, так и применение по запросу.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-push.png)

Первое развертывание может занять некоторое время, необходимое для создания виртуальной среды, установку пакетов и т. д.

В Visual Studio не отображается ход выполнения развертывания. Информацию о том, как можно просмотреть результат, см. в разделе [Устранение неполадок — развертывание](#troubleshooting-deployment).

Перейдите по URL-адресу Azure, чтобы просмотреть внесенные изменения.


## Разработка веб-приложения — Windows — командная строка

### Клонирование репозитория

Сначала клонируйте репозиторий, используя URL-адрес на портале Azure, а затем добавьте репозиторий Azure в качестве удаленного. Дополнительные сведения см. в разделе [Непрерывное развертывание с использованием GIT в службе приложений Azure](web-sites-publish-source-control.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Создание виртуальной среды

Давайте создадим новую виртуальную среду для разработки (не добавляйте ее в репозиторий). Виртуальные среды на языке Python нельзя перемещать, поэтому каждому разработчику, работающему над приложением, будет необходимо создать такую среду локально.

Убедитесь, что используется та же версия Python, которая выбрана для веб-приложения (в файле runtime.txt или в колонке **Параметры приложения** веб-приложения на портале Azure).

Для Python 2.7:

    c:\python27\python.exe -m virtualenv env

Для Python 3.4:

    c:\python34\python.exe -m venv env

Установите все внешние пакеты, необходимые для этого приложения. Чтобы установить пакеты в своей виртуальной среде, вы можете использовать файл requirements.txt в корне репозитория:

    env\scripts\pip install -r requirements.txt

### Запуск приложения на сервере разработки

Вы можете запустить приложение на сервере разработки с помощью следующей команды:

    env\scripts\python runserver.py

В консоли отобразится URL-адрес и порт, который прослушивает сервер:

![](./media/web-sites-python-create-deploy-flask-app/windows-run-local-flask.png)

Далее откройте браузер и введите этот URL-адрес.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

### Внесение изменений

Теперь вы можете поэкспериментировать, внося изменения в исходные коды и/или шаблоны приложений.

После тестирования изменений их необходимо сохранить в репозитории Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Установка дополнительных пакетов

Зависимости приложений могут не ограничиваться Python и Flask.

С помощью pip можно установить дополнительные пакеты. Например, чтобы установить пакет SDK для Azure для Python, который предоставляет доступ к службе хранилища Azure, служебной шине, а также другим службам Azure, введите:

    env\scripts\pip install azure

Обязательно обновите файл requirements.txt:

    env\scripts\pip freeze > requirements.txt

Примените изменения:

    git add requirements.txt
    git commit -m "Added azure package"

### Развертывание в Azure

Чтобы запустить развертывание, принудительно отправьте эти изменения в Azure:

    git push azure master

Отобразятся выходные данные сценария развертывания, а также создание виртуальной среды, установку пакетов, и создание файла web.config.

Перейдите по URL-адресу Azure, чтобы просмотреть внесенные изменения.


## Разработка веб-приложения — Mac/Linux — командная строка

### Клонирование репозитория

Сначала клонируйте репозиторий, используя URL-адрес на портале Azure, а затем добавьте репозиторий Azure в качестве удаленного. Дополнительные сведения см. в разделе [Непрерывное развертывание с использованием GIT в службе приложений Azure](web-sites-publish-source-control.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Создание виртуальной среды

Давайте создадим новую виртуальную среду для разработки (не добавляйте ее в репозиторий). Виртуальные среды на языке Python нельзя перемещать, поэтому каждому разработчику, работающему над приложением, будет необходимо создать такую среду локально.

Убедитесь, что используется та же версия Python, которая выбрана для веб-приложения (в файле runtime.txt или в колонке **Параметры приложения** веб-приложения на портале Azure).

Для Python 2.7:

    python -m virtualenv env

Для Python 3.4:

    python -m venv env

Установите все внешние пакеты, необходимые для этого приложения. Чтобы установить пакеты в своей виртуальной среде, вы можете использовать файл requirements.txt в корне репозитория:

    env/bin/pip install -r requirements.txt

### Запуск приложения на сервере разработки

Вы можете запустить приложение на сервере разработки с помощью следующей команды:

    env/bin/python runserver.py

В консоли отобразится URL-адрес и порт, который прослушивает сервер:

![](./media/web-sites-python-create-deploy-flask-app/mac-run-local-flask.png)

Далее откройте браузер и введите этот URL-адрес.

![](./media/web-sites-python-create-deploy-flask-app/mac-browser-flask.png)

### Внесение изменений

Теперь вы можете поэкспериментировать, внося изменения в исходные коды и/или шаблоны приложений.

После тестирования изменений их необходимо сохранить в репозитории Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Установка дополнительных пакетов

Зависимости приложений могут не ограничиваться Python и Flask.

С помощью pip можно установить дополнительные пакеты. Например, чтобы установить пакет SDK для Azure для Python, который предоставляет доступ к службе хранилища Azure, служебной шине, а также другим службам Azure, введите:

    env/bin/pip install azure

Обязательно обновите файл requirements.txt:

    env/bin/pip freeze > requirements.txt

Примените изменения:

    git add requirements.txt
    git commit -m "Added azure package"

### Развертывание в Azure

Чтобы запустить развертывание, принудительно отправьте эти изменения в Azure:

    git push azure master

Отобразятся выходные данные сценария развертывания, а также создание виртуальной среды, установку пакетов, и создание файла web.config.

Перейдите по URL-адресу Azure, чтобы просмотреть внесенные изменения.


## Устранение неполадок — установка пакета

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## Устранение неполадок — виртуальная среда

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## Дальнейшие действия

Используйте следующие ссылки, чтобы узнать больше о средствах Flask и Python для Visual Studio.
 
- [Документация по Flask]
- [Документация по средствам Python для Visual Studio]

Дополнительную информацию об использовании табличного хранилища Azure и MongoDB см. в следующих статьях:

- [Использование Flask и MongoDB в Azure с помощью инструментов Python 2.1 для Visual Studio]
- [Использование Flask и табличного хранилища Azure с помощью инструментов Python 2.1 для Visual Studio]

## Изменения
* Указания по изменениям при переходе от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Руководство по смене старого портала на новый портал см. в разделе [Справочник по навигации на предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715).


<!--Link references-->
[Использование Flask и MongoDB в Azure с помощью инструментов Python 2.1 для Visual Studio]: web-sites-python-ptvs-flask-mongodb.md
[Использование Flask и табличного хранилища Azure с помощью инструментов Python 2.1 для Visual Studio]: web-sites-python-ptvs-flask-table-storage.md

<!--External Link references-->
[пакет SDK для Azure для Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[пакет SDK для Azure для Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git для Windows]: http://msysgit.github.io/
[GitHub для Windows]: https://windows.github.com/
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[средства Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Visual Studio]: http://www.visualstudio.com/
[документации PTVS]: http://pytools.codeplex.com/documentation
[Документация по средствам Python для Visual Studio]: http://pytools.codeplex.com/documentation
[Документация по Flask]: http://flask.pocoo.org/
 

<!---HONumber=July15_HO3-->