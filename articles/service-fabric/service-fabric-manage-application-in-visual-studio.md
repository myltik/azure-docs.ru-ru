---
title: "Управление приложениями в Visual Studio | Документация Майкрософт"
description: "Используйте Visual Studio для создания, разработки, упаковки, развертывания и отладки приложений и служб Service Fabric."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: seanmck;mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 60d440c75d6352d5e65e0158e439df9db2315ecd
ms.openlocfilehash: 70c393f1185844bb26ff1f89cb69cb06b51fc155


---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Использование Visual Studio для упрощения создания приложениями Service Fabric и управления ими
Вы можете управлять приложениями и службами Azure Service Fabric с помощью Visual Studio. Настроив [свою среду разработки](service-fabric-get-started.md), вы сможете использовать Visual Studio для создания приложений Service Fabric, добавления служб, создания пакетов, регистрации и развертывания приложений в кластере локальной разработки.

## <a name="deploy-your-service-fabric-application"></a>Развертывание приложения Service Fabric
По умолчанию развертывание приложения объединяет в одной простой операции такие действия:

1. Создание пакета приложения.
2. Загрузка пакета приложения в хранилище образов.
3. регистрация типа приложения;
4. Удаление любых запущенных экземпляров приложения.
5. Создание нового экземпляра.

В Visual Studio вы можете развернуть приложение и с помощью клавиши **F5**. При этом к каждому его экземпляру будет подключен отладчик. Вы можете воспользоваться сочетанием **CTRL+F5**, чтобы развернуть приложение без отладки, или опубликовать его в локальном или удаленном кластере с помощью профиля публикации. Дополнительные сведения см. в статье [Публикация приложения на удаленный кластер с помощью Visual Studio](service-fabric-publish-app-remote-cluster.md).

### <a name="application-debug-mode"></a>Режим отладки приложения
По умолчанию Visual Studio удаляет имеющиеся экземпляры типа приложения после остановки отладки или (если вы развернули приложение без подключения отладчика) после повторного развертывания приложения. В этом случае все данные приложения будут удалены. При локальной отладке может потребоваться сохранить созданные ранее данные при тестировании новой версии приложения, продолжить выполнение приложения или использовать последующие сеансы отладки для обновления приложения. В средствах Service Fabric для Visual Studio доступно свойство **Режим отладки приложения**, которое управляет поведением приложения после нажатия клавиши **F5**. Можно указать, чтобы после завершения сеанса отладки приложение было удалено или продолжало выполняться, либо разрешить обновление приложения в последующих сеансах отладки вместо его удаления и повторного развертывания.

#### <a name="to-set-the-application-debug-mode-property"></a>Настройка свойства "Режим отладки приложения"
1. В контекстном меню проекта приложения выберите пункт **Свойства** (или нажмите клавишу **F4**).
2. В окне **Свойства** укажите значение для свойства **Режим отладки приложения**.

    ![Настройка свойства "Режим отладки приложения"][debugmodeproperty]

Ниже приведены возможные значения свойства **Режим отладки приложения** .

1. **Автообновление.** Приложение продолжает выполняться после завершения сеанса отладки. При следующем нажатии клавиши **F5** развертывание будет обработано как обновление в неотслеживаемом автоматическом режиме. Это позволит быстро обновить приложение до новой версии с добавлением строки даты. Процесс обновления сохраняет все данные, введенные в предыдущем сеансе отладки.
2. **Сохранить приложение.** После завершения сеанса отладки приложение продолжает выполняться в кластере. При следующем нажатии клавиши **F5** приложение будет удалено, а в кластере будет развернуто новое созданное приложение.
3. **Удалить приложение.** По завершении сеанса отладки приложение будет удалено.

В режиме **Автообновление** сохранение данных выполняется с помощью функций обновления Service Fabric, но они настроены для оптимизации производительности, а не обеспечения безопасности. Дополнительные сведения об обновлении приложений и выполнении обновления в реальной среде см. в статье [Обновление приложения Service Fabric](service-fabric-application-upgrade.md).

![Пример новой версии приложения с добавленной датой][preservedata]

> [!NOTE]
> Это свойство доступно в средствах Service Fabric для Visual Studio, начиная с версии 1.1. В версии ниже 1.1 используйте свойство **Сохранять данные при запуске** , чтобы добиться такого же поведения. Параметр "Сохранить приложение" появился в версии 1.2 средств Service Fabric для Visual Studio.
>
>

## <a name="add-a-service-to-your-service-fabric-application"></a>Добавление службы в приложение Service Fabric
Чтобы расширить функциональность приложения, вы можете добавить в него новые службы.  Чтобы включить службу в пакет приложения, используйте для ее добавления команду меню **Создать службу Fabric Service** .

![Добавление новой службы Fabric Service в приложение][newservice]

Выберите тип проекта Service Fabric, который нужно добавить в ваше приложение, и укажите имя службы.  Рекомендации по выбору типа службы см. в статье [Общие сведения о модели программирования Service Fabric](service-fabric-choose-framework.md).

![Выбор типа проекта Fabric Service, который нужно добавить в приложение.][addserviceproject]

В решение и существующий пакет приложения будет добавлена новая служба. В манифест приложения будут добавлены ссылки на службу и используемый по умолчанию экземпляр службы. Служба будет создана и запущена при следующем развертывании приложения.

![Новая служба будет добавлена в манифест приложения.][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Создание пакета приложения Service Fabric
Чтобы развернуть приложение и входящие в него службы в кластере, необходимо создать пакет приложения.  Пакет упорядочивает манифест приложения, манифесты служб и другие необходимые файлы определенным образом.  Visual Studio размещает пакет в подкаталоге \pkg, который находится в каталоге проекта приложения.  Чтобы создать или обновить пакет приложения, в контекстном меню **Приложение** выберите пункт **Пакет**.  Делать это лучше в том случае, если вы развертывали приложение с помощью пользовательских сценариев PowerShell.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Удаление приложений и типов приложений с использованием Cloud Explorer
Вы можете выполнить основные операции управления кластером из среды Visual Studio, используя Cloud Explorer, который можно запустить в меню **Просмотр** . Например, вы можете удалить приложения и отменить подготовку типов приложений на локальном или удаленном кластерах.

![Удаление приложения](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

> [!TIP]
> Сведения о расширенных функциях управления кластером см. в статье [Визуализация кластера с помощью Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Дальнейшие действия
* [Модель приложений Service Fabric](service-fabric-application-model.md)
* [Развертывание приложений Service Fabric](service-fabric-deploy-remove-applications.md)
* [Управление параметрами приложения для нескольких сред](service-fabric-manage-multiple-environment-app-configuration.md)
* [Отладка приложений Service Fabric](service-fabric-debugging-your-application.md)
* [Визуализация кластера с помощью обозревателя Service Fabric](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]:./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png



<!--HONumber=Jan17_HO1-->


