<properties title="Начало работы с использованием HBase с Hadoop в HDInsight" pageTitle="Начало работы с использованием Apache Storm c Microsoft Azure HDInsight (Hadoop)" description="Узнайте, как использовать Apache Storm для обработки данных с HDInsight (Hadoop) в режиме реального времени" metaKeywords="Azure hdinsight storm, Azure hdinsight realtime, azure hadoop storm, azure hadoop realtime, azure hadoop real-time, azure hdinsight real-time" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="larryfr" />

# Начало работы с использованием Storm c HDInsight (Hadoop)

Apache Storm – это масштабируемая отказоустойчивая распределенная система выполнения расчетов для обработки данных потоковой передачи в режиме реального времени. С Azure HDInsight вы можете создать облачный кластер, проводящий анализ данных в режиме реального времени с помощью Storm.

## В этом руководстве вы узнаете...

-   [Как подготовить кластер HDInsight Storm][Как подготовить кластер HDInsight Storm]

-   [Как подключиться к кластеру][Как подключиться к кластеру]

-   [Как выполнить топологию Storm][Как выполнить топологию Storm]

-   [Как контролировать топологию Storm][Как контролировать топологию Storm]

-   [Как остановить топологию Storm][Как остановить топологию Storm]

-   [Дальнейшие действия][Дальнейшие действия]

## Перед началом работы

Для успешного завершения этого руководства у вас должно быть следующее:

-   Подписка Azure

-   Windows Azure PowerShell

-   Если вы не знакомы с Apache Storm, вам следует сначала ознакомиться со статьей [HDInsight Storm Overview][HDInsight Storm Overview].

## <span id="provision"></span></a>Подготовка кластера Storm на портале Azure

[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

1.  Выполните вход в [портал управления Azure][портал управления Azure]

2.  Щелкните **HDInsight** слева, затем **+NEW** в нижнем левом углу страницы.

3.  Щелкните значок HDInsight во второй колонне, а затем выберите **Настроить**.

4.  На странице **Сведения о кластере** введите имя кластера и выберите **Storm** для **Типа кластера**. Щелкните стрелку для продолжения.

    ![подробная информация о кластере][подробная информация о кластере]

5.  Введите число **узлов данных** для использования в этом кластере и **регион/виртуальная сеть**, в которых будет создан этот кластер. Щелкните стрелку для продолжения.

    > [WACOM.NOTE] Для сокращения затрат на кластер, приведенный в этой статье, уменьшите **Размер кластера** до 1 и удалите кластер после того, как завершите им пользоваться.

    ![информация об узлах и регионе][информация об узлах и регионе]

6.  Введите **Имя пользователя** администратора и **Пароль**, затем выберите стрелку, чтобы продолжить.

    ![учетная запись и пароль][учетная запись и пароль]

7.  Для **Учетной записи хранения** выберите **Создать новое хранилище** или выберите существующую запись хранения. Выберите или введите **Имя учетной записи** и **Контейнер по умолчанию** для использования. Щелкните значок галочки слева внизу для создания кластера Storm.

    ![учетная запись хранения][учетная запись хранения]

## Использование HDInsight Storm

В предварительной версии HDInsight Storm для работы с Storm вы должны подключиться к кластеру с помощью удаленного рабочего стола. Для подключения к кластеру HDInsight и выполнению команд Storm выполните следующее:

### <span id="connect"></span></a>Подключитесь к кластеру

1.  Разрешить кластеру HDInsight подключение к удаленному рабочему столу с помощью [портал управления Azure][управление]. На портале выберите ваш кластер HDInsight, затем выберите флажок **Включить удаленный рабочий стол** в нижней части страницы **Конфигурация**.

    ![включить удаленный рабочий стол][включить удаленный рабочий стол]

    Когда появится запрос на ввод учетных данных, введите имя пользователя и пароль для использования в сеансах подключения к удаленному рабочему столу. Вы также должны выбрать срок действия для конфигурации удаленного рабочего стола.

    ![конфигурации удаленного рабочего стола][конфигурации удаленного рабочего стола]

2.  После включения доступа к удаленному рабочему столу выберите **Подключить** для начала подключения. При этом будет загружен файл **.rdp**, который можно использовать для запуска сеанса подключения к удаленному рабочему столу.

    ![подключить][подключить]

    > [WACOM.NOTE] При подключении к удаленному компьютеру вы можете получить несколько приглашений подтвердить доверие этому сертификату.

3.  После подключения используйте значок **командной строки Hadoop** на рабочем столе, чтобы запустить командную строку Hadoop.

    ![командная строка hadoop][командная строка hadoop]

4.  Для замены каталогов на каталог, содержащий команду Storm выполните следующую команду:

    cd %storm\_home%\\bin

5.  Для просмотра доступных команд введите `storm` без параметров.

Кластер The HDInsight поставляется с несколькими примерами топологий Storm. Образец **WordcountTopology** используется следующим образом: Дополнительные сведения о примерах, поставляемых с HDInsight Storm, см. в разделе [Дальнейшие действия][Дальнейшие действия].

### <span id="run"></span></a>Запуск топологии Storm

Используйте следующую команду для выполнения **WordCountTopology**

    storm jar ..\contrib\storm-starter\storm-starter-<version>-jar-with-dependencies.jar storm.starter.WordCountTopology wordcount

Это указывает Storm о том, что мы хотим запустить топологию **wordcount** с класса **storm.starter.WordCountTopology**, который содержится в файле **storm-starter-\<version\>-jar-with-dependencies.jar**. Наряду с остальными примерами Storm файл расположен в папке contrib каталога %storm\_home%.

> [WACOM.NOTE] Версия файла JAR, содержащего примеры, может периодически меняться. При выполнении команды укажите версию файла, предоставленного вам с кластером.

Заметьте, что после выполнения команды ничего не возвращается. **После запуска топология Storm выполняется до тех пор, пока вы ее остановите.** Топология WordCountTopology формирует случайные предложения и подсчитывает, сколько раз она встречает каждое слово, до тех пор, пока вы ее не остановите.

### <span id="monitor"></span></a>Мониторинг статуса топологии Storm

Образец The WordCountTopology не записывает выход в каталог, но может использовать интерфейс веб-страниц Storm для обзора состояния топологии, а также выхода.

1.  Подключение к кластеру HDInsight с помощью удаленного рабочего стола.

2.  Откройте линк **Storm UI** с рабочего стола. Отобразится веб-страница с интерфейсом Storm. Под **Topology summary**, выберите запись **wordcount**.

    ![интерфейс storm][интерфейс storm]

    Отображает статистику для топологии, включая компоненты, составляющие топологию, т. е. **spouts** и **bolts**.

3.  Выбрать на странице линк **spout**, затем выбрать номер **порта** для одной из записей в списке **Исполнители (постоянные)** больше 0 колонн **Сгенерированный** и **Переданный**.

    ![spouts и bolts][spouts и bolts]

    ![выбор исполнителей][выбор исполнителей]

    > [WACOM.NOTE] В зависимости от количества узлов в вашем кластере и выполняемой вами топологии на начало обработки топологии может потребоваться несколько минут. Периодически обновляйте страницу до тех пор, пока значения **Переданный** и **Сгенерированный** не начнут расти.

4.  Вы должны увидеть страницу с информацией, аналогичной показанной ниже.

        2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [snow white and the seven dwarfs] 
        2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [and] 
        2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16774] 
        2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [and] 
        2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16775] 
        2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [dwarfs] 
        2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8359] 
        2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [dwarfs] 
        2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8360] 
        2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [i am at two with nature] 
        2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:23, stream: default, id: {}, [two] 
        2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [two, 8236] 
        2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:22, stream: default, id: {}, [a] 
        2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [a, 8280] 
        2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:19, stream: default, id: {}, [and] 
        2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16776] 

    На приведенном ниже фрагменте видно, как переданный spout 'snow white and the seven dwarfs' разделен на отдельные слова. Подсчитывается также, сколько раз топология обрабатывает каждое слово с начала его запуска. Например, к моменту просмотра верхнего выхода слово 'dwarfs' было сгенерировано 8360.

