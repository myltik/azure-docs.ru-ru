<properties 
	pageTitle="Веб-сайты Python на Flask - учебник Azure" 
	description="Учебник, в котором вы ознакомитесь с запуском веб-сайта Python в Azure." 
	services="app-service\web" 
	documentationCenter="python" 
	tags="python"
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Создание веб-сайтов с помощью Flask

В этом учебнике описывается, как начать работать с Python на веб-сайтах Azure.  Веб-сайты Azure предоставляют ограниченное бесплатное размещение и быстрое развертывание, а также возможность использовать Python.  По мере роста приложения его можно перевести на платное размещение и интегрировать во все другие службы Azure.

Вы создадите приложение с помощью веб-платформы Flask (см. другие версии этого учебника для веб-платформ [Django](web-sites-python-create-deploy-django-app.md) и [Bottle](web-sites-python-create-deploy-bottle-app.md)).  Затем вы создадите веб-сайт из коллекции Azure, настроите развертывание через Git и локально клонируете репозиторий.  После этого вы запустите приложение локально, внесете в него изменения, зафиксируете их и отправите в Azure.  В этом учебнике показано, как выполнить вышеуказанные действия в Windows, Mac или Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]


## Предварительные требования

- Windows, Mac или Linux.
- Python 2.7 или 3.4.
- setuptools, pip, virtualenv (только для Python 2.7).
- Git
- [Средства Python 2.1 для Visual Studio][] (необязательно).

**Примечание**. Публикация TFS в настоящее время для проектов Python не поддерживается.

### Windows

Если у вас еще не установлен Python 2.7 или 3.4 (32-разрядная версия), рекомендуется сначала установить [пакет SDK для Azure для Python 2.7][] или [пакет SDK для Azure для Python 3.4][] с помощью установщика веб-платформы.  При этом выполняется установка 32-разрядной версии Python (для установки на хост-компьютерах Azure), setuptools, pip, virtualenv, и т. д.  Python также можно скачать с веб-сайта [python.org][].

Для Git рекомендуется использовать [Git для Windows][] или [GitHub для Windows][].  При использовании Visual Studio можно использовать интегрированную поддержку Git.

Мы также рекомендуем установить [средства Python 2.1 для Visual Studio][].  Это необязательно, но если у вас есть [Visual Studio][], например бесплатная версия Visual Studio Community 2013 или Visual Studio Express 2013 для Web, вы получите отличную интегрированную среду разработки Python.

### Mac/Linux

У вас должны быть установлены Python и Git. Убедитесь, что у вас установлена версия Python 2.7 или 3.4.


## Создание веб-сайта на портале

Первым этапом в создании приложения является создание веб-сайта с помощью портала управления Azure.  Для этого понадобится войти на портал и нажать кнопку **СОЗДАТЬ** в нижнем левом углу. Откроется окно. Последовательно выберите **СРЕДА ВЫПОЛНЕНИЯ ПРИЛОЖЕНИЙ**, **ВЕБ-САЙТ** и **ИЗ КОЛЛЕКЦИИ**.

![](./media/web-sites-python-create-deploy-flask-app/portal-create-site.png)

Отобразится окно со списком приложений в коллекции. Щелкните категорию **ПЛАТФОРМЫ ПРИЛОЖЕНИЯ** слева и выберите **Flask**.

![](./media/web-sites-python-create-deploy-flask-app/portal-gallery-flask.png)

На следующей странице введите название и область сайта и нажмите кнопку завершения.

Веб-сайт будет быстро создан. Вы можете нажать кнопку **ОБЗОР** на нижней панели инструментов и увидеть новое приложение Flask, выполняющееся в Azure.

![](./media/web-sites-python-create-deploy-flask-app/portal-website-flask.png)
 
Далее, можно добавить поддержку публикации с помощью Git.  Это можно сделать, выбрав ссылку **Настроить развертывания в системе управления версиями**.

![](./media/web-sites-python-create-deploy-flask-app/portal-site-created.png)

Прокрутите вниз диалоговое окно **Настройка развертывания** и выберите вариант **Локальный репозиторий Git**. Для продолжения щелкните стрелку вправо.

![](./media/web-sites-python-create-deploy-flask-app/portal-setup-deployment.png)

После настройки публикации с помощью репозитория Git тут же появится страница, сообщающая о создании репозитория. Когда он будет создан, появятся указания по подключению.  

