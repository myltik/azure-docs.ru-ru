---
title: "Создание веб-приложений на Python с помощью Bottle в Azure"
description: "Учебник, в котором вы ознакомитесь с запуском веб-приложения Python в веб-приложениях службы приложений Azure."
services: app-service\web
documentationcenter: python
tags: python
author: huguesv
manager: wpickett
editor: 
ms.assetid: 84f043b8-9d05-479f-a9d0-d0d9a32a0bb9
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: f7b54a1a6266d6728ac81e0b709febb1d6f00f9f


---
# <a name="creating-web-apps-with-bottle-in-azure"></a>Создание веб-приложений с помощью Bottle в Azure
В этом учебнике описывается, как начать работу с языком Python в веб-приложениях службы приложений Azure. Служба веб-приложений предоставляет ограниченное бесплатное размещение приложений, а также возможности их быстрого развертывания и использования языка Python. По мере роста приложения его можно перевести на платное размещение и интегрировать во все другие службы Azure.

Вы создадите веб-приложение с использованием веб-платформы Bottle (ознакомьтесь с другими версиями этого руководства для платформ [Django](web-sites-python-create-deploy-django-app.md) и [Flask](web-sites-python-create-deploy-flask-app.md)). Вы можете создать веб-приложение из Azure Marketplace, настроить развертывание Git и локально клонировать репозиторий. Затем вы сможете запускать веб-приложение локально, вносить изменения, фиксировать и отправлять их в [веб-приложения службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714). В этом учебнике показано, как выполнить вышеуказанные действия в Windows, Mac или Linux.

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Если вы хотите приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](https://azure.microsoft.com/try/app-service/), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.
> 
> 

## <a name="prerequisites"></a>Предварительные требования
* Windows, Mac или Linux
* Python 2.7 или 3.4
* setuptools, pip, virtualenv (только для Python 2.7)
* Git
* [инструменты Python 2.2 для Visual Studio][инструменты Python 2.2 для Visual Studio] (PTVS) (Примечание. Необязательный компонент.)

**Примечание.** Публикация TFS в настоящее время для проектов Python не поддерживается.

### <a name="windows"></a>Windows
Если у вас еще не установлен Python 2.7 или 3.4 (32-разрядная версия), рекомендуется сначала установить [пакет SDK для Azure для Python 2.7] или [пакет SDK для Azure для Python 3.4] с помощью установщика веб-платформы. При этом выполняется установка 32-разрядной версии Python (для установки на хост-компьютерах Azure), setuptools, pip, virtualenv, и т. д. Python также можно скачать с веб-сайта [python.org].

Для Git рекомендуется использовать [Git для Windows] или [GitHub для Windows]. При использовании Visual Studio можно использовать интегрированную поддержку Git.

Мы также рекомендуем установить [инструменты Python 2.2 для Visual Studio]. Это необязательно, но, если у вас есть [Visual Studio], например бесплатная версия Visual Studio Community 2013 или Visual Studio Express 2013 для Web, вы получите отличную интегрированную среду разработки Python.

### <a name="maclinux"></a>Mac/Linux
У вас должны быть установлены Python и Git. Убедитесь, что у вас установлена версия Python 2.7 или 3.4.

## <a name="web-app-creation-on-the-azure-portal"></a>Создание веб-приложения на портале Azure
Первым делом нужно создать веб-приложение на [портале Azure](https://portal.azure.com).  

1. Войдите на портал Azure и нажмите кнопку **СОЗДАТЬ** в нижнем левом углу. 
2. В поле поиска введите "python".
3. В результатах поиска выберите **Bottle**, затем нажмите кнопку **Создать**.
4. Настройте новое приложение Bottle, например создайте новый план службы приложений и новую группу ресурсов для него. Затем щелкните **Создать**.
5. Настройте публикацию Git для вновь созданного веб-приложения, следуя инструкциям из статьи [Развертывание локального репозитория Git в службе приложений Azure](app-service-deploy-local-git.md).

## <a name="application-overview"></a>Обзор приложений
### <a name="git-repository-contents"></a>Содержимое репозитория Git
Ниже приведен обзор файлов, которые можно найти в исходном репозитории Git. Мы клонируем его в следующем разделе.

    \routes.py
    \static\content\
    \static\fonts\
    \static\scripts\
    \views\about.tpl
    \views\contact.tpl
    \views\index.tpl
    \views\layout.tpl

Основные источники для приложения. Состоит из 3 страниц (index, about, contact) с макетом главной страницы.  Использует такое статическое содержимое и сценарии, как bootstrap, jquery, modernizr и respond.

    \app.py

Поддержка локального сервера разработки. Используется для локального запуска приложения.

    \BottleWebProject.pyproj
    \BottleWebProject.sln

Файлы проекта для использования со [средствами Python для Visual Studio].

    \ptvs_virtualenv_proxy.py

Прокси-сервер IIS для виртуальных сред и поддержки удаленной отладки PTVS.

    \requirements.txt

Внешние пакеты, необходимые для этого приложения. Сценарий развертывания установит пакеты, указанные в этом файле, с помощью pip.

    \web.2.7.config
    \web.3.4.config

Файлы конфигурации IIS. Сценарий развертывания использует соответствующий web.x.y.config и скопирует его с именем web.config.

### <a name="optional-files---customizing-deployment"></a>Дополнительные файлы — настройка развертывания
[!INCLUDE [web-sites-python-customizing-deployment](../../includes/web-sites-python-customizing-deployment.md)]

### <a name="optional-files---python-runtime"></a>Дополнительные файлы — среда выполнения Python
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### <a name="additional-files-on-server"></a>Дополнительные файлы на сервере
Некоторые файлы размещены на сервере, но не добавляются в репозиторий git. Такие файлы создаются с помощью сценария развертывания.

    \web.config

Файл конфигурации IIS. Создается из файла web.x.y.config при каждом развертывании.

    \env\

Виртуальная среда Python. Создается при развертывании, если совместимая виртуальная среда еще не создана для веб-приложения.  Пакеты, указанные в файле requirements.txt, устанавливаются с помощью pip. Однако если они уже установлены, pip не будет выполнять установку.

В следующих 3 разделах описывается разработка веб-приложения в 3 разных средах:

* Windows, со средствами Python для Visual Studio
* Windows с командной строкой;
* Mac и Linux с командной строкой.

## <a name="web-app-development---windows---python-tools-for-visual-studio"></a>Развертывание веб-приложений — Windows — инструменты Python для Visual Studio
### <a name="clone-the-repository"></a>Клонирование репозитория
Сначала клонируйте репозиторий, используя URL-адрес на портале Azure. Дополнительные сведения см. в статье [Развертывание локального репозитория Git в службе приложений Azure](app-service-deploy-local-git.md).

Откройте файл решения (SLN-файл), который содержится в корневой папке репозитория.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-solution-bottle.png)

### <a name="create-virtual-environment"></a>Создание виртуальной среды
Теперь мы создадим виртуальную среду для локальной разработки веб-сайта. Щелкните правой кнопкой мыши **Python Environments** (Среды Python) и выберите **Add Virtual Environment...** (Добавить виртуальную среду...).

* Убедитесь, что имя среды — `env`.
* Выберите базовый интерпретатор. Убедитесь, что используется та же версия Python, которая выбрана для веб-приложения (в файле runtime.txt или в колонке **Параметры приложения** веб-приложения на портале Azure).
* Убедитесь, что установлен флажок для скачивания и установки пакетов.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-add-virtual-env-27.png)

