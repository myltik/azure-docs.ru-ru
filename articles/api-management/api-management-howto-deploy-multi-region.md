<properties
	pageTitle="Развертывание экземпляра службы управления Azure API в различных регионах Azure"
	description="Дополнительные сведения о развертывании экземпляра службы управления Azure API в различных регионах Azure." 
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/09/2016"
	ms.author="sdanie"/>

# Развертывание экземпляра службы управления Azure API в различных регионах Azure

Служба управления API поддерживает развертывание в нескольких регионах, что позволяет издателям API распространять единую службу управления API в любых требуемых регионах Azure. Это сокращает задержки, связанные с географической удаленностью потребителей API, а также повышает доступность службы, когда какой-либо из регионов переходит в автономный режим.

При создании службы управления API она содержит только одну [единицу][] и располагается в одном регионе Azure, который считается первичным. Однако через классический портал Azure можно легко добавить дополнительные регионы. В каждом регионе развертывается сервер шлюза управления API, и весь трафик вызовов направляется на ближайший из таких шлюзов. Если регион переходит в автономный режим, трафик автоматически перенаправляется к другому ближайшему шлюзу.

> [AZURE.IMPORTANT] Развертывание в нескольких регионах доступно только для уровня **[Premium][]**.

## <a name="add-region"> </a>Создание экземпляра службы управления API в новом регионе

Для начала на классическом портале Azure для службы управления API щелкните **Управление**. Будет открыт портал издателя службы управления API.

![Портал издателя][api-management-management-console]

>Если экземпляр службы API Management еще не создан, см. раздел [Создание экземпляра службы API Management][] в руководстве [Начинаем работу с API Management][].

Перейдите на вкладку **Масштаб** на классическом портале Azure для вашего экземпляра службы управления API.

![Вкладка "Масштаб"][api-management-scale-service]

Чтобы выполнить развертывание в новом регионе, откройте раскрывающийся список под названием первичного региона и выберите дополнительный регион.

![Добавление региона][api-management-add-region]

После выбора региона задайте число единиц для него из раскрывающегося списка.

![Указание единиц][api-management-select-units]

После настройки желаемого региона и единиц нажмите кнопку **Сохранить**.

## <a name="remove-region"> </a>Удаление экземпляра службы управления API из региона

Чтобы удалить экземпляр службы управления API из отдельного региона, перейдите на вкладку **Масштаб** классического портала Azure для вашего экземпляра службы управления API.

![Вкладка "Масштаб"][api-management-scale-service]

Щелкните **X** справа от региона, который нужно удалить.

![Удаление региона][api-management-remove-region]

После удаления региона щелкните **Сохранить**.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Создание экземпляра службы API Management]: api-management-get-started.md#create-service-instance
[Начинаем работу с API Management]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[единицу]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

<!---HONumber=AcomDC_0810_2016-->