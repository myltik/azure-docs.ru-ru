---
title: Создание решения IoT с помощью Stream Analytics | Microsoft Docs
description: Руководство по началу работы с решением IoT Stream Analytics (сценарий с пунктом сбора платы)
keywords: Решение IOT, функции окна
documentationcenter: ''
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun

ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 09/26/2016
ms.author: jeffstok

---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Создание решения IoT с помощью Stream Analytics
## <a name="introduction"></a>Введение
В этом руководстве содержатся инструкции по анализу данных в режиме реального времени с использованием Azure Stream Analytics. Разработчики могут легко объединять потоки данных, такие как сведения о посещении, данные журналов и созданные устройствами события, с историческими записями или справочными данными для получения бизнес-информации. Являясь полностью управляемой службой потоковой обработки в режиме реального времени, которая размещена на платформе Microsoft Azure, Azure Stream Analytics обеспечивает высокую надежность, малую задержку и масштабируемую обработку, что помогает приступить к работе за считаные минуты.

После работы с этим учебником вы сможете выполнить следующие задачи:

* Изучить портал Azure Stream Analytics.
* Настроить и развернуть задание потоковой передачи.
* Сформулировать реальные проблемы и устранить их с помощью языка запросов Stream Analytics.
* Уверенно разрабатывать решения потоковой передачи для клиентов с помощью Stream Analytics.
* Использовать процедуры мониторинга и ведения журнала для устранения неполадок.

## <a name="prerequisites"></a>Предварительные требования
Для работы с данным руководством вам потребуется:

