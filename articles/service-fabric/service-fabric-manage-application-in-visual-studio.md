<properties
   pageTitle="Управление приложениями в Visual Studio | Microsoft Azure"
   description="Используйте Visual Studio для создания, разработки, упаковки, развертывания и отладки приложений и служб Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="jesseb"/>

# Использование Visual Studio для упрощения создания приложениями Service Fabric и управления ими

Вы можете управлять приложениями и службами Azure Service Fabric с помощью Visual Studio. Настроив [свою среду разработки](service-fabric-get-started.md), вы сможете использовать Visual Studio для создания приложений Service Fabric, добавления служб, создания пакетов, регистрации и развертывания приложений в кластере локальной разработки.

Для управления приложением щелкните правой кнопкой мыши проект приложения в обозревателе решений.

![Для управления приложением Service Fabric щелкните проект приложения правой кнопкой мыши.][manageservicefabric]

## Развертывание приложения Service Fabric

Развертывание приложения объединяет в одной простой операции такие действия:

1. Создание пакета приложения.
2. Загрузка пакета приложения в хранилище образов.
3. регистрация типа приложения;
4. Удаление любых запущенных экземпляров приложения.
5. Создание нового экземпляра.

В Visual Studio вы можете развернуть приложение и с помощью клавиши **F5**. При этом к каждому его экземпляру будет подключен отладчик. Вы можете воспользоваться сочетанием **CTRL + F5**, чтобы развернуть приложение без отладки, или опубликовать его в локальном или удаленном кластере с помощью профиля публикации. Дополнительные сведения см. в статье [Публикация приложения в удаленном кластере с помощью Visual Studio](service-fabric-publish-app-remote-cluster.md).

### Сохранение данных между тестовыми запусками

Зачастую тестирование служб выполняется локально путем добавления входных данных тестирования, изменения нескольких блоков кода и повторного выполнения локальной отладки. Инструменты Visual Studio Service Fabric включают удобное свойство **Сохранять данные при запуске**, с помощью которого можно сохранить данные, введенные в предыдущем сеансе, и использовать их повторно.

#### Включение свойства "Сохранять данные при запуске"

1. В контекстном меню проекта приложения выберите пункт **Свойства** (или нажмите клавишу **F4**).
1. В окне **Свойства** задайте для свойства **Сохранять данные при запуске** значение **Да**.

	![Настройка свойства "Сохранять данные при запуске"][preservedata]

При повторном запуске приложения сценарий развертывания будет считать развертывание обновлением в неотслеживаемом автоматическом режиме, что позволит быстро обновить приложение до новой версии с добавлением строки даты. Процесс обновления сохраняет все данные, введенные в предыдущем сеансе отладки.

![Пример новой версии приложения с добавленной датой][preservedate]

Сохранение данных выполняется с помощью функции обновления из платформы Service Fabric. Дополнительные сведения об обновлении приложения см. в статье [Обновление приложения Service Fabric](service-fabric-application-upgrade.md).

## Добавление службы в приложение Service Fabric

Чтобы расширить функциональность приложения, вы можете добавить в него новые службы Fabric Services. Чтобы включить службу в пакет приложения, используйте для ее добавления команду меню **Создать службу Fabric Service**.

![Добавление новой службы Fabric Service в приложение][newservice]

Выберите тип проекта Service Fabric, который нужно добавить в ваше приложение, и укажите имя службы. Рекомендации по выбору типа службы см. в статье [Выбор платформы для службы](service-fabric-choose-framework.md).

![Выбор типа проекта Fabric Service, который нужно добавить в приложение.][addserviceproject]

В решение и существующий пакет приложения будет добавлена новая служба. В манифест приложения будут добавлены ссылки на службу и используемый по умолчанию экземпляр службы. Служба будет создана и запущена при следующем развертывании приложения.

![Новая служба будет добавлена в манифест приложения.][newserviceapplicationmanifest]

## Создание пакета приложения Service Fabric

Чтобы развернуть приложение и входящие в него службы в кластере, необходимо создать пакет приложения. Пакет упорядочивает манифест приложения, манифесты служб и другие необходимые файлы определенным образом. Visual Studio размещает пакет в подкаталоге \\pkg, который находится в каталоге проекта приложения. Для создания и обновления пакета приложения щелкните пункт **Пакет** в контекстном меню **Приложение**. Делать это лучше в том случае, если вы развертывали приложение с помощью пользовательских сценариев PowerShell.

## Удаление приложения

Отменить подготовку типа приложения из локального кластера вы можете с помощью обозревателя Service Fabric. Обозреватель кластера доступен из конечной точки шлюза HTTP кластера (обычно 19080 или 19007), например http://localhost:19080/Explorer. При этом все описанные выше действия по развертыванию будут отменены.

1. Удаление отдельных запущенных экземпляров приложения.
2. Отмена регистрации типа приложения.

![Удаление приложения](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия

- [Модель приложений Service Fabric](service-fabric-application-model.md)
- [Развертывание приложений Service Fabric](service-fabric-deploy-remove-applications.md)
- [Управление параметрами приложения для нескольких сред](service-fabric-manage-multiple-environment-app-configuration.md)
- [Отладка приложений Service Fabric](service-fabric-debugging-your-application.md)
- [Визуализация кластера с помощью обозревателя Service Fabric](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]: ./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[preservedate]: ./media/service-fabric-manage-application-in-visual-studio/preservedate.png

<!---HONumber=AcomDC_0204_2016-->