![](./media/web-sites-python-create-deploy-flask-app/portal-repo-created.png)

Мы выполним их в следующих разделах.


## Обзор приложения

### Содержимое репозитория Git

Ниже приведен обзор файлов, которые можно найти в исходном репозитории Git. Мы клонируем его в следующем разделе.

    \FlaskWebProject\__init__.py
    \FlaskWebProject\views.py
    \FlaskWebProject\static\content\
    \FlaskWebProject\static\fonts\
    \FlaskWebProject\static\scripts\
    \FlaskWebProject\templates\about.html
    \FlaskWebProject\templates\contact.html
    \FlaskWebProject\templates\index.html
    \FlaskWebProject\templates\layout.html

Основные источники для приложения.  Состоит из 3 страниц (index, about, contact) с макетом главной страницы.  Использует такое статическое содержимое и сценарии, как bootstrap, jquery, modernizr и respond.

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

Файлы конфигурации IIS.  Сценарий развертывания использует соответствующий web.x.y.config и скопирует его с именем web.config.

### Дополнительные файлы - настройка развертывания

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../includes/web-sites-python-customizing-deployment.md)]

### Дополнительные файлы - среда выполнения Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

### Дополнительные файлы на сервере

Некоторые файлы размещены на сервере, но не добавляются в репозиторий git.  Такие файлы создаются с помощью сценария развертывания.

    \web.config

Файл конфигурации IIS.  Создается из файла web.x.y.config при каждом развертывании.

    \env\

Виртуальная среда Python.  Создается при развертывании, если совместимая виртуальная среда еще не создана на сайте.  Пакеты, указанные в файле requirements.txt, устанавливаются с помощью pip. Однако если они уже установлены, pip не будет выполнять установку.

В следующих 3 разделах описывается разработка веб-сайта в 3 разных средах:

- Windows, со средствами Python для Visual Studio
- Windows с командной строкой;
- Mac и Linux с командной строкой.


## Разработка веб-сайтов - Windows - средства Python для Visual Studio

### Клонирование репозитория

Сначала клонируйте репозиторий, используя URL-адрес на портале Azure.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-clone.png)

Откройте файл решения (SLN-файл), который содержится в корневой папке репозитория.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-solution-flask.png)

### Создание виртуальной среды

Теперь мы создадим виртуальную среду для локальной разработки веб-сайта.  Щелкните правой кнопкой мыши **Среды Python** и выберите **Добавить виртуальную среду...**

- Убедитесь, что имя среды - `env`.

- Выберите базовый интерпретатор.  Используйте ту же версию Python, что выбрана для сайта (в файле runtime.txt или на странице настройки сайта).

- Убедитесь, что установлен флажок для скачивания и установки пакетов.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-add-virtual-env-27.png)

Щелкните **Создать**.  Таким образом будет создана виртуальная среда и установлены зависимости из файла requirements.txt.

### Запуск страницы с помощью сервера разработки

Нажмите клавишу F5, чтобы начать отладку, и браузер автоматически откроет страницу, запущенную локально.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

Вы можете устанавливать точки останова в исходном коде, выполнять действия с окнами контрольных значений и т. д. Дополнительную информацию о разных возможностях см. в [документации по PTVS][].

### Внесение изменений

Теперь вы можете поэкспериментировать, внося изменения в исходные коды и/или шаблоны приложений.

После тестирования изменений их необходимо сохранить в репозитории Git:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-commit-flask.png)

### Установка дополнительных пакетов

Зависимости приложений могут не ограничиваться Python и Flask.

С помощью pip можно установить дополнительные пакеты.  Чтобы установить пакет, щелкните правой кнопкой мыши в виртуальной среде и выберите **Установить пакет Python**.

Например, чтобы установить пакет SDK для Azure для Python, который предоставляет доступ к службе хранилища Azure, Service Bus и другим службам Azure, введите `azure`:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-install-package-dialog.png)

Щелкните правой кнопкой мыши в виртуальной среде и выберите **Создать файл requirements.txt**, чтобы обновить файл requirements.txt.

Затем сохраните внесенные в этот файл изменения в репозитории Git.

### Развертывание в Azure

Чтобы запустить развертывание, щелкните **Синхронизировать** или **Отправить**.  При синхронизации выполняется как принудительная отправка, так и применение по запросу.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-push.png)

Первое развертывание может занять некоторое время, необходимое для создания виртуальной среды, установку пакетов и т. д.