Щелкните **Создать**. Таким образом будет создана виртуальная среда и установлены зависимости из файла requirements.txt.

### <a name="run-using-development-server"></a>Запуск приложения на сервере разработки
Нажмите клавишу F5, чтобы начать отладку, и браузер автоматически откроет страницу, запущенную локально.

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

Можно установить точки останова в источниках, использовать окна контрольных значений и т. д. Дополнительные сведения о различных функциях см. в [документации по средствам Python для Visual Studio].

### <a name="make-changes"></a>Внесение изменений
Теперь вы можете поэкспериментировать, внося изменения в исходные коды и/или шаблоны приложений.

После тестирования изменений их необходимо сохранить в репозитории Git:

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-commit-bottle.png)

### <a name="install-more-packages"></a>Установка дополнительных пакетов
Зависимости приложений могут не ограничиваться Python и Bottle.

С помощью pip можно установить дополнительные пакеты. Чтобы установить пакет, щелкните правой кнопкой мыши в виртуальной среде и выберите **Установить пакет Python**.

Например, чтобы установить пакет SDK для Azure для Python, который предоставляет доступ к службе хранилища Azure, служебной шине и другим службам Azure, введите `azure`:

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-install-package-dialog.png)

Щелкните правой кнопкой мыши в виртуальной среде и выберите **Создать файл requirements.txt** , чтобы обновить файл requirements.txt.

