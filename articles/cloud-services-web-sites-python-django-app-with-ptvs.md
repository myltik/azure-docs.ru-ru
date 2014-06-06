<properties linkid="develop-python-tutorials-django-with-python-tools-for-visual-studio" urlDisplayName="Django со средствами Python 2.0 для Visual Studio" pageTitle="Создание приложений Django с помощью средств Python для Visual Studio 2.0" metaKeywords="" description="Узнайте, как с помощью средств Python для Visual Studio создать приложение Django, которое сохраняет данные в экземпляре базы данных SQL или MySQL и может развертываться на веб-сайте или в облачной службе." metaCanonical="" services="web-sites,cloud-services" documentationCenter="Python" title="Создание приложений Django с помощью средств Python 2.0 для Visual Studio" authors=""  solutions="" writer="" manager="" editor=""  />





# Создание приложений Django с помощью средств Python 2.0 для Visual Studio

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике мы используем средства Python 2.0 для Visual Studio, чтобы создать простое приложение Django. Приложение позволит пользователям голосовать в опросах. Сначала мы используем локальную базу данных sqlite3, а затем перейдем к базе данных SQL Server или MySQL в Azure. Мы покажем, как включить интерфейс администрирования Django и использовать его для добавления опросов в нашу базу данных. Мы используем оболочку Django, интегрированную в Visual Studio.  Наконец, мы развернем приложение на веб-сайте Azure и в облачной службе Azure.</p>
<p>Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/python/videos/django-tutorial-180x120.png') !important;" href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">33:08</span></div>
</div>

