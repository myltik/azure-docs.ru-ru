<properties
   pageTitle="Управление приложениями Service Fabric в Visual Studio"
   description="Вы можете управлять приложениями и службами Service Fabric в Microsoft Azure с помощью Visual Studio."
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
   ms.date="04/14/2015"
   ms.author="jesseb"/>

# Управление приложениями Service Fabric в Visual Studio

Вы можете управлять приложениями и службами Service Fabric в Microsoft Azure с помощью Visual Studio. Настроив [свою среду разработки](../service-fabric-setup-your-development-environment), вы сможете использовать Visual Studio для создания приложений Service Fabric, добавления служб, создания пакетов, регистрации и развертывания приложений в кластере локальной разработки.

Для управления приложением Service Fabric щелкните правой кнопкой мыши проект приложения в обозревателе решений.

![Для управления приложением Service Fabric щелкните проект приложения правой кнопкой мыши.][manageservicefabric]

## Развертывание приложения Service Fabric

Развертывание приложения Service Fabric объединяет в одной простой операции такие действия:

1. Создание пакета приложения.
2. Загрузка пакета приложения в хранилище образов.
3. регистрация типа приложения;
4. Удаление любых запущенных экземпляров приложения.
5. Создание нового экземпляра.

Чтобы в Visual Studio развернуть приложение Service Fabric, щелкните его правой кнопкой мыши в **обозревателе решений** и выберите команду **Развернуть**. Вы можете развернуть приложение и с помощью клавиши **F5**. При этом к каждому его экземпляру будет подключен отладчик.

Чтобы удалить развернутое приложение, используйте команду **Удалить развертывание**. Все действия в рамках операции развертывания будут отменены.

1. Удаление отдельных запущенных экземпляров приложения.
2. Отмена регистрации типа приложения.
3. Удаление пакета приложения из хранилища образов.

## Добавление службы в приложение Service Fabric

Чтобы расширить функциональность приложения, вы можете добавить в него новые службы Fabric Services. Чтобы включить службу в пакет приложения, используйте для ее добавления команду меню **Создать службу Fabric Service**.

![Добавление новой службы Fabric Service в приложение.][newservice]

Выберите тип проекта Service Fabric, который нужно добавить в ваше приложение, и укажите имя службы. Рекомендации по выбору типа службы см. в статье [Выбор платформы для службы](service-fabric-choose-framework.md).

![Выбор типа проекта Fabric Service, который нужно добавить в приложение.][addserviceproject]

В решение и существующий пакет приложения будет добавлена новая служба. В манифест приложения будут добавлены ссылки на службу и используемый по умолчанию экземпляр службы. Служба будет создана и запущена при следующем развертывании приложения.

![Новая служба будет добавлена в манифест приложения.][newserviceapplicationmanifest]

## Создание пакета приложения Service Fabric

Чтобы развернуть приложение и входящие в него службы в кластере, необходимо создать пакет приложения. Пакет упорядочивает манифест приложения, манифесты служб и другие необходимые файлы определенным образом. Visual Studio размещает пакет в подкаталоге \\pkg, который находится в каталоге проекта приложения. Для создания и обновления пакета приложения щелкните элемент **Пакет**. Делать это лучше в том случае, если вы развертывали приложение с помощью пользовательских сценариев PowerShell.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия

- [Модель приложений Service Fabric](../service-fabric-application-model)
- [Развертывание приложений Service Fabric](../service-fabric-deploy-remove-applications)
- [Отладка приложений Service Fabric](../service-fabric-debugging-your-application)
- [Визуализация кластера с помощью обозревателя Service Fabric](../service-fabric-visualizing-your-cluster)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
 

<!---HONumber=July15_HO2-->