В Visual Studio не отображается ход выполнения развертывания.  Информацию о том, как можно просмотреть результат, см. в разделе [Устранение неполадок - развертывание](#troubleshooting-deployment).

Перейдите по URL-адресу Azure, чтобы просмотреть внесенные изменения.


## Разработка веб-сайтов - Windows - командная строка

### Клонирование репозитория

Сначала клонируйте репозиторий, используя URL-адрес на портале Azure, а затем добавьте репозиторий Azure в качестве удаленного.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Создание виртуальной среды

Давайте создадим новую виртуальную среду для разработки (не добавляйте ее в репозиторий).  Виртуальные среды на языке Python нельзя перемещать, поэтому каждому разработчику, работающему над приложением, будет необходимо создать такую среду локально.

Используйте ту же версию Python, что выбрана для сайта (в файле runtime.txt или на странице настройки сайта).

Для Python 2.7:

    c:\python27\python.exe -m virtualenv env

Для Python 3.4:

    c:\python34\python.exe -m venv env

Установите все внешние пакеты, необходимые для этого приложения. Чтобы установить пакеты в своей виртуальной среде, вы можете использовать файл requirements.txt в корне репозитория:

    env\scripts\pip install -r requirements.txt

### Запуск страницы с помощью сервера разработки

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

С помощью pip можно установить дополнительные пакеты.  Например, чтобы установить пакет SDK для Azure для Python, который предоставляет доступ к службе хранилища Azure, Service Bus, а также другим службам Azure, введите:

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


## Разработка веб-сайтов - Mac/Linux - командная строка

### Клонирование репозитория

Сначала клонируйте репозиторий, используя URL-адрес на портале Azure, а затем добавьте репозиторий Azure в качестве удаленного.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Создание виртуальной среды

Давайте создадим новую виртуальную среду для разработки (не добавляйте ее в репозиторий).  Виртуальные среды на языке Python нельзя перемещать, поэтому каждому разработчику, работающему над приложением, будет необходимо создать такую среду локально.

Используйте ту же версию Python, что выбрана для сайта (в файле runtime.txt или на странице настройки сайта).

Для Python 2.7:

    python -m virtualenv env

Для Python 3.4:

    python -m venv env

Установите все внешние пакеты, необходимые для этого приложения. Чтобы установить пакеты в своей виртуальной среде, вы можете использовать файл requirements.txt в корне репозитория:

    env/bin/pip install -r requirements.txt

### Запуск страницы с помощью сервера разработки

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

С помощью pip можно установить дополнительные пакеты.  Например, чтобы установить пакет SDK для Azure для Python, который предоставляет доступ к службе хранилища Azure, Service Bus, а также другим службам Azure, введите:

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


## Troubleshooting - Deployment

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


## Устранение неполадок - установка пакета

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


## Устранение неполадок - виртуальная среда

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## Дальнейшие действия

Используйте следующие ссылки, чтобы узнать больше о средствах Flask и Python для Visual Studio. 
 
- [Документация по Flask][]
- [Документация по средствам Python для Visual Studio][]

Дополнительную информацию об использовании табличного хранилища Azure и MongoDB см. в следующих статьях:

- [Использование Flask и MongoDB в Azure с помощью инструментов Python 2.1 для Visual Studio][]
- [Использование Flask и табличного хранилища Azure в Azure с помощью инструментов Python 2.1 для Visual Studio][]


<!--Link references-->
[Использование Flask и MongoDB в Azure с помощью инструментов Python 2.1 для Visual Studio]: web-sites-python-ptvs-flask-table-storage.md
[Использование Flask и табличного хранилища Azure в Azure с помощью инструментов Python 2.1 для Visual Studio]: web-sites-python-ptvs-flask-mongodb.md

<!--External Link references-->
[пакет SDK для Azure для Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[пакет SDK для Azure для Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git для Windows]: http://msysgit.github.io/
[GitHub для Windows]: https://windows.github.com/
[Средства Python для Visual Studio]: http://aka.ms/ptvs
[Средства Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[средства Python 2.1 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Visual Studio]: http://www.visualstudio.com/
[Документация по PTVS]: http://pytools.codeplex.com/documentation
[документации по PTVS]: http://pytools.codeplex.com/documentation
[Документация по средствам Python для Visual Studio]: http://pytools.codeplex.com/documentation 
[Документация по Flask]: http://flask.pocoo.org/ 

<!--HONumber=52-->