Этот учебник посвящен использованию средств Python для Visual Studio и Azure. Дополнительные сведения о Django и приложении для опросов, созданном в этом учебнике, см. на веб-сайте [https://www.djangoproject.com/](https://www.djangoproject.com/).

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Требования
Для работы с учебником вам потребуются:

- [Средства Python 2.0 для Visual Studio](http://pytools.codeplex.com)
- [Python 2.7 (32-разрядная версия)](http://www.python.org/download/)
- Visual Studio и пакет SDK для Azure:
  - VS 2010 Pro или более поздней версии с пакетом Azure SDK 2.1
  - VS 2012 Pro или более поздней версии с пакетом Azure SDK 2.1, 2.2 или более поздней версии
  - VS 2013 Pro или более поздней версии с пакетом Azure SDK 2.2 или более поздней версии
  - Бесплатная интегрированная оболочка VS 2013. **Пакет SDK для Azure не поддерживает интегрированную оболочку**. С помощью бесплатной интегрированной оболочки можно локально разрабатывать, отлаживать и запускать приложения Django, но нельзя публиковать их на веб-сайтах и в облачных службах Azure с использованием Visual Studio.

Чтобы установить пакет SDK для Azure, используйте установщик веб-платформы. При этом установятся пакет SDK, эмулятор и средства Azure для Visual Studio. В установщике веб-платформы выполните поиск **Azure SDK для .NET** и выберите одну из поддерживаемых версий пакета SDK для вашей версии Visual Studio.

**Примечание.** Для успешного запуска приложения с использованием веб-сайта или облачной службы Azure необходимо использовать официальный дистрибутив CPython 2.7 с сайта [python.org](http://www.python.org/download/). Другие дистрибутивы могут работать, но официально не поддерживаются.

## Загрузка существующего проекта

Если вы хотите пропустить этот раздел в учебнике, можете [загрузить исходный код для этого проекта](http://download-codeplex.sec.s-msft.com/Download?ProjectName=pytools&DownloadId=783376).

База данных sqlite3 уже создана с использованием следующих учетных данных для суперпользователя:

```
Username: tutorial
Password: azure
```

В загрузку НЕ включена виртуальная среда. Ее необходимо создать, выполнив шаги, описанные в разделе [Создание виртуальной среды](#creating-a-virtual-environment). После этого проект будет готов к отладке (см. раздел [Отладка](#debugging)).

## Создание проекта

Средства Python для Visual Studio поддерживают виртуальные среды Python.  Мы создадим проект django и используем виртуальную среду для установки зависимостей.  Это рекомендуемый способ настройки проектов, которые публикуются на веб-сайтах или в облачных службах Azure.

1. Откройте Visual Studio, выберите "Файл"/"Новый проект", приложение Django с именем **tutorial**.

	![Новый проект](./media/cloud-services-python-create-deploy-django-app/django-tutorial-001-new-project.png)


**Примечание.** В обозревателе решений в разделе ссылок вы увидите узел для Django 1.4. Он используется при развертывании облачной службы Azure для установки Python и Django на целевом компьютере. Не удаляйте ссылку на Django 1.4 из ссылок. Поскольку мы используем виртуальную среду и устанавливаем в ней наш собственный пакет Django, будет использоваться пакет Django, установленный в нашей виртуальной среде.

##<a name="creating-a-virtual-environment"></a>Создание виртуальной среды

Мы используем виртуальную среду для установки зависимостей. Это хорошая практика для любого приложения Python и это необходимо при публикации в Azure.

1. Создайте новую виртуальную среду.  В обозревателе решений щелкните правой кнопкой мыши **Среды Python** и выберите **Добавить виртуальную среду**.

	![Добавление виртуальной среды](./media/cloud-services-python-create-deploy-django-app/django-tutorial-002-add-virtual-env.png)

1. Выберите Python 2.7 в качестве базового интерпретатора Python и примите имя по умолчанию **env**. PTVS установит pip и/или virtualenv, если они еще не установлены.

1. Щелкните правой кнопкой мыши **env** и **Установить пакет Python**: **django**.

	![Установка Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-003-install-django.png)

1. Django содержит большое количество файлов, поэтому установка займет некоторое время. Ход выполнения можно посмотреть в окне вывода.

	![Окно вывода при установке Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-004-install-django-output.png)

	**Примечание.** В редких случаях в окне вывода появляется ошибка. В этом случае проверьте, связана ли ошибка с очисткой. Иногда происходит сбой очистки, но установка по-прежнему успешно выполняется (прокрутите вверх окно вывода, чтобы убедиться в этом). Это обусловлено тем, что PTVS блокирует вновь созданные временные файлы и папки, чтобы предотвратить их удаление на шаге очистки pip.

1. Щелкните правой кнопкой мыши **env** и **Установить пакет Python**: **pytz** (необязательно, но рекомендуется, используется в django для поддержки часового пояса).

## Проверка виртуальной среды

1. Убедимся, что все установлено правильно. Запустите веб-сайт, используя клавишу **F5** или сочетание клавиш **CTRL+F5**. При этом запустится сервер разработки django, который запустит ваш веб-браузер. Вы должны увидеть следующую страницу:

	![Веб-браузер Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-004b-itworked.png)

## Создание приложения для опросов

В этом разделе мы добавим приложение для обработки голосования в опросах.

В проекте Django может быть несколько приложений. В этом учебнике наш проект называется "tutorial" и соответствует проекту Visual Studio. Добавляется приложение с именем **polls** и создается папка в узле нашего проекта.

1. Выберите **узел проекта**, **Добавить** -> **Приложение Django** и приложение с именем **polls**. При этом создается папка для приложения со стандартным кодом для часто используемых файлов приложения.

	![Добавление приложения Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-005-add-django-app.png)

1. В элементе **tutorial/settings.py** добавьте в **INSTALLED\_APPS** следующее:

		'polls',

1. В **INSTALLED\_APPS** раскомментируйте следующее:

		'django.contrib.admin',

1. Замените **tutorial/urls.py** следующим кодом:

        from django.conf.urls import patterns, include, url

        from django.contrib import admin
        admin.autodiscover()

        urlpatterns = patterns('',
            url(r'^', include('polls.urls', namespace="polls")),
            url(r'^admin/', include(admin.site.urls)),
        )

1. Замените **polls/models.py** следующим кодом:

        from django.db import models
		
        class Poll(models.Model):
            question = models.CharField(max_length=200)
            pub_date = models.DateTimeField('date published')
			
            def __unicode__(self):
                return self.question
		
        class Choice(models.Model):
            poll = models.ForeignKey(Poll)
            choice_text = models.CharField(max_length=200)
            votes = models.IntegerField(default=0)
			
            def __unicode__(self):
                return self.choice_text

1. Замените **polls/views.py** следующим кодом:

		from django.shortcuts import get_object_or_404, render
		from django.http import HttpResponseRedirect
		from django.core.urlresolvers import reverse
		from polls.models import Choice, Poll
		
		def vote(request, poll_id):
		    p = get_object_or_404(Poll, pk=poll_id)
		    try:
		        selected_choice = p.choice_set.get(pk=request.POST['choice'])
		    except (KeyError, Choice.DoesNotExist):
		        # Redisplay the poll voting form.
		        return render(request, 'polls/detail.html', {
		            'poll': p,
		            'error_message': "You didn't select a choice.",
		        })
		    else:
		        selected_choice.votes += 1
		        selected_choice.save()
		        # Always return an HttpResponseRedirect after successfully dealing
		        # with POST data. This prevents data from being posted twice if a
		        # user hits the Back button.
		        return HttpResponseRedirect(reverse('polls:results', args=(p.id,)))
		
1. Добавьте новый файл Python **polls/urls.py** со следующим кодом:

	    from django.conf.urls import patterns, url
	    from django.views.generic import DetailView, ListView
	    from polls.models import Poll
	
	    urlpatterns = patterns('',
	        url(r'^$',
	            ListView.as_view(
	                queryset=Poll.objects.order_by('-pub_date')[:5],
	                context_object_name='latest_poll_list',
	                template_name='polls/index.html'),
	            name='index'),
	        url(r'^(?P<pk>\d+)/$',
	            DetailView.as_view(
	                model=Poll,
	                template_name='polls/detail.html'),
	            name='detail'),
	        url(r'^(?P<pk>\d+)/results/$',
	            DetailView.as_view(
	                model=Poll,
	                template_name='polls/results.html'),
	            name='results'),
	        url(r'^(?P<poll_id>\d+)/vote/$', 'polls.views.vote', name='vote'),
	    )
	
1. Создайте новый элемент **polls/admin.py** со следующим кодом:

		from django.contrib import admin
		from polls.models import Choice, Poll
		
		class ChoiceInline(admin.TabularInline):
		    model = Choice
		    extra = 3
		
		class PollAdmin(admin.ModelAdmin):
		    fieldsets = [
		        (None,               {'fields': ['question']}),
		        ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
		    ]
		    inlines = [ChoiceInline]
		    list_display = ('question', 'pub_date')
		    list_filter = ['pub_date']
		    search_fields = ['question']
		    date_hierarchy = 'pub_date'
		
		admin.site.register(Poll, PollAdmin)

1. В папке **polls/templates** создайте новую папку с именем **polls**.

1. Переместите файл **polls/templates/index.html** в папку **polls/templates/polls** с помощью перетаскивания или вырезания и вставки.

1. Замените **polls/templates/polls/index.html** следующей разметкой:

        <html>
        <head></head>
        <body>
        {% if latest_poll_list %}
            <ul>
            {% for poll in latest_poll_list %}
                <li><a href="{% url 'polls:detail' poll.id %}">{{ poll.question }}</a></li>
            {% endfor %}
            </ul>
        {% else %}
            <p>Нет доступных опросов.</p>
        {% endif %}
        </body>
        </html>

1. Создайте новый HTML-шаблон Django **polls/templates/polls/detail.html** со следующим кодом:

        <html>
        <head></head>
        <body>
        <h1>{{ poll.question }}</h1>
        {% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
        <form action="{% url 'polls:vote' poll.id %}" method="post">
        {% csrf_token %}
        {% for choice in poll.choice_set.all %}
            <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
            <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br />
        {% endfor %}
        <input type="submit" value="Vote" />
        </form>
        </body>
        </html>

1. Создайте новый HTML-шаблон Django **polls/templates/polls/results.html** со следующим кодом:

        <html>
        <head></head>
        <body>
        <h1>{{ poll.question }}</h1>
        <ul>
        {% for choice in poll.choice_set.all %}
            <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
        {% endfor %}
        </ul>
        <a href="{% url 'polls:detail' poll.id %}">Проголосовать еще раз?</a>
        </body>
        </html>

1. Теперь в вашем проекте имеются следующие файлы:

	![Обозреватель решений](./media/cloud-services-python-create-deploy-django-app/django-tutorial-006-solution-explorer.png)

## Локальное создание базы данных sqlite3

Наше веб-приложение почти готово к использованию, но сначала необходимо настроить базу данных.  Чтобы протестировать веб-сайт локально, мы создадим базу данных sqlite3.  Это очень упрощенная база данных, которая не требует дополнительной установки.  Файл базы данных будет создан в папке проекта.

1. В **tutorial/settings.py** добавьте следующий параметр импорта в начало файла:

        from os import path

1. В начале файла после импорта добавьте следующее определение:

		PROJECT_ROOT = path.dirname(path.abspath(path.dirname(__file__)))

1. Замените раздел DATABASES следующим кодом:

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.sqlite3',
                'NAME': path.join(PROJECT_ROOT, 'db.sqlite3'),
                'USER': '',
                'PASSWORD': '',
                'HOST': '',
                'PORT': '',
            }
        }

1. Щелкните правой кнопкой мыши узел проекта и выберите **Django** -> **Django Sync DB**.  Появится окно интерактивного управления Django.  Поскольку базы данных еще не существует, будет предложено создать учетные данные администратора.  Введите имя пользователя и пароль. Адрес электронной почты использовать необязательно.

	![БД синхронизации Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-007-sqlite3.png)

1. Запустите веб-сайт, используя клавишу F5 или сочетание клавиш CTRL+F5. При этом запустится сервер разработки django, который запустит ваш веб-браузер. В корневом URL-адресе веб-сайта отображается индекс опросов, но в базе данных еще ничего нет.

	![Веб-браузер](./media/cloud-services-python-create-deploy-django-app/django-tutorial-008-dev-server.png)

1. Перейдите на веб-страницу **http://localhost:{port}/admin**. Номер порта можно получить из окна консоли сервера разработки. Войдите, используя учетные данные, созданные на предыдущем шаге.

	![Добавление опроса](./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-login.png)

1. Используйте интерфейс администрирования для добавления одного или двух опросов.  Не стоит тратить время, добавляя опросы в локальную базу данных. Мы позже перейдем в облачную базу данных и затем повторно заполним базу данных.

	![Индекс опроса](./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-add-poll.png)

1. Перейдите по адресу **http://localhost:{port}/**.  Вы увидите индекс добавленных опросов.

	![](./media/cloud-services-python-create-deploy-django-app/django-tutorial-010-index.png)

1. Щелкните один из опросов, чтобы перейти на страницу голосования.

	![Сведения об опросе](./media/cloud-services-python-create-deploy-django-app/django-tutorial-011-detail.png)

1. Отправьте ваш голос, после чего вы будете перенаправлены на страницу результатов, на которой должно быть видно, как увеличился счетчик голосов.

	![Результаты опроса](./media/cloud-services-python-create-deploy-django-app/django-tutorial-012-results.png)

## Использование таблиц стилей и других статических файлов

В этом разделе мы обновим внешний вид сайта, чтобы использовать таблицу стилей. Статические файлы, такие как таблицы стилей, обрабатываются по-разному, поэтому важно хранить их в правильном месте.

1. В **tutorial/settings.py** найдите назначение **STATIC_ROOT** и измените его на:

        STATIC_ROOT = path.join(PROJECT_ROOT, 'static').replace('\\','/')

1. В папке **polls** создайте новую папку с именем **static**.

1. В папке **polls/static** создайте новую папку с именем **polls**.

1. В папке **polls/static/polls** создайте новую папку с именем **images**.

1. Добавьте новый файл таблицы стилей **polls/static/polls/style.css** со следующим кодом:

        body {
            color: darkblue;
            background: white url("images/background.jpg");
        }
		
1. Добавьте существующее изображение в папку **polls/static/polls/images** и назовите его **background.jpg**.

1. Теперь в вашем проекте имеются следующие файлы:

	![Обозреватель решений](./media/cloud-services-python-create-deploy-django-app/django-tutorial-013-solution-explorer.png)

1. Измените заголовок всех шаблонов для ссылки на таблицу стилей, используя следующую разметку:

        <head>
        {% load staticfiles %}
        <link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
        </head>

1. Снова запустите веб-сайт.  На страницах индекса, опроса и результатов будет использоваться созданная нами таблица стилей с темно-синим текстом и фоновым изображением.

	![Сведения об опросе](./media/cloud-services-python-create-deploy-django-app/django-tutorial-014-detail-styled.png)

##<a name="debugging"></a>Отладка

В средствах Python для Visual Studio имеется специальная поддержка для отладки шаблонов Django.

1. Откройте **polls/templates/polls/index.html** и с помощью клавиши **F9** поместите точку останова в этой строке:

        {% if latest_poll_list %}

1. Начните отладку с помощью клавиши **F5**. Visual Studio прервет процесс в файле шаблона.

1. Откройте **Окно локальных переменных**, выбрав последовательно **Отладка -> Окна -> Локальные**, и вы увидите переменную **latest\_poll\_list** и ее значение.

1. Для пошаговой отладки можно использовать клавишу **F10**, как в обычном коде Python.  В цикле for можно проверить значение **poll**:

	![Отладка](./media/cloud-services-python-create-deploy-django-app/django-tutorial-015-debugging.png)

## Создание базы данных в Azure

Теперь, когда мы убедились, что наше приложение опроса локально работает, перейдем к использованию базы данных, размещенной в Azure.

В следующих 2 разделах будет показано, как использовать базы данных SQL и MySQL.  Обе являются размещенными службами.

Еще один вариант — создание виртуальной машины и установка сервера базы данных.  Инструкции по настройке MySQL на виртуальной машине Linux Azure см. [здесь](http://www.windowsazure.com/ru-ru/manage/linux/common-tasks/mysql-on-a-linux-vm/).

**Примечание.** Можно использовать базу данных sqlite3 в Azure (только для целей разработки, не рекомендуется использовать ее в рабочей среде). Для развертывания базы данных с приложением django потребуется добавить файл **db.sqlite3** в проект.

### База данных SQL

В этом разделе мы создадим базу данных SQL в Azure, добавим необходимые пакеты в нашу виртуальную среду и изменим параметры для использования новой базы данных.

1. На портале управления Azure выберите **БАЗЫ ДАННЫХ SQL**.

1. Сначала создайте сервер для размещения базы данных.  Выберите **СЕРВЕРЫ** и **ДОБАВИТЬ**.

1. На вкладке **НАСТРОИТЬ** вновь созданного сервера появится ваш текущий IP-адрес клиента. Рядом с ним щелкните **ДОБАВИТЬ В РАЗРЕШЕННЫЕ IP-АДРЕСА**.

	**Примечание.** Иногда Azure не удается правильно определить IP-адрес клиента. Если при синхронизации базы данных появляется сообщение об ошибке, скопируйте IP-адрес из сообщения об ошибке и добавьте его в разрешенные IP-адреса.

1. Далее мы создадим базу данных. На вкладке **БАЗЫ ДАННЫХ** щелкните **ДОБАВИТЬ** на нижней панели.

1. В Visual Studio мы установим пакеты, необходимые для доступа к базам данных SQL Server из Django в нашей виртуальной среде.

1. Щелкните правой кнопкой мыши **env** и **Установить пакет Python**: **pyodbc** с использованием **easy\_install**.

1. Щелкните правой кнопкой мыши **env** и **Установить пакет Python**: **django-pyodbc-azure** с использованием **pip**.

1. Внесите изменения в **tutorial/settings.py** и измените определение **DATABASES** на следующее определение, заменив **NAME**, **USER**, **PASSWORD** и **HOST** значениями, указанными на панели управления ClearDB:

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<database name>',
                'USER': '<user name>@<server name>',
                'PASSWORD': '<user password>',
                'HOST': '<server name>.database.windows.net',
                'PORT': '',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': True,
                },
            }
        }

	Обязательно используйте драйвер, установленный на вашем компьютере.  Из меню или экрана "Пуск" откройте **Администрирование**, **Источники данных ODBC (32-разрядная версия)**. Драйверы указаны на вкладке **Драйверы**.
	
	При запуске на веб-сайте Azure будут работать оба варианта: **SQL Server Native Client 10.0** и **SQL Server Native Client 11.0**.

	При запуске в облачной службе Azure будет работать только вариант **SQL Server Native Client 11.0**.
	
1. Синхронизируйте базу данных и создайте учетные данные администратора, как это было сделано для локальной базы данных sqlite3.

### База данных MySQL

В этом разделе мы создадим базу данных MySQL в Azure, добавим необходимые пакеты в нашу виртуальную среду и изменим параметры для использования новой базы данных.

В магазине Azure вы можете добавить различные службы для своей учетной записи, в том числе базу данных MySQL.  Мы можем создать пробную учетную запись для бесплатного использования и небольшие базы данных, которые будут бесплатными до определенного размера.

1. На портале управления Azure выберите **СОЗДАТЬ** -> **МАГАЗИН** -> **СЛУЖБЫ ПРИЛОЖЕНИЙ** -> **База данных MySQL ClearDB**.  Создайте базу данных с использованием бесплатного плана.

1. Далее мы установим пакет, необходимый для доступа к базам данных MySQL из Django в нашей виртуальной среде.  Щелкните правой кнопкой мыши **env** и **Установить пакет Python**: **mysql-python** с использованием **easy\_install**.

1. Внесите изменения в **tutorial/settings.py** и измените определение **DATABASES** на следующее определение, заменив **NAME**, **USER**, **PASSWORD** и **HOST** значениями, указанными на панели управления ClearDB:

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<database name>',
                'USER': '<user name>',
                'PASSWORD': '<user password>',
                'HOST': '<host url>',
                'PORT': '',
            }
        }

1. Синхронизируйте базу данных и создайте учетные данные администратора, как это было сделано для локальной базы данных sqlite3.

## Использование оболочки Django

1. Щелкните правой кнопкой мыши узел проекта и выберите **Django** -> **Открыть Django Shell**.

1. В этом интерактивном окне мы можем получить доступ к базе данных с помощью наших моделей.  Введите следующий код, чтобы добавить опрос в базу данных:

		from polls.models import Poll, Choice
		from django.utils import timezone

		p = Poll(question="Favorite way to host Django on Azure?", pub_date=timezone.now())
		p.save()

		p.choice_set.create(choice_text='Web Site', votes=0)
		p.choice_set.create(choice_text='Cloud Service', votes=0)
		p.choice_set.create(choice_text='Virtual Machine', votes=0)

	![Добавление опроса в оболочке Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-018-shell-add-poll.png)

1. Статический анализ моделей обеспечивает ограниченное представление полного API-интерфейса.  В интерактивном окне вы получите IntelliSense для динамических объектов, так что это прекрасный способ изучения API.  В интерактивном окне можно попробовать следующее:

		# all poll objects
		Poll.objects.all()

		# primary key for poll
		p.id

		# all choice objects for the poll
		p.choice_set.all()

		# get object by primary key
		Poll.objects.get(pk=1)

	![Запрос опроса в оболочке Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-019-shell-query.png)

1. Запустите веб-сайт.  Вы должны увидеть опрос, добавленный с помощью оболочки Django.

## Публикация в Azure

Теперь, когда наша база данных находится в Azure, следующим шагом является размещение самого веб-сайта в Azure.

В Azure предусмотрено несколько вариантов размещения приложений Django:

- [Веб-сайт](http://www.windowsazure.com/ru-ru/services/web-sites/)
- [Облачная служба](http://www.windowsazure.com/ru-ru/services/cloud-services/)
- [Виртуальная машина](http://www.windowsazure.com/ru-ru/services/virtual-machines/)

В средствах Python для Visual Studio предусмотрены возможности публикации на веб-сайтах и в облачных службах Azure.  Они описываются в следующих 2 разделах, и вы можете выбрать один или оба варианта.

В обоих случаях PTVS позаботится о настройке IIS для вас и создаст файл web.config, если его еще нет в проекте.  Статические файлы будут собраны автоматически (manage.py collectstatic), если вы задали STATIC_ROOT в settings.py.

Размещение Django на виртуальной машине не рассматривается в этом учебнике.  Оно включает в себя создание виртуальной машины с использованием предпочтительной операционной системы (Windows или Linux), установку Python и развертывание вручную приложения Django. 

### Веб-сайт Azure

1. Сначала необходимо создать веб-сайт.  С помощью портала управления Azure щелкните **СОЗДАТЬ** -> **СРЕДА ВЫПОЛНЕНИЯ ПРИЛОЖЕНИЙ** -> **ВЕБ-САЙТ** -> **БЫСТРОЕ СОЗДАНИЕ**  Выберите любое доступное имя.

1. По завершении операции создания загрузите профиль публикации для веб-сайта.

	![Загрузка профиля веб-сайта](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-download-profile.png)

1. В Visual Studio щелкните правой кнопкой мыши узел проекта и выберите **Опубликовать**.

	![Публикация веб-сайта](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-publish.png)

1. Импортируйте ранее загруженный файл профиля публикации веб-сайта.

1. Примите значения по умолчанию и щелкните **Опубликовать** для запуска публикации.

1. По завершении публикации опубликованный веб-сайт откроется в веб-браузере.

	![Браузер веб-сайта](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website.png)

### Облачная служба Azure

1. Щелкните правой кнопкой мыши узел проекта и выберите **Добавить проект облачной службы Azure** или **Преобразовать -> Преобразовать в проект облачной службы Azure** (вы увидите тот или другой элемент в зависимости от версии Visual Studio).  В результате в решение добавится новый проект с суффиксом .Azure. Этот новый проект помечается как запускаемый проект в решении.

	**Примечание.** Если в контекстном меню проекта отсутствует команда для создания проекта облачной службы Azure, необходимо установить средства Azure для Visual Studio. Они устанавливаются в составе пакета Azure SDK для .NET. См. раздел требований в начале этого учебника.

	![Обозреватель решений](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-solution-explorer.png)

#### Запуск в эмуляторе Azure

1. Необходимо, чтобы функция **перезапуска Visual Studio с правами администратора** могла выполняться в эмуляторе среды выполнения приложений.

1. Начните отладку с помощью клавиши **F5**, и приложение запустится и развернется в эмуляторе среды выполнения приложений. Проверьте, работает ли интерфейс администрирования и можете ли вы голосовать в опросах.

	![Эмулятор среды выполнения приложений](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-emulator.png)

1. Теперь можно перезапустить Visual Studio, если вы не хотите продолжить работу в качестве администратора.

#### Публикация в облачной службе Azure

1. Теперь выполним публикацию в облачной службе Azure. Щелкните правой кнопкой мыши проект облачной службы **tutorial.Azure** и выберите **Опубликовать**.

	**Примечание.** Обязательно выберите команду **Опубликовать** в проекте облачной службы. В результате откроется диалоговое окно **Публикация приложения Azure** для публикации в облачной службе Azure.  Если выбрать команду **Опубликовать** в проекте Django, откроется диалоговое окно **Публикация веб-сайта**, которое используется для публикации на веб-сайте Azure.  

	![Публикация в облачной службе](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish.png)

1. Вам потребуется импортировать файл подписки Azure.  Чтобы загрузить его с портала Azure, щелкните ссылку [Вход для загрузки учетных данных](https://manage.windowsazure.com/publishsettings/index?client=vs&schemaversion=2.0).

1. На странице параметров выберите **Создать** в поле со списком "Облачная служба", чтобы создать новую облачную службу. Можно использовать любое доступное имя.

	![Параметры облачной службы](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-settings.png)

1. Примите значения по умолчанию и щелкните **Опубликовать**. Это займет больше времени, чем публикация на веб-сайте, поскольку необходимо подготовить виртуальную машину для облачной службы. Ход выполнения можно посмотреть в окне журнала действий Azure.

	![Публикация в облачной службе](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish-progress.png)

1. По завершении операции щелкните URL-адрес веб-сайта в окне журнала действий Azure, чтобы открыть веб-браузер.

	![Браузер облачной службы](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice.png)

## Заключение

В этом учебнике мы разработали приложение Django с помощью [средств Python для Visual Studio](http://pytools.codeplex.com).  Мы использовали 3 различные базы данных: sqlite3, SQL Server и MySQL.  Наконец, мы опубликовали приложение на веб-сайтах и в облачных службах Azure.