### <span id="stop"></span></a>Остановка топологии Storm

Топология **WordCountTopology** будет выполняться до тех пор, пока ее остановят. Для ее остановки введите следующую команду:

    storm kill wordcount

Если посмотреть на интерфейс веб-страницы Storm непосредственно после вводы команды, можно заметить, что состояние **wordcount** в **Сводке топологии** сменилось на ЗАВЕРШЕН. Через несколько секунд топологии в разделе **Topology summary** более не будет.

## <span id="next"></span></a>Дальнейшие действия

-   **Примеры файлов** — кластер HDInsight Storm предоставлет несколько примеров в каталоге **%storm\_home%\\contrib**. Каждый пример должен содержать следующие элементы:

    -   Исходный код, например, storm-starter-0.9.1.2.1.5.0-2057-sources.jar

    -   Документы Java, например, storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar

    -   Пример, например, storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar

    Для извлечения исходного кода или документов Java используйте команду 'jar'. Например, 'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'.

    > [WACOM.NOTE] Документы Java состоят из веб-страниц. После извлечения откройте файл **index.html** браузером.

-   [Анализируя данные от датчиков со Storm и HDInsight][Анализируя данные от датчиков со Storm и HDInsight]

-   [Разработка приложений для обработки потоковой передачи данных с помощью SCP.NET и C\# на Storm в HDInsight][Разработка приложений для обработки потоковой передачи данных с помощью SCP.NET и C\# на Storm в HDInsight]

  [Как подготовить кластер HDInsight Storm]: #provision
  [Как подключиться к кластеру]: #connect
  [Как выполнить топологию Storm]: #run
  [Как контролировать топологию Storm]: #monitor
  [Как остановить топологию Storm]: #stop
  [Дальнейшие действия]: #next
  [HDInsight Storm Overview]: /ru-ru/documentation/articles/hdinsight-storm-overview
  [портал управления Azure]: https://manage.windowsazure.com/
  [подробная информация о кластере]: ./media/hdinsight-storm-getting-started/wizard1.png
  [информация об узлах и регионе]: ./media/hdinsight-storm-getting-started/wizard2.png
  [учетная запись и пароль]: ./media/hdinsight-storm-getting-started/wizard3.png
  [учетная запись хранения]: ./media/hdinsight-storm-getting-started/wizard4.png
  [включить удаленный рабочий стол]: ./media/hdinsight-storm-getting-started/enableremotedesktop.png
  [конфигурации удаленного рабочего стола]: ./media/hdinsight-storm-getting-started/configremotedesktop.png
  [подключить]: ./media/hdinsight-storm-getting-started/connect.png
  [командная строка hadoop]: ./media/hdinsight-storm-getting-started/hadoopcommandline.png
  [интерфейс storm]: ./media/hdinsight-storm-getting-started/stormui.png
  [spouts и bolts]: ./media/hdinsight-storm-getting-started/stormuiboltsnspouts.png
  [выбор исполнителей]: ./media/hdinsight-storm-getting-started/executors.png
  [Анализируя данные от датчиков со Storm и HDInsight]: /ru-ru/documentation/articles/hdinsight-storm-sensor-data-analysis
  [Разработка приложений для обработки потоковой передачи данных с помощью SCP.NET и C\# на Storm в HDInsight]: /ru-ru/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application
