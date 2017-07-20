---
title: "Веб-приложение Python на Django на виртуальной машине Azure под управлением Linux | Документация Майкрософт"
description: "Узнайте, как разместить веб-приложение на основе Django в Azure с помощью виртуальной машины Linux."
services: virtual-machines-linux
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-resource-manager
ms.assetid: 00ad4c2c-4316-4f9a-913f-f7f49b158db7
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 6e2ab8c7da7496d0e2b567a4bdc9341adcf01552
ms.contentlocale: ru-ru
ms.lasthandoff: 06/22/2017


---
# <a name="django-hello-world-web-app-on-a-linux-vm"></a>Веб-приложение Hello World на Django на виртуальной машине Linux
> [!div class="op_single_selector"]
> * [Windows](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Mac/Linux](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

В этом руководстве описывается размещение веб-сайта на основе Django в Linux на виртуальных машинах Azure. Для работы с этим руководством опыт работы с Azure не требуется. По завершении работы с этим руководством у вас будет готовое приложение на базе Django, выполняющееся в облаке.

Ниже перечислено, что вы можете узнать.

* Настраивать виртуальную машину Azure для размещения Django. Хотя в этом руководстве описываются действия для **Linux**, эти же шаги вы можете выполнить для виртуальных машин Windows Server, размещенных в Azure. 
* Создайте приложение Django в Linux.

В этом руководстве рассматривается создание базового веб-приложения Hello World. Приложение будет размещаться на виртуальной машине Azure.

На снимке экрана ниже показано готовое приложение:

![Окно браузера, в котором отображается страница Hello World в Azure](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

[!INCLUDE [create-account-and-vms-note](../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Создание и настройка виртуальной машины Azure для размещения приложения Django

1. Чтобы создать виртуальную машину Azure с дистрибутивом Ubuntu Server 14.04 LTS, ознакомьтесь со статьей [Создание виртуальной машины Linux с помощью портала Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Кроме того, можно выбрать проверку пароля вместо открытого ключа SSH.
2. Дополнительные сведения об изменении группы безопасности сети, чтобы разрешить входящий трафик HTTP для порта 80, см. в статье [Создание групп безопасности сети с помощью портала Azure](../../virtual-network/virtual-networks-create-nsg-arm-pportal.md).
3. (Необязательно.) По умолчанию у новой виртуальной машины нет полного доменного имени (FQDN).  Дополнительные сведения о создании виртуальной машины с полным доменным именем см. в статье [Создание полного доменного имени на портале Azure для виртуальной машины Windows](../windows/portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Этот шаг не требуется для изучения этого руководства.

## <a id="setup"> </a>Настройка среды разработки
> [!NOTE]
> Если нужно использовать клиентские библиотеки или установить Python, см. статью [Установка Python и пакета SDK](../../python-how-to-install.md).

Виртуальная машина Linux Ubuntu уже поставляется с предварительно установленной программой Python 2.7, но в ней отсутствует Apache или Django. Чтобы подключиться к своей виртуальной машине и установить Apache и Django, сделайте следующее:

1. Откройте новое окно терминала.
2. Введите следующую команду для подключения к виртуальной машине Azure. Если полное доменное имя не было создано, можно подключиться с помощью общедоступного IP-адреса, который отображается в сводке по виртуальной машине на портале Azure.
   
       $ ssh yourusername@yourVmUrl
3. Для установки Django введите следующие команды:
   
       $ sudo apt-get install python-setuptools python-pip
       $ sudo pip install django
4. Для установки Apache с mod-wsgi введите следующую команду:
   
       $ sudo apt-get install apache2 libapache2-mod-wsgi

## <a name="create-a-new-django-app"></a>Создание приложения Django
1. Чтобы войти в виртуальною машину с помощью SSH, откройте окно терминала, использованное в предыдущем разделе.
2. Введите следующие команды для создания нового проекта Django:
   
       $ cd /var/www
       $ sudo django-admin.py startproject helloworld
   
   Скрипт `django-admin.py` создает базовую структуру для веб-сайтов на основе Django:
   
   * `helloworld/manage.py` поможет вам начать и остановить размещение веб-сайта на основе Django.
   * `helloworld/helloworld/settings.py` содержит настройки Django для приложения.
   * `helloworld/helloworld/urls.py` содержит код сопоставления между каждым URL-адресом и его представлением.
3. В каталоге /var/www/helloworld/helloworld создайте файл с именем views.py. Он содержит представление, которое осуществляет отрисовку страницы hello world. В редакторе кода введите следующие команды:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Замените содержимое файла urls.py на команды ниже:
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-apache"></a>Настройка Apache
1. Создайте файл конфигурации виртуального узла Apache в папке /etc/apache2/sites-available/helloworld.conf. Введите следующее содержимое для переменных. Замените *yourVmName* фактическим именем используемого компьютера (например, *pyubuntu*).
   
       <VirtualHost *:80>
       ServerName yourVmName
       </VirtualHost>
       WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
       WSGIPythonPath /var/www/helloworld
2. Включите сайт, выполнив следующую команду:
   
       $ sudo a2ensite helloworld
3. Чтобы перезапустить Apache, используйте следующую команду:
   
       $ sudo service apache2 reload
4. Загрузите веб-страницу в браузере:
   
   ![Окно браузера, в котором отображается страница Hello World в Azure](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

## <a name="shut-down-your-azure-virtual-machine"></a>Завершение работы виртуальной машины Azure
По завершении работы с этим руководством мы рекомендуем завершить работу виртуальной машины Azure (созданной для работы с этим руководством) или же удалить ее. Это позволит освободить ресурсы для работы с другими руководствами, а также избежать расходов за использование Azure.