Затем сохраните внесенные в этот файл изменения в репозитории Git.

### <a name="deploy-to-azure"></a>Развернуть в Azure
Чтобы запустить развертывание, щелкните **Синхронизировать** или **Отправить**. При синхронизации выполняется как принудительная отправка, так и применение по запросу.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-git-push.png)

Первое развертывание может занять некоторое время, необходимое для создания виртуальной среды, установку пакетов и т. д.

В Visual Studio не отображается ход выполнения развертывания. Сведения о том, как можно просмотреть результат, см. в разделе [Устранение неполадок — развертывание](#troubleshooting-deployment).

Перейдите по URL-адресу Azure, чтобы просмотреть внесенные изменения.

## <a name="web-app-development---windows---command-line"></a>Разработка веб-приложения — Windows — командная строка
### <a name="clone-the-repository"></a>Клонирование репозитория
Сначала клонируйте репозиторий, используя URL-адрес на портале Azure, а затем добавьте репозиторий Azure в качестве удаленного. Дополнительные сведения см. в статье [Развертывание локального репозитория Git в службе приложений Azure](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### <a name="create-virtual-environment"></a>Создание виртуальной среды
Давайте создадим новую виртуальную среду для разработки (не добавляйте ее в репозиторий). Виртуальные среды на языке Python нельзя перемещать, поэтому каждому разработчику, работающему над приложением, будет необходимо создать такую среду локально.

Убедитесь, что используется та же версия Python, которая была выбрана для вашего веб-приложения (в файле runtime.txt или в колонке "Параметры приложения" веб-приложения на портале Azure).

Для Python 2.7:

    c:\python27\python.exe -m virtualenv env

Для Python 3.4:

    c:\python34\python.exe -m venv env

Установите все внешние пакеты, необходимые для этого приложения. Чтобы установить пакеты в своей виртуальной среде, вы можете использовать файл requirements.txt в корне репозитория:

    env\scripts\pip install -r requirements.txt

### <a name="run-using-development-server"></a>Запуск приложения на сервере разработки
Вы можете запустить приложение на сервере разработки с помощью следующей команды:

    env\scripts\python app.py

В консоли отобразится URL-адрес и порт, который прослушивает сервер:

![](./media/web-sites-python-create-deploy-bottle-app/windows-run-local-bottle.png)

Далее откройте браузер и введите этот URL-адрес.

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

### <a name="make-changes"></a>Внесение изменений
Теперь вы можете поэкспериментировать, внося изменения в исходные коды и/или шаблоны приложений.

После тестирования изменений их необходимо сохранить в репозитории Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Установка дополнительных пакетов
Зависимости приложений могут не ограничиваться Python и Bottle.

С помощью pip можно установить дополнительные пакеты. Например, чтобы установить пакет SDK для Azure для Python, который предоставляет доступ к службе хранилища Azure, служебной шине, а также другим службам Azure, введите:

    env\scripts\pip install azure

Обязательно обновите файл requirements.txt:

    env\scripts\pip freeze > requirements.txt

Примените изменения:

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Развертывание в Azure
Чтобы запустить развертывание, принудительно отправьте эти изменения в Azure:

    git push azure master

Отобразятся выходные данные сценария развертывания, а также создание виртуальной среды, установку пакетов, и создание файла web.config.

Перейдите по URL-адресу Azure, чтобы просмотреть внесенные изменения.

## <a name="web-app-development---maclinux---command-line"></a>Разработка веб-приложения — Mac/Linux — командная строка
### <a name="clone-the-repository"></a>Клонирование репозитория
Сначала клонируйте репозиторий, используя URL-адрес на портале Azure, а затем добавьте репозиторий Azure в качестве удаленного. Дополнительные сведения см. в статье [Развертывание локального репозитория Git в службе приложений Azure](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### <a name="create-virtual-environment"></a>Создание виртуальной среды
Давайте создадим новую виртуальную среду для разработки (не добавляйте ее в репозиторий). Виртуальные среды на языке Python нельзя перемещать, поэтому каждому разработчику, работающему над приложением, будет необходимо создать такую среду локально.

Убедитесь, что используется та же версия Python, которая выбрана для веб-приложения (в файле runtime.txt или в колонке "Параметры приложения" веб-приложения на портале Azure).

Для Python 2.7:

    python -m virtualenv env

Для Python 3.4:

    python -m venv env
или pyvenv env

Установите все внешние пакеты, необходимые для этого приложения. Чтобы установить пакеты в своей виртуальной среде, вы можете использовать файл requirements.txt в корне репозитория:

    env/bin/pip install -r requirements.txt

### <a name="run-using-development-server"></a>Запуск приложения на сервере разработки
Вы можете запустить приложение на сервере разработки с помощью следующей команды:

    env/bin/python app.py

В консоли отобразится URL-адрес и порт, который прослушивает сервер:

![](./media/web-sites-python-create-deploy-bottle-app/mac-run-local-bottle.png)

Далее откройте браузер и введите этот URL-адрес.

![](./media/web-sites-python-create-deploy-bottle-app/mac-browser-bottle.png)

### <a name="make-changes"></a>Внесение изменений
Теперь вы можете поэкспериментировать, внося изменения в исходные коды и/или шаблоны приложений.

После тестирования изменений их необходимо сохранить в репозитории Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Установка дополнительных пакетов
Зависимости приложений могут не ограничиваться Python и Bottle.

С помощью pip можно установить дополнительные пакеты. Например, чтобы установить пакет SDK для Azure для Python, который предоставляет доступ к службе хранилища Azure, служебной шине, а также другим службам Azure, введите:

    env/bin/pip install azure

Обязательно обновите файл requirements.txt:

    env/bin/pip freeze > requirements.txt

Примените изменения:

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Развертывание в Azure
Чтобы запустить развертывание, принудительно отправьте эти изменения в Azure:

    git push azure master

Отобразятся выходные данные сценария развертывания, а также создание виртуальной среды, установку пакетов, и создание файла web.config.

Перейдите по URL-адресу Azure, чтобы просмотреть внесенные изменения.

## <a name="troubleshooting---package-installation"></a>Устранение неполадок — установка пакета
[!INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]

## <a name="troubleshooting---virtual-environment"></a>Устранение неполадок — виртуальная среда
[!INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>Дальнейшие действия
Используйте следующие ссылки, чтобы узнать больше о средствах Bottle и Python для Visual Studio. 

* [Документация по работе с Bottle]
* [документации по средствам Python для Visual Studio]

Дополнительную информацию об использовании табличного хранилища Azure и MongoDB см. в следующих статьях:

* [Использование Bottle и MongoDB в Azure с помощью инструментов Python для Visual Studio]
* [Использование Bottle и табличного хранилища Azure в Azure с помощью инструментов Python для Visual Studio]

## <a name="whats-changed"></a>Изменения
* Руководство по переходу от веб-сайтов к службе приложений см. в статье [Служба приложений Azure и существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

<!--Link references-->
[Использование Bottle и MongoDB в Azure с помощью инструментов Python для Visual Studio]: web-sites-python-ptvs-bottle-table-storage.md
[Использование Bottle и табличного хранилища Azure в Azure с помощью инструментов Python для Visual Studio]: web-sites-python-ptvs-bottle-table-storage.md

<!--External Link references-->
[пакет SDK для Azure для Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[пакет SDK для Azure для Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git для Windows]: http://msysgit.github.io/
[GitHub для Windows]: https://windows.github.com/
[средствами Python для Visual Studio]: http://aka.ms/ptvs
[инструменты Python 2.2 для Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Visual Studio]: http://www.visualstudio.com/
[документации по средствам Python для Visual Studio]: http://aka.ms/ptvsdocs 
[Документация по работе с Bottle]: http://bottlepy.org/docs/dev/index.html




<!--HONumber=Feb17_HO3-->