* Последняя версия [Azure PowerShell](../powershell-install-configure.md)
* Visual Studio 2015 или бесплатная версия [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
* [Подписка Azure](https://azure.microsoft.com/pricing/free-trial/)
* Права администратора на компьютере.
* Файл [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) , который нужно скачать из Центра загрузки Майкрософт.
* Исходный код генератора событий TollApp из [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction:-“hello,-toll!”"></a>Введение в сценарий "Hello, Toll!"
Станции сбора дорожной платы представляют собой распространенное явление. Они встречаются на многих скоростных дорогах, мостах и туннелях по всему миру. Каждая станция имеет несколько пунктов сбора платы. В пунктах, работающих в ручном режиме, водитель останавливается и передает деньги служащему. В пунктах, работающих в автоматическом режиме, размещенный на крыше пункта датчик сканирует RFID-карту, прикрепленную на ветровом стекле автомобиля, во время его проезда через пункт. Проезд автомобилей через станции сбора платы можно легко представить в виде потока событий, в котором выполняются интересные операции.

![Изображение автомобилей в пунктах сбора платы](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Входящие данные
В этом руководстве используется два потока данных. Датчики, установленные на въездах и выездах станций сбора платы, создают первый поток. Второй поток — это статический набор данных, содержащий данные регистрации транспортного средства.

### <a name="entry-data-stream"></a>Входной поток данных
Входной поток данных содержит сведения об автомобилях, въезжающих в пункты сбора платы.

| ИД пункта сбора | Время въезда | LicensePlate | Состояние | Убедитесь, | Модель | Тип транспортного средства | Вес транспортного средства | Плата | Тег |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |10.09.2014 12:01:00.000 |JNB 7001 |Нью-Йорк |Honda |CRV |1 |0 |7 | |
| 1 |10.09.2014 12:02:00.000 |YXZ 1001 |Нью-Йорк |Toyota |Camry |1 |0 |4. |123456789 |
| 3 |10.09.2014 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |10.09.2014 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4. | |
| 1 |10.09.2014 12:03:00.000 |BNJ 1007 |Нью-Йорк |Honda |CRV |1 |0 |5 |789123456 |
| 2 |10.09.2014 12:05:00.000 |CDE 1007 |Нью-Джерси |Toyota |4x4 |1 |0 |6 |321987654 |

Далее приведено краткое описание столбцов:

| столбец | Описание |
| --- | --- |
| ИД пункта сбора |Уникальный идентификатор пункта сбора платы |
| Время въезда |Дата и время въезда транспортного средства в пункт сбора платы в формате UTC |
| LicensePlate |Номерной знак транспортного средства |
| Состояние |Штат в США |
| Убедитесь, |Изготовитель транспортного средства |
| Модель |Номер модели транспортного средства |
| Тип транспортного средства |1 — для пассажирского транспорта, 2 — для коммерческого транспорта |
| Тип веса |Вес транспортного средства в тоннах. 0 — для пассажирского транспорта. |
| Плата |Значение платы в долларах США |
| Тег |Электронная метка на автомобиле для автоматической оплаты. Пустая метка, если оплата осуществляется вручную |

### <a name="exit-data-stream"></a>Выходной поток данных
Выходной поток данных содержит сведения об автомобилях, выезжающих из станций сбора платы.

| **ИД пункта сбора** | **Время выезда** | **LicensePlate** |
| --- | --- | --- |
| 1 |10.09.2014T12:03:00.0000000Z |JNB 7001 |
| 1 |10.09.2014T12:03:00.0000000Z |YXZ 1001 |
| 3 |10.09.2014T12:04:00.0000000Z |ABC 1004 |
| 2 |10.09.2014T12:07:00.0000000Z |XYZ 1003 |
| 1 |10.09.2014T12:08:00.0000000Z |BNJ 1007 |
| 2 |10.09.2014T12:07:00.0000000Z |CDE 1007 |

Далее приведено краткое описание столбцов:

| столбец | Описание |
| --- | --- |
| ИД пункта сбора |Уникальный идентификатор пункта сбора платы |
| Время выезда |Дата и время выезда транспортного средства с пункта сбора платы в формате UTC |
| LicensePlate |Номерной знак транспортного средства |

### <a name="commercial-vehicle-registration-data"></a>Данные регистрации коммерческого транспортного средства
В руководстве используется статический снимок базы данных регистрации коммерческого транспортного средства.

| LicensePlate | ИД регистрации | Срок действия истек |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Далее приведено краткое описание столбцов:

| столбец | Описание |
| --- | --- |
| LicensePlate |Номерной знак транспортного средства |
| ИД регистрации |Идентификатор регистрации транспортного средства |
| Срок действия истек |Состояние регистрации транспортного средства: 0 — если регистрация транспортного средства действительна. 1 — если срок регистрации истек |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Настройка среды для Azure Stream Analytics
Для работы с этим руководством необходимо использовать подписку Microsoft Azure. Мы предлагаем бесплатную пробную версию служб Microsoft Azure.

Если у вас нет учетной записи Azure, вы можете [получить бесплатную пробную версию](http://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Чтобы зарегистрироваться для получения бесплатной пробной версии, потребуется мобильное устройство, которое может принимать текстовые сообщения, и действительная кредитная карта.
> 
> 

Обязательно выполните шаги в разделе "Очистка учетной записи Azure" в конце этой статьи, чтобы максимально эффективно использовать 200 долл. на счете Azure.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Подготовка ресурсов Azure, необходимых для работы с руководством
В этом руководстве необходимы два концентратора событий для получения потоков данных *entry* и *exit*. Для вывода результатов выполнения заданий Stream Analytics используется база данных SQL Azure. Для хранения справочных данных о регистрации транспортных средств используется служба хранилища Azure.

Для создания всех необходимых ресурсов можно использовать скрипт Setup.ps1 в папке TollApp на сайте GitHub. Его рекомендуется выполнить для экономии времени. Если вы хотите узнать больше о настройке этих ресурсов на портале Azure, см. приложение о настройке ресурсов для руководства на портале Azure.

Скачайте и сохраните вспомогательную папку [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) и файлы.

Откройте окно **Microsoft Azure PowerShell***от имени администратора*. Если у вас нет Azure PowerShell, следуйте инструкциям по установке в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md) .

Так как Windows автоматически блокирует файлы типа в формате PS1, DLL И EXE, перед запуском скрипта необходимо задать политику выполнения. Убедитесь, что окно Azure PowerShell открыто *от имени администратора*. Выполните команду **Set-ExecutionPolicy unrestricted**. При появлении запроса введите **Y**.

![Снимок экрана с командой Set-ExecutionPolicy unrestricted в окне Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Выполните **Get-ExecutionPolicy** , чтобы убедиться, что команда сработала.

![Снимок экрана с командой Get-ExecutionPolicy в окне Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Перейдите в каталог, где находятся скрипты и приложение генератора.

![Снимок экрана с командой cd .\TollApp\TollApp в окне Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Введите **.\\Setup.ps1**, чтобы настроить учетную запись Azure, создать и настроить все необходимые ресурсы и приступить к созданию событий. Скрипт случайным образом выбирает регион для создания ресурсов. Чтобы явно указать регион, можно передать параметр **-location** , как показано в следующем примере.

**.\\Setup.ps1 -location "Central US"**

![Снимок экрана страницы входа в Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

Скрипт откроет страницу **входа** в Microsoft Azure. Введите свои учетные данные.

> [!NOTE]
> Если у вас есть доступ к нескольким подпискам, то вам будет предложено ввести имя подписки, которая будет использоваться для работы с руководством.
> 
> 

Выполнение сценария может занять несколько минут. По завершении выходные данные должны выглядеть, как на снимке экрана ниже.

![Снимок экрана выходных данных скрипта в окне Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Появится еще одно окно, аналогичное приведенному ниже. Это приложение отправляет события в концентраторы событий Azure, что требуется для выполнения руководства. Поэтому до завершения работы с руководством приложение должно работать, а окно — оставаться открытым.

![Снимок экрана "Отправка данных в концентратор событий"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Теперь вы можете увидеть все созданные ресурсы на портале Azure. Откройте страницу <https://manage.windowsazure.com> и войдите в систему со своими учетными данными.

### <a name="azure-event-hubs"></a>Концентраторы событий Azure
Чтобы просмотреть концентраторы событий, созданные скриптом в предыдущем разделе, щелкните **Служебная шина** в левой части портала Azure.

![Служебная шина](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Вы увидите все доступные пространства имен в вашей подписке. Щелкните то, которое начинается с *tolldata* (в нашем примере — tolldata4637388511). Перейдите на вкладку **Концентраторы событий** .

![Вкладка "Концентраторы событий" на портале Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

В этом пространстве имен вы увидите два созданных концентратора событий с именами *entry* и *exit*.

![Снимок экрана концентраторов событий въезда и выезда на портале Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### <a name="azure-storage-container"></a>Контейнер хранилища Azure
1. Чтобы увидеть контейнер хранилища Azure, используемый в руководстве, щелкните **Хранилище** в левой части портала Azure.
   
    ![Пункт меню хранилища](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)
2. Щелкните тот, который начинается с *tolldata* (в нашем примере — tolldata4637388511). Откройте вкладку **Контейнеры** , чтобы увидеть созданный контейнер.
   
    ![Вкладка "Контейнеры" на портале Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)
3. Щелкните контейнер **tolldata** , чтобы увидеть отправленный JSON-файл с данными регистрации транспортных средств.
   
    ![Снимок экрана файла registration.json в контейнере](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### <a name="azure-sql-database"></a>База данных SQL Azure
1. Чтобы увидеть базу данных SQL, которая будет использоваться в руководстве, щелкните **Базы данных SQL** в левой части портала Azure.
   
    ![Базы данных SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)
2. Щелкните **tolldatadb**.
   
    ![Снимок экрана созданной базы данных SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)
3. Скопируйте имя сервера без номера порта (например,*имя_сервера*.database.windows.net).

## <a name="connect-to-the-database-from-visual-studio"></a>Подключение к базе данных из Visual Studio
Для доступа к результатам запроса в выходной базе данных можно использовать Visual Studio.

Подключитесь к базе данных SQL (целевой) из Visual Studio.

1. Откройте Visual Studio, а затем щелкните **Инструменты** > **Подключиться к базе данных**.
2. При запросе выберите **Microsoft SQL Server** в качестве источника данных.
   
    ![Диалоговое окно "Сменить источник данных"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)
3. В поле **Имя сервера** вставьте имя, скопированное в предыдущем разделе на портале Azure (т. е. *имя_сервера*.database.windows.net).
4. Выберите **Использовать проверку подлинности SQL Server**.
5. Введите **tolladmin** в поле **Имя пользователя** и **123toll!** в поле **Пароль**.
6. Щелкните **Выбрать или ввести имя базы данных** и выберите **TollDataDB** в качестве базы данных.
   
    ![Диалоговое окно "Добавить подключение"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
7. Нажмите кнопку **ОК**.
8. Откройте обозреватель сервера.
   
    ![Обозреватель серверов](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
9. В базе данных TollDataDB вы увидите четыре таблицы.
   
    ![Таблицы в базе данных TollDataDB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator:-tollapp-sample-project"></a>Генератор событий — пример проекта TollApp
Сценарий PowerShell автоматически начинает отправку событий, используя пример приложения TollApp. Никаких дополнительных действий выполнять не нужно.

Однако если вас интересуют подробности реализации, вы можете найти исходный код приложения TollApp на сайте GitHub в разделе [samples/TollApp](https://aka.ms/azure-stream-analytics-toll-source).

![Снимок экрана примера кода в Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Создание задания Stream Analytics
1. На портале Azure откройте Stream Analytics и нажмите кнопку **Создать** в левом нижнем углу страницы, чтобы создать задание аналитики.
   
    ![Кнопка «Создать»](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)
2. Щелкните **Быстрое создание**. Выберите тот же регион, где с помощью сценария создаются другие ресурсы.
3. Для параметра **Учетная запись хранения регионального мониторинга** выберите **Создать новую учетную запись хранения** и присвойте ей уникальное имя. С помощью этой учетной записи Azure Stream Analytics будет хранить сведения о наблюдении за всеми последующими заданиями.
4. В нижней части страницы щелкните **Создать задание Stream Analytics** .
   
    ![Параметр "Создать задание Stream Analytics"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Определение источников входных данных
1. На портале щелкните созданное задание аналитики.
   
    ![Снимок экрана задания аналитики на портале](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)
2. Щелкните вкладку **Входные данные** , чтобы определить источник данных.
   
    ![Вкладка "Входные данные"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)
3. Нажмите кнопку **Добавление входных данных**.
   
    ![Параметр "Добавление входных данных"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)
4. На первой странице выберите **Поток данных** .
   
    ![Параметр "Поток данных "](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)
5. На второй странице мастера выберите **Концентратор событий** .
   
    ![Параметр "Концентратор событий"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)
6. В качестве **Псевдонима входных данных** введите **EntryStream**.
7. Щелкните раскрывающийся список **Концентратор событий** и выберите концентратор, начинающийся с TollData (например, TollData9518658221).
8. Выберите **entry** в качестве имени концентратора событий и **all** в качестве имени политики концентратора событий.
   
    Настройки будут выглядеть следующим образом:
   
    ![Параметры концентратора событий](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)
9. На следующей странице выберите **JSON** в поле **Формат сериализации событий** и **UTF8** в поле **Кодировка**.
   
    ![Параметры сериализации](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)
10. Чтобы завершить работу мастера, нажмите кнопку **ОК** в нижней части страницы.
    
    ![Снимок экрана входных данных EntryStream на портале Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)
    
    Теперь после создания потока entry выполните те же действия для создания потока exit. На третьей странице мастера обязательно введите значения, как показано на снимке экрана ниже.
    
    ![Параметры потока выхода](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)
    
    Определены два потока входных данных:
    
    ![Определенные потоки входных данных на портале Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)
    
    Далее добавим входные справочные данные для файла большого двоичного объекта с данными регистрации автомобиля.
11. Щелкните **Добавить входные данные**, а затем выберите **Справочные данные**.
    
    ![Параметры добавления входных данных с выбранным параметром "Справочные данные"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)
12. На следующей странице выберите учетную запись хранения, которая начинается с **tolldata**. Контейнер должен иметь имя **tolldata**, а в разделе **Шаблон пути** должен быть выбран BLOB-объект **registration.json**. Имя файла должно быть введено строчными буквами (в нем учитывается регистр).
    
    ![Параметры хранилища BLOB-объектов](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)
13. На следующей странице выберите значения, как показано ниже, и нажмите кнопку **ОК** , чтобы завершить работу мастера.
    
    ![Выбор JSON в качестве формата сериализации событий и UTF8 в качестве типа кодировки](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

Все входные данные определены.

![Снимок экрана с тремя определенными наборами входных данных](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## <a name="define-output"></a>Определение выходных данных
1. Перейдите на вкладку **Выходные данные** и нажмите кнопку **Добавить выходные данные**.
   
    ![Вкладка "Выходные данные" и параметр "Добавить выходные данные"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)
2. Щелкните элемент **База данных SQL**.
3. Выберите имя сервера, которое использовалось в разделе "Подключение к базе данных из Visual Studio". Имя базы данных — **TollDataDB**.
4. Введите **tolladmin** в поле **Имя пользователя**, **123toll!** в поле **Пароль** и **TollDataRefJoin** в поле **Таблица**.
   
    ![Параметры базы данных SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## <a name="azure-stream-analytics-query"></a>Запрос в службе Azure Stream Analytics
Вкладка **Запрос** содержит запрос SQL, который выполняет преобразование входящих данных.

![Добавленный запрос на вкладке "Запрос"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

В этом руководстве мы попытаемся ответить на несколько бизнес-вопросов, связанных с данными платы, и создадим запросы Stream Analytics, используемые в Azure Stream Analytics для нахождения нужного ответа.

Прежде чем приступить к первому заданию Stream Analytics, давайте рассмотрим некоторые сценарии и синтаксис запроса.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Общие сведения о языке запросов Azure Stream Analytics
- - -
Допустим, вам нужно подсчитать количество автомобилей, въезжающих в пункт сбора платы. Так как это непрерывный поток событий, необходимо определить период времени. Давайте изменим вопрос на следующий: "Сколько автомобилей въезжает в пункт сбора платы каждые три минуты?". Этот период часто называют переворачивающимся.

Давайте взглянем на запрос Azure Stream Analytics, который отвечает на этот вопрос.

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Как видите, Azure Stream Analytics использует язык запросов, такой же как SQL, и добавляет несколько расширений для указания аспектов запроса, связанных со временем.

Дополнительные сведения об используемых в запросах конструкциях [Управление временем](https://msdn.microsoft.com/library/azure/mt582045.aspx) и [Оконные расширения](https://msdn.microsoft.com/library/azure/dn835019.aspx) см. на сайте MSDN.

## <a name="testing-azure-stream-analytics-queries"></a>Тестирование запросов в службе Azure Stream Analytics
Теперь, когда вы написали первый запрос Azure Stream Analytics, настало время его тестирования с помощью файлов-примеров, расположенных в папке TollApp по указанному ниже пути.

**..\\TollApp\\TollApp\\Data**

Эта папка содержит следующие файлы:

* Entry.json
* Exit.json
* Registration.json

## <a name="question-1:-number-of-vehicles-entering-a-toll-booth"></a>Вопрос 1. Количество автомобилей, въезжающих в пункт сбора платы
1. Откройте портал Azure и перейдите к созданному заданию Azure Stream Analytics. Откройте вкладку **Запрос** и вставьте запрос из предыдущего раздела.
   
    ![Вставленный запрос на вкладке "Запрос"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)
2. Чтобы сверить этот запрос с примером данных, нажмите кнопку **Тестировать** . В открывшемся окне перейдите к файлу Entry.json (пример данных из потока событий **EntryTime** ).
   
    ![Снимок экрана файла Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)
3. Убедитесь, что отображаются нужные результаты запроса.
   
    ![Результаты тестирования](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## <a name="question-2:-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Вопрос 2. Общее время, затрачиваемое каждым автомобилем на проезд через пункт сбора платы
Среднее время, затрачиваемое на проезд через пункт, помогает оценить эффективность процесса и условий работы для клиентов.

Чтобы определить общее время, необходимо объединить поток EntryTime и ExitTime. Потоки будут объединены по столбцам TollId и LicencePlate. Для использования оператора **JOIN** требуется указать запас, описывающий допустимую разницу во времени между объединенными событиями. Вы будете использовать функцию **DATEDIFF** , чтобы указать, что события должны происходить с интервалом не более 15 минут. Кроме того, необходимо применить функцию **DATEDIFF** к значениям времени въезда и выезда для вычисления фактического времени, проводимого автомобилем на станции сбора платы. Обратите внимание на различия использования **DATEDIFF** при применении в инструкции **SELECT** по сравнению с условием **JOIN**.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Чтобы протестировать этот запрос, обновите его на вкладке **Запрос** задания.
   
    ![Обновленный запрос на вкладке "Запрос"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)
2. Щелкните **Тестировать** и укажите примеры входных файлов для EntryTime и ExitTime.
   
    ![Снимок экрана выбранных входных файлов](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)
3. Установите флажок, чтобы проверить запрос и просмотреть выходные данные.
   
    ![Выходные данные тестирования](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3:-report-all-commercial-vehicles-with-expired-registration"></a>Вопрос 3. Сообщать обо всех коммерческих транспортных средствах с истекшим сроком действия регистрации
Azure Stream Analytics может использовать статические снимки данных для объединения с потоками временных данных. Чтобы продемонстрировать эту возможность, можно использовать следующий пример вопроса.

Если коммерческое транспортное средство зарегистрировано в компании автодорожных сборов, оно может проезжать через пункт оплаты без остановки для проверки. Для выявления всех коммерческих транспортных средств с истекшим сроком действия регистрации будет использоваться таблица подстановки "Регистрация коммерческих транспортных средств".

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

Чтобы протестировать запрос с помощью справочных данных, для этих данных необходимо определить источник входных данных (что мы уже сделали).

1. Чтобы протестировать этот запрос, вставьте его на вкладку **Запрос**, щелкните **Тестировать** и укажите два источника входных данных:
   
    ![Снимок экрана выбранных входных файлов](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)
2. Просмотрите результаты запроса.
   
    ![Снимок экрана выходных данных запроса](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## <a name="start-the-stream-analytics-job"></a>Запуск задания Stream Analytics
Теперь необходимо завершить настройку и запустить задание. Сохраните запрос из вопроса 3. Будут созданы выходные данные, которые соответствуют схеме таблицы выходных данных **TollDataRefJoin**.

Перейдите на **панель мониторинга** задания и нажмите кнопку **Запустить**.

![Снимок экрана с кнопкой "Запустить" на панели мониторинга заданий](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

В открывшемся диалоговом окне измените **Время начала передачи выходных данных** на **Настраиваемое время**. Укажите время за час до текущего времени. Таким образом, будут обработаны все события из концентратора событий, созданные в рамках этого руководства. Установите флажок, чтобы запустить задание.

![Выбор настраиваемого времени](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

Запуск задания может занять несколько минут. Состояние отображается на странице верхнего уровня для Stream Analytics.

![Снимок экрана состояния задания](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## <a name="check-results-in-visual-studio"></a>Проверка результатов в Visual Studio
1. Откройте обозреватель серверов Visual Studio и щелкните правой кнопкой мыши таблицу **TollDataRefJoin** .
2. Выберите **Показать таблицу данных** для просмотра выходных данных задания.
   
    ![Выбор "Показать таблицу данных" в обозревателе серверов](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Задания по масштабированию в Azure Stream Analytics
Azure Stream Analytics поддерживает масштабируемость, что позволяет обрабатывать большие объемы данных. С помощью предложения **PARTITION BY** служба Azure Stream Analytics указывает системе, что на этом шаге будет выполняться масштабирование. **PartitionId** — это специальный, добавленный системой столбец, который совпадает с идентификатором раздела входных данных (концентратора событий).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Остановите текущее задание, обновите запрос на вкладке **Запрос** и откройте вкладку **Масштаб**.
   
    **Единицы потоковой передачи** определяют объем вычислительной мощности, которую может получать задание.
2. Передвиньте ползунок в положение 6.
   
    ![Снимок экрана: выбор 6 единиц потоковой передачи](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)
3. Перейдите на вкладку **Выходные данные** и измените имя таблицы SQL на **TollDataTumblingCountPartitioned**.

Теперь при запуске задания Azure Stream Analytics сможет распределять нагрузку среди большего количества вычислительных ресурсов и повышать пропускную способность. Обратите внимание, что приложение TollApp также отправляет события, секционированные по TollId.

## <a name="monitor"></a>Монитор
Вкладка **Мониторинг** содержит статистические данные по выполнению задания.

![Снимок экрана статистики о выполнении заданий](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

На вкладке **Панель мониторинга** можно получить доступ к **Журналам операций**.

![Параметр "Журналы операций"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![Снимок экрана журналов операций, где можно просмотреть состояние заданий](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

Чтобы просмотреть дополнительные сведения об определенном событии, выберите событие и нажмите кнопку **Сведения** .

![Снимок экрана сведений о выбранном событии](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## <a name="conclusion"></a>Заключение
В этом руководстве приведены общие сведения о службе Azure Stream Analytics. Вы узнали, как настроить входные и выходные данные для задания Stream Analytics. На основе сценария "Данные о сборе платы" вы узнали о распространенных проблемах, возникающих в среде передачи данных, и способах их устранения с помощью простых запросов SQL в службе Azure Stream Analytics. В руководстве описаны конструкции расширений SQL для работы с временными данными. Здесь также рассмотрено объединение потоков данных, их дополнение статическими справочными данными, а также масштабирование запроса для повышения пропускной способности.

Несмотря на то что в этом руководстве хорошо представлена вступительная информация, оно является далеко не полным. Дополнительные шаблоны запросов на языке SQL можно найти в статье [Примеры запросов для распространенных шаблонов использования Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).
Дополнительные сведения об Azure Stream Analytics см. в [документации в Интернете](https://azure.microsoft.com/documentation/services/stream-analytics/).

## <a name="clean-up-your-azure-account"></a>Очистка учетной записи Azure
1. Остановите выполнение задания Stream Analytics на портале Azure.
   
    Скрипт Setup.ps1 создает два концентратора событий и базу данных SQL. Следующие инструкции предназначены для очистки ресурсов по завершении изучения этого руководства.
2. В окне PowerShell введите **.\\Cleanup.ps1**, чтобы запустить скрипт, который удалит ресурсы, используемые в этом руководстве.
   
   > [!NOTE]
   > Ресурсы идентифицируются по имени. Убедитесь, что перед подтверждением удаления вы внимательно просмотрели каждый элемент.
   > 
   > 
   
    ![Снимок экрана процесса очистки](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)

<!--HONumber=Oct16_HO2-->


