<properties
	pageTitle="Приступая к работе с решениями с предварительно заданными параметрами | Microsoft Azure"
	description="Следуйте инструкциям этого учебника, чтобы научиться развертывать предварительно настроенные решения набора IoT Microsoft Azure"
	services=""
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="12/03/2015"
     ms.author="dobett"/>

# Учебник: начало работы с решениями IoT с предварительно заданными параметрами

## Введение

Набор [предварительно настроенных решений][lnk-preconfigured-solutions] Azure IoT включает несколько служб Azure IoT, которые используются при создании комплексных решений для реализации распространенных бизнес-сценариев IoT.

В этом руководстве показано, как подготовить предварительно настроенное решение *удаленного мониторинга*. Кроме того, описываются основные функции этого решения.

Для работы с этим руководством вам потребуется активная подписка Azure.

> [AZURE.NOTE]Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][lnk_free_trial].

## Подготовка предварительно настроенного решения удаленного мониторинга

1.  Войдите на сайт [azureiotsuite.com][lnk-azureiotsuite] с помощью учетных данных Azure и нажмите кнопку **+**, чтобы создать решение.

2.  На плитке **Удаленный мониторинг** щелкните **Выбрать**.

3.  Укажите **имя** для предварительно настроенного решения удаленного мониторинга.

4.  Укажите **регион** и **подписку**, которые будут использоваться при подготовке решения.

5.  Щелкните **Создать решение**, чтобы начать процесс подготовки. Это обычно занимает несколько минут.

## Просмотр панели мониторинга решения удаленного мониторинга

1.  Когда подготовка завершится и на плитке предварительно настроенного решения появится надпись **Готово**, нажмите кнопку **Запустить**. Панель мониторинга решения откроется на новой вкладке.

    ![][img-launch-solution]

2.  По умолчанию на портале решения отображается *панель мониторинга решения*. В меню слева можно выбрать другие представления.

    ![][img-dashboard]

## Просмотр списка устройств решения

1.  В меню слева щелкните **Устройства**, чтобы перейти к *списку устройств* этого решения.

    ![][img-devicelist]

2.  Вы увидите четыре имитации устройств, созданные в ходе подготовки.

3.  Чтобы просмотреть сведения об устройстве, щелкните его в списке устройств.

    ![][img-devicedetails]

## Отправка команды устройству

1.  В области сведений о выбранном устройстве щелкните **Команды**.

    ![][img-devicecommands]

2.  В списке команд выберите **PingDevice**.

3.  Щелкните **Отправить команду**.

4.  В журнале команд отобразится состояние команды.

    ![][img-pingcommand]

## Добавление нового виртуального устройства

1.  Вернитесь к списку устройств.

2.  В левом нижнем углу щелкните **+ Добавить устройство**, чтобы добавить новое устройство.

    ![][img-adddevice]

3.  На плитке **Имитация устройства** щелкните **Добавить**.

    ![][img-addnew]

4.  Выберите пункт **Задать идентификатор устройства самостоятельно** и укажите имя уникального идентификатора устройства: **mydevice\_01**.

5.  Щелкните **Создать**.

    ![][img-definedevice]

6. На этапе 3 действия **Добавить имитацию устройства** щелкните **Готово**, чтобы вернуться к списку устройств.

7.  В списке устройств состояние устройства изменится на **Работает**.

    ![][img-runningnew]

## Просмотр и изменение правил решения

1.  Вернитесь к панели мониторинга решения и выберите плитку **Журнал сигналов**.

    ![][img-alarmhistory]

2.  Эти сигналы активирует правило **AlarmTemp**.

3.  В меню слева щелкните **Правила**, чтобы просмотреть список правил для решения.

    ![][img-rules]

4.  Для предварительно настроенного решения доступны два правила.

5.  В списке правил щелкните правило **Температура**, чтобы просмотреть его свойства.

6.  На панели свойств правила щелкните **Изменить**.

    ![][img-displayrule]

7.  Введите 30 в поле **Пороговое значение**, оставив остальные свойства без изменений.

8.  Щелкните **Сохранить и просмотреть правила**.

    ![][img-editrule]

9.  Вернитесь к таблице **История сигналов** на **Панели мониторинга решения** и обратите внимание, как изменилось поведение правила.

    ![][img-newhistory]

## Дальнейшие действия

Теперь, когда вы создали рабочее предварительно настроенное решение, можете перейти к следующим сценариям.

-   [Рекомендации по настройке предварительно настроенных решений][lnk-customize]
-   [Обзор предварительно настроенного решения прогнозного обслуживания][lnk-predictive]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-alarmhistory]: media/iot-suite-getstarted-preconfigured-solutions/alarmhistory.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-displayrule]: media/iot-suite-getstarted-preconfigured-solutions/displayrule.png
[img-editrule]: media/iot-suite-getstarted-preconfigured-solutions/editrule.png
[img-newhistory]: media/iot-suite-getstarted-preconfigured-solutions/newhistory.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_1210_2015-->