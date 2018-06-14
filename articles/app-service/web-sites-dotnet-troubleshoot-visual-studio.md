---
title: Устранение неполадок веб-приложения в службе приложений Azure с помощью Visual Studio
description: Узнайте, как устранять неполадки в работе веб-приложения Azure с помощью удаленной отладки, трассировки и средств ведения журналов, которые встроены в Visual Studio 2013.
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
editor: ''
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.author: cephalin
ms.openlocfilehash: 7973f4311095b7c87ccd2394b048ec92c50f32a9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30266144"
---
# <a name="troubleshoot-a-web-app-in-azure-app-service-using-visual-studio"></a>Устранение неполадок веб-приложения в службе приложений Azure с помощью Visual Studio
## <a name="overview"></a>Обзор
В этом руководстве показано, как использовать инструменты Visual Studio, которые позволяют отлаживать работу веб-приложения в [службе приложений](http://go.microsoft.com/fwlink/?LinkId=529714) путем запуска веб-приложения удаленно в [режиме отладки](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) или путем просмотра журналов приложения и журналов веб-сервера.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Вы узнаете:

* Какие функции управления веб-приложением Azure доступны в среде Visual Studio.
* Как использовать удаленное представление Visual Studio для быстрого изменения удаленного веб-приложения.
* Как удаленно запустить режим отладки для веб-приложения и веб-задания, если проект запущен в Azure.
* Как создать журналы трассировки приложения и просматривать их непосредственно во время создания.
* Как просматривать журналы веб-сервера, включая подробные сообщения об ошибках и трассировку неудачно завершившихся запросов.
* Как отправлять диагностические журналы в учетную запись хранения Azure и просматривать их там.

Если у вас установлена Visual Studio Ultimate, вы также можете использовать [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) для отладки. Использование IntelliTrace не рассматривается в этом учебнике.

## <a name="prerequisites"></a>Предварительные требования
В этом руководстве речь идет о среде разработки, веб-проекте и веб-приложении Azure, которые вы настроили, следуя инструкциям в статье [Начало работы с Azure и ASP.NET][GetStarted]. Для разделов, посвященных веб-заданиям, вам понадобится приложение, созданное при изучении руководства [Начало работы с пакетом SDK для Azure для веб-заданий][GetStartedWJ].

В этом учебнике приводятся примеры кода для веб-приложения C# MVC, однако в приложениях Visual Basic и веб-форм применяются те же процедуры устранения неполадок.

В этом руководстве предполагается, что вы используете Visual Studio 2017 или Visual Studio 2013. 

Журналы потоковой передачи работают только для приложений, которые предназначены для .NET Framework 4 и выше.

## <a name="sitemanagement"></a>Настройка веб-приложения и управление им
Visual Studio обеспечивает доступ к сокращенному набору функций управления веб-приложением и параметров конфигурации, полный набор которых доступен на [портале Azure](http://go.microsoft.com/fwlink/?LinkId=529715). В этом разделе вы узнаете, какие функции доступны с помощью **обозревателя сервера**. Для просмотра новых функций интеграции Azure также воспользуйтесь **Обозревателем облака** . Оба окна можно открыть в меню **Вид** .

1. Если вы еще не вошли в Azure в Visual Studio, щелкните правой кнопкой мыши **Azure** и выберите подключение к **подписке Microsoft Azure** в **обозревателе сервера**.

    Также вы можете установить сертификат управления, обеспечивающий доступ к вашей учетной записи. Чтобы установить сертификат, щелкните правой кнопкой мыши узел **Azure** в **обозревателе сервера** и выберите **Управление подписками и их фильтрация** в контекстном меню. В диалоговом окне **Управление подписками Microsoft Azure** откройте вкладку **Сертификаты**, а затем щелкните **Импорт**. Следуя указаниям, загрузите и импортируйте файл подписки (файл *.publishsettings* ) для своей учетной записи Azure.

   > [!NOTE]
   > Если вы скачиваете файл подписки, сохраните его в папку, не входящую в структуру каталогов с исходными кодами (например, в папку «Загрузки»), а затем удалите его сразу после завершения импорта. Злоумышленник, получивший доступ к файлу подписки, сможет изменять, создавать и удалять ваши службы Azure.
   >
   >

    Дополнительные сведения о подключении к ресурсам Azure из Visual Studio см. в разделе [Управление учетными записями, подписками и административными ролями](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. В **обозревателе сервера** разверните узел **Azure**, а затем — узел **Служба приложений**.
3. Разверните группу ресурсов, которая содержит веб-приложение, созданное в разделе [Начало работы с Azure и ASP.NET][app-service-web-get-started-dotnet.md], а затем щелкните узел этого веб-приложения правой кнопкой мыши и выберите **Просмотреть параметры**.

    ![Просмотр параметров в обозревателе сервера](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Появится вкладка **Веб-приложение Azure** , где можно просмотреть задачи по управлению и конфигурации, доступные в Visual Studio.

    ![Окно веб-приложений Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    В этом руководстве используются раскрывающиеся списки ведения журналов и трассировки. Для удаленной отладки вы будете использовать другой метод.

    Сведения о полях параметров приложения и строк подключения в этом окне см. в разделе [Веб-приложения Azure: как работают параметры приложения и строки подключения](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Если необходимо выполнить задачу по управлению веб-приложением, которую невозможно выполнить в этом окне, щелкните **Открыть на портале управления** , чтобы открыть портал Azure в браузере.

## <a name="remoteview"></a>Доступ к файлам веб-сайта в обозревателе серверов
Как правило, при развертывании веб-проекта флагу `customErrors` в файле Web.config присваивается значение `On` или `RemoteOnly`, поэтому вы не видите сообщения о возникающих ошибках. В большинстве случаев при ошибке отображается страница следующего вида.

**Ошибка сервера в приложении "/":**

![Неинформативная страница ошибки](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Произошла ошибка:**

![Неинформативная страница ошибки](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Не удается отобразить страницу веб-сайта**

![Неинформативная страница ошибки](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Подробное сообщение об ошибке, как правило, содержит полезные сведения для ее устранения. На предыдущих снимках экрана показано, как включить эту функцию. Для этого необходимо внести изменения в развернутый файл Web.config. Вы можете изменить файл *Web.config* в проекте и повторно развернуть проект либо создать [преобразование Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) и развернуть отладочную сборку. Однако вы можете значительно упростить этот процесс с помощью **обозревателя решений**, непосредственно просматривая и редактируя файлы на удаленном сайте с использованием функции *удаленного представления*.

1. В **обозревателе сервера** разверните узлы **Azure**, **Служба приложений** и группу ресурсов, в которой расположено ваше веб-приложение, а затем разверните узел веб-приложения.

    Откроются узлы, обеспечивающие доступ к файлам содержимого и журналов веб-приложения.
2. Разверните узел **Файлы** и дважды щелкните файл *Web.config* .

    ![Открытие файла Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    В Visual Studio откроется файл Web.config удаленного веб-приложения, а в строке заголовка к его имени будет добавлено [Remote].
3. Добавьте следующую строку в элемент `system.web` :

    `<customErrors mode="Off"></customErrors>`

    ![Изменение файла Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Обновите окно браузера, в котором отображается неинформативное сообщение. Вы увидите подробную информацию об ошибке, как в следующем примере:

    ![Подробные сведение об ошибке](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    Для создания отображаемой ошибки в файл *Views\Home\Index.cshtml* была добавлена выделенная красным цветом строка.

Изменение файла Web.config — это пример упрощения устранения неполадок путем чтения и редактирования файлов в веб-приложении Azure.

## <a name="remotedebug"></a>Удаленная отладка веб-приложений
Если в подробном сообщении об ошибке содержится недостаточно сведений и вы не можете воспроизвести ошибку в локальной системе, попробуйте выполнить отладку в удаленном режиме. В этом режиме вы можете задавать точки останова, обращаться к памяти напрямую, осуществлять пошаговое выполнение кода и даже изменять путь кода.

Удаленная отладка не поддерживается в выпусках Visual Studio Express.

В этом разделе показано, как удаленно выполнить отладку с помощью проекта, созданного при изучении раздела [Создание веб-приложения ASP.NET в Azure](app-service-web-get-started-dotnet.md).

1. Откройте веб-проект, созданный после выполнения инструкций статьи [Создание веб-приложения ASP.NET в Azure](app-service-web-get-started-dotnet.md).

2. Откройте файл *Controllers\HomeController.cs*.

3. Удалите метод `About()` и вставьте вместо него следующий код.

``` c#
public ActionResult About()
{
    string currentTime = DateTime.Now.ToLongTimeString();
    ViewBag.Message = "The current time is " + currentTime;
    return View();
}
```
4. [Задайте точку останова](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) в строке `ViewBag.Message`.

5. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Опубликовать**.

6. Из раскрывающегося списка **Профиль** выберите профиль, который вы использовали при выполнении инструкций из статьи [Создание веб-приложения ASP.NET в Azure](app-service-web-get-started-dotnet.md). Затем щелкните "Параметры".

7. В диалоговом окне **Публикация** щелкните вкладку **Параметры** и измените значение **Конфигурация** на **Отладка**, затем щелкните **Сохранить**.

    ![Публикация в режиме отладки](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

8. Щелкните **Опубликовать**. Когда завершится развертывание и откроется URL-адрес Azure вашего веб-приложения, закройте браузер.

9. В **обозревателе сервера** щелкните правой кнопкой мыши веб-приложение и выберите **Подключить отладчик**.

    ![Подключить отладчик](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    В браузере автоматически откроется ваша главная страница, размещенная в Azure. Возможно, придется подождать около 20 секунд, пока Azure настраивает сервер для отладки. Эта задержка возникает только при первом запуске веб-приложения в режиме отладки в течение 48-часового периода. При повторном запуске отладки в рамках того же периода времени задержки не будет.

    > [!NOTE] 
    > При возникновении сложностей с запуском отладчика попробуйте сделать это с помощью **Cloud Explorer**, а не **обозревателя сервера**.
    >

10. Выберите пункт **О программе** в меню.

     Visual Studio прерывается в точке останова, при этом код выполняется в Azure, а не на локальном компьютере.

11. Наведите указатель мыши на переменную `currentTime` , чтобы просмотреть значение времени.

     ![Просмотр переменной в режиме отладки в Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

     Время, которое отображается здесь, является временем сервера Azure, который может находиться в другом часовом поясе, отличном от часового пояса локального компьютера.

12. Введите новое значение переменной `currentTime` , например "Теперь выполняется в Azure".

13. Нажмите клавишу F5, чтобы продолжить работу.

     На странице "О программе" в Azure отображается новое значение, введенное в переменной currentTime.

     ![Страница "О программе" с новым значением](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a> Удаленная отладка веб-заданий
В этом разделе демонстрируется удаленная отладка с использованием проекта и веб-приложения, созданного на этапе [Начало работы с пакетом SDK веб-заданий Azure](https://github.com/Azure/azure-webjobs-sdk/wiki).

Функции, приведенные в этом разделе, доступны только в Visual Studio 2013 с обновлением 4 или более поздней версии.

Удаленная отладка работает только с непрерывными веб-заданиями. Веб-задания по расписанию и по требованию не поддерживают отладку.

1. Откройте веб-проект, созданный в разделе [Информация о пакете SDK веб-заданий][GetStartedWJ].

2. В проекте ContosoAdsWebJob откройте *Functions.cs*.

3. [Установите точку останова](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) в первом операторе метода `GnerateThumbnail`.

    ![Задание точки останова](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. В **обозревателе решений** щелкните правой кнопкой мыши веб-проект (не проект веб-задания) и выберите **Опубликовать**.

5. В раскрывающемся списке **Профиль** выберите тот же профиль, который вы использовали в учебнике [Приступая к работе с пакетом SDK для веб-заданий Azure](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Щелкните вкладку **Параметры** и измените значение **Конфигурация** на **Отладка**, затем щелкните **Опубликовать**.

    Visual Studio развертывает веб-проекты и проекты веб-заданий и открывает в браузере URL-адрес Azure вашего веб-приложения.

7. В **обозревателе решений** разверните узлы **Azure > Служба приложений > ваша группа ресурсов > ваше веб-приложение > Веб-задания > Непрерывное**, а затем щелкните правой кнопкой мыши **ContosoAdsWebJob**.

8. Щелкните **Подключить отладчик**.

    ![Подключить отладчик](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    В браузере автоматически откроется ваша главная страница, размещенная в Azure. Возможно, придется подождать около 20 секунд, пока Azure настраивает сервер для отладки. Эта задержка возникает только при первом запуске веб-приложения в режиме отладки в течение 48-часового периода. При повторном запуске отладки в рамках того же периода времени задержки не будет.

9. В веб-браузере на главной странице Contoso Ads создайте новое объявление.

    Создание рекламы приводит к созданию сообщения очереди, которое забирается и обрабатывается веб-заданиями. Когда пакет SDK веб-заданий вызывает функцию для обработки сообщения очереди, выполнение кода прерывается в точке останова.

10. Когда отладчик останавливается в точке останова, вы можете проверить и изменить значения переменных, пока программа выполняется в облаке. На следующем рисунке отладчик показывает содержимое объекта blobInfo, который был передан в метод `GenerateThumbnail`.

     ![Объект blobInfo в отладчике](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Нажмите клавишу F5, чтобы продолжить работу.

     Метод `GenerateThumbnail` завершает создание эскиза.

12. В браузере обновите страницу индекса и посмотрите эскиз.

13. В Visual Studio нажмите сочетание клавиш SHIFT+F5, чтобы остановить отладку.

14. В **обозревателе сервера** щелкните правой кнопкой мыши узел ContosoAdsWebJob и щелкните **Просмотреть панель мониторинга**.

15. Выполните вход с использованием ваших учетных данных Azure и щелкните имя веб-задания, чтобы перейти к странице вашего веб-задания.

     ![Щелкните ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Панель мониторинга показывает, что недавно была выполнена функция `GenerateThumbnail`.

     (В следующий раз при нажатии кнопки **Представление панели мониторинга**не нужно выполнять вход, браузер перейдет непосредственно на страницу вашего веб-задания.)

16. Щелкните имя функции для просмотра подробных сведений о выполнении функции.

     ![Сведения о функции](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Если функция [записала журналы](https://github.com/Azure/azure-webjobs-sdk/wiki), для их просмотра щелкните пункт **ToggleOutput** .

## <a name="notes-about-remote-debugging"></a>Примечания о режиме удаленной отладки

* В рабочей среде выполнение в режиме отладки не рекомендуется. Если ваше рабочее веб-приложение не масштабировано до нескольких экземпляров сервера, отладка не позволит веб-серверу отвечать на другие запросы. Если имеется несколько экземпляров веб-сервера, то при подключении отладчика будет использован случайно выбранный экземпляр и не будет никакой возможности гарантировать, что последующие запросы браузера будут передаваться в тот же самый экземпляр. Кроме того, как правило, отладочная сборка не развертывается в производственной среде, а оптимизации компилятора для разворачиваемых сборок делают невозможным построковое отображения действий в исходном коде. Для устранения неполадок в рабочей среде лучше всего использовать журналы веб-сервера и трассировки приложения.
* Во время удаленной отладки избегайте длинных остановок на точках останова. Azure обрабатывает процесс, остановленный дольше, чем на несколько минут, как процесс, не отвечающий на запросы, и закрывает его.
* Во время отладки сервер отправляет данные в среду Visual Studio, что может повлиять на расходы, связанные с пропускной способностью. Сведения о тарифах на пропускную способность см. в разделе [Цены на Azure](https://azure.microsoft.com/pricing/calculator/).
* Убедитесь, что атрибут `debug` элемента `compilation` в файле *Web.config* имеет значение true. По умолчанию при публикации отладочной конфигурации сборки для него задано значение true.

``` xml
<system.web>
  <compilation debug="true" targetFramework="4.5" />
  <httpRuntime targetFramework="4.5" />
</system.web>
```
* Если вы обнаружите, что отладчик не осуществляет пошаговое выполнение кода, который требуется отладить, может потребоваться изменить параметр "Только мой код".  Дополнительные сведения см. в разделе [Ограничение выполнения шагов только моим кодом](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).
* При активации функции удаленной отладки на сервере запускается таймер, который автоматически отключает эту функцию по истечении 48 часов. Это 48-часовое ограничение установлено в целях повышения безопасности и производительности. Вы можете в любое время снова активировать эту функцию. Если вы не ведете отладку, эту функцию рекомендуется оставить отключенной.
* Вы можете вручную присоединить отладчик к любому процессу, а не только к процессу веб-приложения (w3wp.exe). Дополнительные сведения о том, как использовать режим отладки в Visual Studio, см. в разделе [Отладка в Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

## <a name="logsoverview"></a>Обзор журналов диагностики
Приложение ASP.NET, которое работает в веб-приложении Azure, может создавать следующие виды журналов:

* **Журналы трассировки приложения**<br/>
  Приложение создает эти журналы, вызывая методы класса [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx) .
* **Журналы веб-сервера**<br/>
  Веб-сервер создает запись журнала для каждого HTTP-запроса, адресованного сайту.
* **Подробные журналы сообщений об ошибках**<br/>
  Веб-сервер создает страницу HTML с некоторой дополнительной информацией об ошибочных HTTP-запросах (это запросы, которые приводят к появлению кода состояния 400 и выше).
* **Журналы трассировки неудачно завершенных запросов**<br/>
  Веб-сервер создает XML-файл с подробными сведениями трассировки ошибочных HTTP-запросов. Веб-сервер также предоставляет XSL-файл для форматирования XML-кода в браузере.

Ведение журнала влияет на производительность веб-приложения, поэтому Azure предоставляет возможность при необходимости включить или отключить каждый тип журнала. Для журналов приложений можно указать, что следует писать только журналы с уровнем серьезности выше заданного. При создании нового веб-приложения ведение журналов по умолчанию отключено.

Журналы записываются в файлы, содержащиеся в папке *LogFiles* файловой системы вашего веб-сайта, и доступны через FTP. Журналы приложений и журналы веб-сервера также могут записываться в учетную запись хранения Azure. В учетной записи хранения можно держать больший объем журналов, чем в файловой системе. При использовании файловой системы на журналы отводится 100 мегабайтов. Журналы файловой системы хранятся в течение короткого срока. При достижении установленного ограничения Azure удаляет старые файлы журналов, чтобы освободить место под новые.  

## <a name="apptracelogs"></a>Создание и просмотр журналов трассировки приложения
В этом разделе вам нужно выполнить следующие задачи.

* Добавление инструкций трассировки в веб-проект, созданный в разделе [Развертывание веб-приложения ASP.NET в службе приложений Azure с помощью Visual Studio][GetStarted].
* Просмотр журналов при локальном выполнении проекта.
* Просмотр журналов, создаваемых во время работы приложения в Azure.

Сведения о том, как создавать журналы приложений в веб-заданиях, см. в разделе [Работа с хранилищем очередей Azure с помощью пакета SDK веб-заданий — создание записей в журналах](https://github.com/Azure/azure-webjobs-sdk/wiki). Следующие инструкции для просмотра журналов и управления их хранением в Azure также применяются для журналов приложений, созданных с помощью Azure.

### <a name="add-tracing-statements-to-the-application"></a>Добавление инструкций трассировки в приложение
1. Откройте файл *Controllers\HomeController.cs* и замените методы `Index`, `About` и `Contact` приведенным ниже кодом, чтобы добавить инструкции `Trace` и инструкцию `using` для `System.Diagnostics`.

```c#
public ActionResult Index()
{
    Trace.WriteLine("Entering Index method");
    ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
    Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
    Trace.WriteLine("Leaving Index method");
    return View();
}

public ActionResult About()
{
    Trace.WriteLine("Entering About method");
    ViewBag.Message = "Your app description page.";
    Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
    Trace.WriteLine("Leaving About method");
    return View();
}

public ActionResult Contact()
{
    Trace.WriteLine("Entering Contact method");
    ViewBag.Message = "Your contact page.";
    Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
    Trace.WriteLine("Leaving Contact method");
    return View();
}        
```

2. Добавьте в начало файла инструкцию `using System.Diagnostics;` .

### <a name="view-the-tracing-output-locally"></a>Просмотр результатов трассировки локально
1. Нажмите F5, чтобы выполнить приложение в режиме отладки.

    Прослушиватель трассировки по умолчанию записывает весь вывод трассировки в окно **Вывод** вместе со всем остальным выводом отладки. На следующем рисунке показан вывод инструкций трассировки, добавленных в метод `Index` .

    ![Трассировка в окне отладки](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Ниже показано, как просмотреть выходные данные трассировки на веб-странице без компиляции в режиме отладки.
2. Откройте файл Web.config приложения (находится в папке проекта) и добавьте элемент `<system.diagnostics>` в конец файла непосредственно перед закрывающим элементом `</configuration>`:

``` xml
<system.diagnostics>
<trace>
  <listeners>
    <add name="WebPageTraceListener"
        type="System.Web.WebPageTraceListener,
        System.Web,
        Version=4.0.0.0,
        Culture=neutral,
        PublicKeyToken=b03f5f7f11d50a3a" />
  </listeners>
</trace>
</system.diagnostics>
```

`WebPageTraceListener` позволяет просматривать результаты трассировки, открыв `/trace.axd`.
3. Добавьте <a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">элемент трассировки</a> под `<system.web>` в файле Web.config, например так:

``` xml
<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
```       

4. Для запуска приложения нажмите сочетание клавиш CTRL+F5.
5. В адресной строке окна браузера добавьте *trace.axd* к URL-адресу и нажмите клавишу "ВВОД" (URL-адрес должен иметь вид http://localhost:53370/trace.axd)).
6. На странице **Трассировка приложения** щелкните **Просмотр сведений** в первой строке (не в строке BrowserLink).

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    Откроется страница **Сведения о запросе**, где в разделе **Сведения о трассировке** отображаются выходные данные инструкций трассировки, добавленных в метод `Index`.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    По умолчанию `trace.axd` доступен только локально. Если он должен быть доступен также в удаленном веб-приложении, можно добавить строку `localOnly="false"` в элемент `trace` файла *Web.config*, как показано в следующем примере.

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Тем не менее включение `trace.axd` в рабочем веб-приложении не рекомендуется по соображениям безопасности. В следующих разделах вы изучите более простой способ чтения журналов трассировки в веб-приложении Azure.

### <a name="view-the-tracing-output-in-azure"></a>Просмотр результатов трассировки в Azure
1. В **обозревателе решений** щелкните правой кнопкой веб-проект и выберите **Опубликовать**.
2. В диалоговом окне **Публикация веб-сайта** щелкните **Опубликовать**.

    После того как Visual Studio опубликует обновление, главная страница откроется в окне браузера (если не снят флажок **URL-адрес назначения** на вкладке **Подключение**).
3. В **обозревателе сервера** щелкните правой кнопкой мыши веб-приложение и выберите **Просмотр журналов потоковой передачи**.

    ![Просмотр журналов потоковой передачи в контекстном меню](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    В окне **Вывод** показано, что вы подключены к службе потоковой передачи журналов; каждую минуту, когда отсутствует журнал для отображения, появляется строка уведомления.

    ![Просмотр журналов потоковой передачи в контекстном меню](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. В окне браузера с открытой главной страницей щелкните пункт **Contact**.

    Через несколько секунд в окне `Contact`Вывод**появятся результаты трассировки уровня ошибки, добавленные в метод**.

    ![Трассировка ошибок в окне вывода](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio отображает только трассировки уровня ошибок, так как это настройка выставляется по умолчанию при включении службы мониторинга журналов. При создании нового веб-приложения Azure ведение всех журналов по умолчанию отключено. Вы могли убедиться в этом ранее при открытии страницы параметров сайта.

    ![Выключение ведение журнала приложения](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Однако при выборе **Просмотр журналов потоковой передачи** Visual Studio автоматически изменяет значение параметра **Ведение журнала приложений (файловая система)** на **Ошибка**, что означает представление только журналов уровня ошибок. Чтобы просмотреть все журналы трассировки, следует изменить значение этого параметра на **Подробно**. Если выбран уровень серьезности меньше уровня ошибок, начинают предоставляться и все журналы с более высокими уровнями серьезности. Поэтому при выборе подробного уровня вы также сможете просматривать журналы информационных сообщений, предупреждений и ошибок.  

1. В **обозревателе сервера** щелкните правой кнопкой мыши веб-приложение, затем щелкните **Просмотреть параметры**, как и раньше.
2. Измените значение параметра **Ведение журнала приложения (файловая система)** на **Подробно**, затем нажмите кнопку **Сохранить**.

    ![Уровень трассировки "Подробно"](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
3. В окне браузера, в котором теперь отображается страница **Contact** (Контактная информация), щелкните **Home** (Главная), затем **About** (О программе) и щелкните **Contact** (Контактная информация).

    Через несколько секунд в окне **Вывод** появятся все результаты трассировки.

    ![Подробные результаты трассировки](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    В этом разделе вы включили и отключили ведение журналов с помощью параметров веб-приложения Azure. Можно также включить и отключить прослушиватели трассировки, изменив содержимое файла Web.config. Но изменение файла Web.config приводит к перезапуску домена приложения, тогда как включение ведения журналов через настройку веб-приложения не приводит к таким результатам. Если воспроизведение проблемы занимает много времени или проблема возникает только периодически, перезапуск домена приложения может устранить неполадку, так что вам придется ждать следующего ее возникновения. Включение диагностики в Azure позволяет незамедлительно начать сбор сведений об ошибках, не перезапуская домен приложения.

### <a name="output-window-features"></a>Функции окна вывода
На вкладке **Журналы Microsoft Azure** окна **Вывод** расположены несколько кнопок и текстовое поле.

![Кнопки вкладки журналов](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Служат для выполнения следующих функций:

* Очистка окна **Вывод** .
* Включение или отключение переноса слов.
* Запуск или остановка журналов мониторинга.
* Определение журналов мониторинга.
* Скачивание журналов.
* Фильтрация журналов на основе регулярного выражения или строки поиска.
* Закрытие окна **Вывод** .

Если ввести строку поиска или регулярное выражение, Visual Studio фильтрует сведения в журналах на стороне клиента. Это означает, что можно ввести критерии после отображения журналов в окне **Вывод** , а также изменить критерии фильтрации без необходимости повторного создания журналов.

## <a name="webserverlogs"></a>Просмотр журналов веб-сервера
Журналы веб-сервера регистрируют все действия по протоколу HTTP в веб-приложении. Чтобы увидеть их в окне **Вывод**, включите их в веб-приложении и укажите Visual Studio отслеживать их.

1. На вкладке **Конфигурация веб-приложения Azure**, открытой в **обозревателе сервера**, измените значение параметра "Ведение журналов веб-сервера" на **Включено** и нажмите кнопку **Сохранить**.

    ![Включение ведения журналов веб-сервера](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. В окне **Вывод** нажмите кнопку **Выбор журналов Microsoft Azure для мониторинга**.

    ![Укажите, какие журналы Azure отслеживать](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. В диалоговом окне **Параметры ведения журнала Microsoft Azure** выберите **Журналы веб-сервера** и нажмите кнопку **ОК**.

    ![Мониторинг журналов веб-сервера](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. В окне браузера, в котором отображается веб-приложение, щелкните **Home** (Главная), затем **About** (О программе) и **Contact** (Контактная информация).

    Как правило, первыми отображаются журналы приложений, за которыми следуют журналы веб-сервера. Подождите некоторое время, чтобы дождаться отображения журналов.

    ![Журналы веб-сервера в окне вывода](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

По умолчанию при первом включении журналов веб-сервера с помощью Visual Studio Azure записывает журналы в файловой системе. В качестве альтернативы на портале Azure можно указать, что журналы веб-сервера должны записываться в контейнер больших двоичных объектов в учетной записи хранения.

Если включить ведение журналов веб-сервера для учетной записи хранения Azure с помощью портала, а затем отключить ведение журналов в Visual Studio, то при повторном включении ведения журналов в Visual Studio восстанавливаются параметры учетной записи хранения.

## <a name="detailederrorlogs"></a>Просмотр подробных журналов сообщений об ошибках
Подробные журналы сообщений об ошибках представляют некоторые дополнительные сведения о HTTP-запросах, которые завершаются с кодом ошибки (400 или выше). Чтобы увидеть их в окне **Вывод** , включите их в веб-приложении и дайте команду Visual Studio отслеживать их.

1. На вкладке **Конфигурация веб-приложения Azure**, открытой в **обозревателе сервера**, измените значение параметра **Подробные сообщения об ошибках** на **Включено**, затем нажмите кнопку **Сохранить**.

    ![Включение подробных сообщений об ошибках](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. В окне **Вывод** нажмите кнопку **Выбор журналов Microsoft Azure для мониторинга**.

3. В диалоговом окне **Параметры ведения журнала Microsoft Azure** выберите **Все журналы** и нажмите кнопку **ОК**.

    ![Отслеживание всех журналов](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. В адресной строке окна браузера добавьте дополнительный символ в URL-адрес, чтобы вызвать ошибку 404 (например, `http://localhost:53370/Home/Contactx`), и нажмите клавишу "ВВОД".

    После нескольких секунд в окне Visual Studio **Вывод** появится подробный журнал ошибок.

    ![Подробный журнал ошибок в окне "Вывод"](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Щелкните ссылку, удерживая нажатой клавишу CTRL, чтобы просмотреть форматированные выходные данные журнала в браузере:

    ![Подробный журнал ошибок в окне браузера](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Загрузка журналов файловой системы
Все журналы, которые можно отслеживать в окне **Вывод** , также можно загрузить в виде файла *ZIP* .

1. В окне **Вывод** щелкните **Загрузить журналы потоковой передачи**.

    ![Кнопки вкладки журналов](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    В проводнике открывается папка *Загрузки* с выбранным загруженным файлом.

    ![Загруженный файл](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Извлеките содержимое *ZIP* -файла, чтобы увидеть структуру папки, аналогичную следующей:

    ![Загруженный файл](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Журналы трассировки приложения хранятся в *TXT*-файлах в папке *LogFiles\Application*.
   * Журналы веб-сервера хранятся в *LOG*-файлах в папке *LogFiles\http\RawLogs*. Можно использовать средство, аналогичное [Log Parser](http://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) для просмотра и обработки этих файлов.
   * Подробные журналы сообщений об ошибках хранятся в *HTML*-файлах в папке *LogFiles\DetailedErrors*.

    (Папка *deployments* предназначена для файлов, созданных при публикации системы управления версиями; она никак не связана с публикациями Visual Studio. Папка *Git* предназначена для трассировок, относящихся к публикациям системы управления версиями и службы потоковой передачи файлов журналов.)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-create-storage-account.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](http://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="failedrequestlogs"></a>Просмотр журналов неудачно завершенных запросов
Журналы трассировки неудачно завершенных запросов полезны при просмотре подробных сведений по обработке IIS HTTP-запроса в таких сценариях, как перезапись URL-адреса, или при возникновении проблем, связанных с проблемами проверки подлинности.

Веб-приложения Azure используют те же функциональные возможности трассировки неудачно завершенных запросов, которые доступны в IIS, начиная с версии 7.0. Однако у вас нет доступа к параметрам IIS, которые определяют, какие ошибки будут регистрироваться. При включении трассировки неудачно завершенных запросов захватываются все ошибки.

Можно включить трассировку неудачно завершенных запросов с помощью Visual Studio, однако эту трассировку невозможно просмотреть в Visual Studio. Эти журналы представляют собой XML-файлы. Служба потоковой передачи журналов только отслеживает файлы, которые могут быть прочтены в режиме обычного текста: это *TXT*-, *HTML*- и *LOG*-файлы.

Журналы трассировки неудачно завершенных запросов можно просматривать напрямую в браузере через FTP или локально после загрузки их на локальный компьютер с помощью средства для работы с FTP. В этом разделе они будут просматриваться непосредственно в браузере.

1. На вкладке **Конфигурация** окна **Веб-приложение Azure**, открытого в **обозревателе сервера**, измените значение параметра **Трассировка неудачно завершенных запросов** на **Включено**, затем нажмите кнопку **Сохранить**.

    ![Включение трассировки неудачно завершенных запросов](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. В адресной строке окна браузера, в котором отображается веб-приложение, добавьте дополнительный символ в URL-адрес, чтобы вызвать ошибку 404, и нажмите клавишу "ВВОД".

    Это приведет к созданию журнала трассировки неудачно завершенных запросов. Ниже приведены действия, демонстрирующие, как просмотреть или загрузить журнал.

3. В Visual Studio на вкладке **Конфигурация** окна **Веб-приложение Azure** щелкните **Открыть на портале управления**.

4. На [портале Azure](https://portal.azure.com) на странице **Параметры** веб-приложения щелкните **Учетные данные развертывания**, а затем введите новые имя пользователя и пароль.

    ![Новое имя пользователя и пароль FTP](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Для входа необходимо использовать полное имя пользователя с префиксом в виде имени веб-приложения. Например, если ввести имя пользователя myid и сайт myexample, то полным именем будет myexample\myid.
    >

5. В новом окне браузера перейдите по URL-адресу, показанному в поле **Имя узла FTP** или **Имя узла FTPS** на странице **Обзор** для вашего веб-приложения.

6. Выполните вход с использованием учетных данных FTP, созданных ранее (включая имя веб-приложения в виде префикса к имени пользователя).

    В браузере отображается корневая папка веб-приложения.

7. Откройте папку *LogFiles* .

    ![Откройте папку LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Откройте папку с именем W3SVC плюс числовое значение.

    ![Откройте папку W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    Папка содержит XML-файлы, связанные с любыми ошибками, которые были зарегистрированы после включения трассировки неудачно завершенных запросов, а также XSL-файл, который может использоваться браузером для форматирования XML.

    ![Папка W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Щелкните XML-файл, связанный с неудачно завершенным запросом, данные трассировки которого необходимо просмотреть.

    На следующем рисунке показана часть сведений трассировки примерной ошибки.

    ![Трассировка неудачно завершенных запросов в браузере](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="nextsteps"></a>Дальнейшие действия
Вы видели, как Visual Studio позволяет легко просматривать журналы, созданные веб-приложением Azure. В следующих разделах содержатся ссылки на дополнительные ресурсы на связанные темы:

* Устранение неполадок веб-приложения Azure
* Отладка в Visual Studio
* Удаленная отладка в Azure
* Трассировка в приложениях ASP.NET
* Анализ журналов веб-сервера
* Анализ журналов трассировки неудачно завершенных запросов
* Отладка облачных служб

### <a name="azure-web-app-troubleshooting"></a>Устранение неполадок веб-приложения Azure
Дополнительную информацию об устранении неполадок, связанных с веб-приложениями в службе приложений Azure, см. на следующих ресурсах:

* [Мониторинг веб-приложений](/manage/services/web-sites/how-to-monitor-websites/)
* [Исследование проблемы утечки памяти в веб-приложениях Azure с использованием Visual Studio 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Запись блога Microsoft ALM о функциях Visual Studio для анализа проблем с управляемой памятью.
* [Онлайн-инструменты веб-приложений Azure, о которых вам следует знать](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Запись в блоге Амита Эппла (Amit Apple).

Справочные данные об определенном вопросе, связанном с устранением неполадок, можно получить, начав беседу на одном из следующих форумов:

* [Форум Azure на веб-сайте ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Форум Azure на портале MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Отладка в Visual Studio
Дополнительные сведения об использовании режима отладки в Visual Studio см. в статьях [Отладка в Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) и [Debugging Tips with Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx) (Рекомендации по отладке в Visual Studio 2010).

### <a name="remote-debugging-in-azure"></a>Удаленная отладка в Azure
Для получения дополнительных сведений об удаленной отладке для веб-приложений Azure и веб-заданий см. следующие ресурсы:

* [Общие сведения об удаленной отладке веб-приложений службы приложений Azure](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Общие сведения об удаленной отладке веб-приложений службы приложений Azure (часть 2) — подробнее об удаленной отладке](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Общие сведения об удаленной отладке веб-приложений службы приложений Azure (часть 3) — среда с несколькими экземплярами и GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Отладка веб-заданий (видео)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Если в вашем веб-приложении используется веб-API Azure или серверная часть мобильных служб, вы можете получить дополнительные сведения об отладке в разделе [Отладка серверной части .NET в Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Трассировка в приложениях ASP.NET
В Интернете отсутствуют обновленные и подробные вводные данные по трассировке ASP.NET. Лучшее, что можно сделать, это начать со старых вводных материалов, написанных для Web Forms, так как тогда MVC еще не существовало. А также дополнить эти сведения новыми постами из блогов, посвященных тем или иным конкретным вопросам. Можно также продуктивно начать изучение со следующих ресурсов:

* [Мониторинг и телеметрия (создание реальных облачных приложений в Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  Глава электронной книги с рекомендациями по трассировке в облачных приложениях Azure.
* [Трассировка ASP.NET](http://msdn.microsoft.com/library/ms972204.aspx)<br/>
  Старый, но по-прежнему хороший ресурс для введения в тему.
* [Прослушиватели трассировки](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  Сведения о прослушивателях трассировки, но без упоминания [WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Пошаговое руководство: интеграция трассировки ASP.NET с трассировкой System.Diagnostics](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  Это также старая статья, которая, однако, содержит некоторые дополнительные сведения, которые не освещаются во вводной статье.
* [Трассировка в представлениях ASP.NET MVC Razor](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Помимо трассировки в представлениях Razor, в этом посте также описывается, как создать фильтр ошибок, чтобы регистрировать все необработанные исключения в приложении MVC. Сведения о записи в журнал всех необработанных исключений в приложении Web Forms представлены в примере Global.asax в разделе [Полный пример для обработчиков ошибок](http://msdn.microsoft.com/library/bb397417.aspx) на сайте MSDN. Если в MVC или Web Forms понадобится зарегистрировать определенные исключения, но позволить платформе их обрабатывать, можно перехватить и повторно создать элементы, как показано на следующем примере:

``` c#
try
{
   // Your code that might cause an exception to be thrown.
}
catch (Exception ex)
{
    Trace.TraceError("Exception: " + ex.ToString());
    throw;
}
```

* [Потоковая передача диагностических журналов трассировки из командной строки Azure (включая Glimpse!)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Как использовать командную строку, чтобы сделать все, что показано в этом руководстве на примере Visual Studio. [Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) – это средство для отладки приложений ASP.NET.
* [Использование функций диагностики и ведения журнала для веб-приложений с Дэвидом Эббо (David Ebbo)](/documentation/videos/azure-web-site-logging-and-diagnostics/) и [Потоковая передача журналов из веб-приложений с Дэвидом Эббо (David Ebbo)](/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Авторы видео: Скотт Хансельман (Scott Hanselman) и Дэвид Эббо (David Ebbo).

Для ведения журнала ошибок вместо написания своего кода трассировки можно использовать платформу ведения журналов с открытым кодом, например [ELMAH](http://nuget.org/packages/elmah/). Дополнительные сведения см. в [записях блога Скотта Хансельмана (Scott Hanselman), посвященных ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Кроме того, не нужно использовать ASP.NET или трассировку `System.Diagnostics`, чтобы получить журналы потоковой передачи из Azure. Служба потоковой передачи журналов веб-приложения Azure будет передавать любые *TXT*-, *HTML*- или *LOG*-файлы, которые будут найдены в папке *LogFiles*. Поэтому можно создать собственную систему ведения журнала, которая записывает данные в файловую систему или веб-приложение, а файлы будут автоматически передаваться и скачиваться. Все, что нужно сделать, — это написать код приложения, который создает файлы в папке *d:\home\logfiles*.

### <a name="analyzing-web-server-logs"></a>Анализ журналов веб-сервера
Дополнительные сведения об анализе журналов веб-сервера см. на следующих ресурсах:

* [LogParser](http://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Инструмент для просмотра данных в журналах веб-сервера (*LOG* -файлов).
* [Устранение неполадок, связанных с производительностью IIS, или ошибок приложений с помощью LogParser](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Введение в средство LogParser, которое можно использовать для анализа журналов веб-сервера.
* [Записи блога Роберта Мак-Мюррея (Robert McMurray), посвященные использованию LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Код состояния HTTP в IIS 7.0, IIS 7.5 и IIS 8.0](http://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Анализ журналов трассировки неудачно завершенных запросов
Веб-сайт Microsoft TechNet содержит раздел [Using Failed Request Tracing](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) (Использование трассировки неудачно завершенных запросов), который может оказаться полезным для понимания того, как используются эти журналы. Но эта документация посвящена преимущественно настройке трассировки неудачно завершенных запросов в IIS, которую невозможно выполнить в веб-приложениях Azure.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
