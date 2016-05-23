<properties
	pageTitle="Непрерывная доставка в Azure с помощью Visual Studio Team Services | Microsoft Azure"
	description="Узнайте, как настроить командные проекты Visual Studio Team Services для автоматического выполнения сборки и развертывания в веб-приложения в службе приложений Azure и облачные службы."
	services="cloud-services"
	documentationCenter=".net"
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# Непрерывная доставка в Azure с использованием Visual Studio Team Services

Вы можете настроить командные проекты Visual Studio Team Services для автоматического выполнения сборки и развертывания в облачные службы или веб-приложения Azure. (Дополнительную информацию о настройке непрерывного процесса построения и развертывания системы с использованием *локального* решения Team Foundation Server см. в разделе [Непрерывная доставка для облачных служб в Azure](cloud-services-dotnet-continuous-delivery.md).)

В данном учебнике предполагается, что у вас установлены решения Visual Studio 2013 и пакет SDK Azure. Чтобы загрузить Visual Studio 2013, щелкните ссылку **Начните работу бесплатно** на сайте [www.visualstudio.com](http://www.visualstudio.com). Пакет SDK Azure можно установить по [этой ссылке](http://go.microsoft.com/fwlink/?LinkId=239540).

> [AZURE.NOTE] Для работы с этим учебником необходима учетная запись Visual Studio Team Services. Вы можете [бесплатно зарегистрировать учетную запись Visual Studio Team Services](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Чтобы настроить автоматическое выполнение сборки и развертывания облачной службы в Azure с использованием Visual Studio Team Services, выполните следующие действия.

## 1\. Создание командного проекта

Следуйте указаниям, приведенным [здесь](http://go.microsoft.com/fwlink/?LinkId=512980), чтобы создать командный проект и связать его с Visual Studio. В этом руководстве в качестве системы управления версиями применяется Team Foundation Version Control (TFVC). Если для управления версиями вы хотите использовать Git, см. [версию этого руководства для Git](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## 2\. Регистрация проекта в системе управления версиями

1. В Visual Studio откройте решение, которое вы хотите развернуть, или создайте новое. В этом пошаговом руководстве представлены инструкции по развертыванию веб-приложения или облачной службы (приложение Azure). Чтобы создать новое решение, создайте новый проект облачной службы Azure или новый проект MVC ASP.NET. Убедитесь, что в проекте используется платформа .NET Framework 4 или 4.5 и создается проект облачной службы, добавьте рабочую роль и веб-роль ASP.NET MVC, после чего выберите интернет-приложение для веб-роли. При появлении запроса выберите **Интернет-приложение**. Чтобы создать веб-приложение, выберите шаблон проекта "Веб-приложение ASP.NET" и затем выберите MVC. См. статью [Создание веб-приложения ASP.NET в службе приложений Azure](../app-service-web/web-sites-dotnet-get-started.md).

	> [AZURE.NOTE] В настоящее время Visual Studio Team Services поддерживают только развертывания непрерывной интеграции веб-приложений Visual Studio. Проекты веб-сайтов выходят за эти рамки.

1. Откройте контекстное меню решения и выберите **Добавить решение в систему управления версиями**.

	![][5]

1. Примите предлагаемые по умолчанию параметры или измените их, после чего нажмите кнопку **ОК**. По завершении обработки в **обозревателе решений** появится значок системы управления версиями.

	![][6]

1. Откройте контекстное меню решения и выберите пункт **Регистрация**.

	![][7]

1. В области **Ожидающие изменения** в обозревателе **Team Explorer** введите примечания к регистрации и нажмите кнопку **Регистрация**.

	![][8]

	Обратите внимание на варианты для включения или исключения определенных изменений при настройке. Если нужные изменения исключаются, нажмите ссылку **Включить все**.

	![][9]

## 3\. Подключение проекта к Azure

1. Теперь, когда создан командный проект VS Team Services с определенным исходным кодом, вы можете подключить его к Azure. Выполните вход на [классический портал Azure](http://go.microsoft.com/fwlink/?LinkID=213885), выберите существующую облачную службу или веб-приложение. Также можно создать новые объекты, нажав в нижнем левом углу значок **+**, а затем выбрав пункты **Облачная служба** или **Веб-приложение** и **Быстрое создание**. Щелкните ссылку **Настройка публикации в Visual Studio Team Services**.

	![][10]

1. В мастере введите имя учетной записи Visual Studio Team Services в соответствующем поле и щелкните ссылку **Авторизовать сейчас**. Может появиться запрос на вход в систему.

	![][11]

1. Во всплывающем диалоговом окне **Запрос подключения** нажмите кнопку **Принять**, чтобы предоставить Azure полномочия для настройки командного проекта в VS Team Services.

	![][12]

1. После успешной авторизации появится раскрывающийся список, в котором будут представлены ваши командные проекты Visual Studio Team Services. Щелкните имя созданного на предыдущих шагах командного проекта и нажмите кнопку с галочкой в мастере.

	![][13]

1. После привязки проекта появятся инструкции по проверке изменений в командном проекте Visual Studio Team Services. При следующем возврате данных после изменения Visual Studio Team Services выполнит сборку и развертывание вашего проекта в Azure. Чтобы проверить реализованное поведение, щелкните ссылку **Регистрация из Visual Studio**, а затем ссылку **Запустить Visual Studio** (или аналогичную кнопку **Visual Studio** в нижней части окна портала).

	![][14]

## 4\. Запуск процессов повторного построения и развертывания проекта

1. В обозревателе **Visual Studio Team Explorer** щелкните ссылку **Обозреватель управления исходным кодом**.

	![][15]

1. Перейдите к файлу решения и откройте его.

	![][16]

1. Откройте и измените файл в **обозревателе решений**. Измените, например, файл `_Layout.cshtml` в каталоге Views\\Shared веб-роли MVC.

	![][17]

1. Измените логотип сайта и сохраните этот файл с помощью клавиш **CTRL + S**.

	![][18]

1. В обозревателе **Team Explorer** щелкните ссылку **Ожидающие изменения**.

	![][19]

1. Введите комментарий и нажмите кнопку **Регистрация**.

	![][20]

1. Нажмите кнопку **Главная**, чтобы вернуться на главную страницу **Team Explorer**.

	![][21]

1. Щелкните ссылку **Сборки**, чтобы просмотреть выполняющиеся сборки.

	![][22]

	В **Team Explorer** отображается информация о том, что для вашего возврата запущен процесс сборки.

	![][23]

1. Дважды щелкните имя выполняемой сборки, чтобы просмотреть подробный журнал процесса построения.

	![][24]

1. В процессе построения вы можете просмотреть определение сборки, созданное во время привязки TFS к Azure с помощью мастера. Откройте контекстное меню определения сборки и выберите **Редактировать определение сборки**.

	![][25]

	На вкладке **Триггер** указывается, что в определении сборки настроено выполнение сборки по умолчанию при каждой регистрации.

	![][26]

	На вкладке **Процесс** указывается, что в среде разработки настроено имя ваших облачной веб-службы или веб-сайта. При работе с веб-приложениями здесь будут показаны другие свойства.

	![][27]

1. При необходимости измените установленные по умолчанию значения свойств. Свойства публикации Azure находятся в разделе **Развертывание**.

	В следующей таблице приводятся доступные свойства в разделе **Развертывание**:

	|Свойство|Значение по умолчанию|
	|---|---|
	|Разрешить недоверенные сертификаты|Если установлено значение false, то SSL-сертификаты должны быть подписаны корневым центром сертификации.|
	|Разрешить обновление|Разрешает обновление существующего развертывания вместо создания нового. Сохраняет IP-адрес.|
	|Не удалять|Если установлено значение true, не разрешена перезапись существующего несвязанного развертывания (обновление разрешено).|
	|Путь к параметрам развертывания|Путь к PUBXML-файлу для веб-приложения относительно корневой папки репозитория. Для облачных служб игнорируется.|
	|Среда развертывания Sharepoint|Совпадает с именем службы.|
	|Среда развертывания Azure|Имя веб-приложения или облачной службы|

1. Если используется несколько конфигураций служб (CSCFG-файлов), можно указать желаемую конфигурацию службы в параметрах **Сборка, Дополнительно, Аргументы MSBuild**. Например, чтобы использовать файл ServiceConfiguration.Test.cscfg, задайте аргумент командной строки MSBuild `/p:TargetProfile=Test`.

	![][38]

	К этому моменту процесс построения должен быть успешно завершен.

	![][28]

1. Если дважды щелкнуть имя сборки, Visual Studio отобразит **сводку сборки**, включающую все результаты тестирования из соответствующих проектов модульного теста.

	![][29]

1. На [классическом портале Azure](http://go.microsoft.com/fwlink/?LinkID=213885) можно увидеть соответствующее развертывание на вкладке **Развертывания**, выбрав промежуточную среду.

	![][30]

1.	Перейдите по URL-адресу вашего сайта. Для веб-приложения достаточно нажать кнопку **Обзор** на панели команд. Для облачной службы выберите URL-адрес в разделе **Сводка** на странице **Панель мониторинга**, где отображается промежуточная среда для облачной службы. Развертывания из решений непрерывной интеграции для облачных служб по умолчанию публикуются в промежуточной среде. Чтобы изменить это поведение, присвойте свойству **Альтернативная среда облачной службы** значение **Рабочая**. На этом снимке экрана показано местонахождение URL-адреса сайта на странице панели мониторинга облачной службы:

	![][31]

	Сайт откроется на новой вкладке браузера.

	![][32]

	При внесении других изменений в проект для облачных служб запускаются дополнительные процессы построения, в результате чего создается несколько развертываний, последнее из которых отмечается как активное.

	![][33]

## 5\. Повторное развертывание предыдущей сборки

Этот шаг необязателен и применяется к облачным службам. Чтобы восстановить состояние сайта на момент более ранней версии, выберите на классическом портале Azure какое-либо предыдущее развертывание и нажмите кнопку **Повторное развертывание**. Обратите внимание, что при этом в TFS будет запущен новый процесс сборки, а в историю развертывания будет добавлена новая запись.

![][34]

## 6\. Изменение развертывания в рабочей среде

Этот шаг применяется только к облачным службам и недоступен для веб-приложений. Выполнив все необходимые действия, можно повысить промежуточную среду до рабочей с помощью кнопки **Переключить** классического портала Azure. Вновь развернутая промежуточная среда повышается до уровня рабочей, а ранее установленная рабочая среда становится промежуточной. Рабочая и промежуточная среды могут использовать разные активные развертывания, однако история развертывания последних сборок будет содержать одинаковые записи независимо от выбранной среды.

![][35]

## 7\. Выполнение модульных тестов

Этот шаг применяется только к веб-приложениям, не облачным службам. Чтобы поместить условия качества вашего развертывания, можно выполнять модульные тесты и, если они не могут остановить развертывание.

1.  В Visual Studio добавьте проект модульного теста.

	![][39]

1.  Добавление ссылки на проект в проект, который требуется проверить.

	![][40]

1.  Добавьте модульные тесты. Сначала попробуйте фиктивный тест, который всегда выдает положительный результат.

		```
		using System;
		using Microsoft.VisualStudio.TestTools.UnitTesting;

		namespace UnitTestProject1
		{
		    [TestClass]
		    public class UnitTest1
		    {
		        [TestMethod]
		        [ExpectedException(typeof(NotImplementedException))]
		        public void TestMethod1()
		        {
		            throw new NotImplementedException();
		        }
		    }
		}
		```

1.  Измените определение сборки, выберите вкладку **Процессы** и разверните узел **Test**.

1.  Установите для параметра **Завершить сборку при ошибке теста** значение True. Это означает, что развертывание не будет происходить, если тест не будет пройден положительно.

	![][41]

1.  Поставьте новую сборку в очередь.

	![][42]

	![][43]

1. По мере обработки сборки проверяйте ход выполнения.

	![][44]

	![][45]

1. По завершению сборки проверьте результаты теста.

	![][46]

	![][47]

1.  Попробуйте создать тест, который выдаст отрицательный результат. Добавьте новый тест путем копирования первой, переименовать его и закомментируйте строку кода, о том, что NotImplementedException является ожидаемое исключение.

		```
		[TestMethod]
		//[ExpectedException(typeof(NotImplementedException))]
		public void TestMethod2()
		{
		    throw new NotImplementedException();
		}
		```

1. Примените изменения, чтобы поставить в очередь новую сборку.

	![][48]

1. Просмотрите результаты теста и подробности отрицательного результата.

	![][49]

	![][50]

## Дальнейшие действия
Дополнительную информацию о модульном тестировании в Visual Studio Team Services см. в разделе [Выполнение тестов в процессе сборки](http://go.microsoft.com/fwlink/p/?LinkId=510474). При использовании Git, см. разделы [Совместное использование кода в Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) и [Непрерывное развертывание с использованием GIT в службе приложений Azure](../app-service-web/web-sites-publish-source-control.md). Дополнительную информацию о Visual Studio Team Services см. в разделе [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG

<!---HONumber=AcomDC_0511_2016-->