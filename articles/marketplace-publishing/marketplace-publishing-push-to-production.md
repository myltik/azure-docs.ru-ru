<properties
   pageTitle="Развертывание предложения в Azure Marketplace | Microsoft Azure"
   description="Дополнительные сведения и пошаговое руководство по развертыванию предложения, например образа виртуальной машины, службы для разработчиков, службы данных и т. д., в Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="AzureStore"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio" />

# Развертывание предложения в Azure Marketplace
Если вы довольны своим предложением, например тестами клиентских сценариев, маркетинговым содержимым и т. д., и вы готовы к запуску, запросите **перемещение в рабочую среду** на вкладке **Публикация**.

1.	Эти 4 шага на странице пошагового руководства должны быть выполнены и отображены зеленым цветом.

  ![рисунок][img-pubportal-walkthru-checked]

2. Выберите вкладку **Публикация** из списка слева.

  ![рисунок][img-pubportal-menu-publish]

3. После выбора этого параметра будет показана страница, приведенная ниже. Щелкните параметр **Запросить утверждение для перемещения в рабочую среду**. После выполнения запроса команда утверждения выполнит окончательную проверку, и затем ваше предложение станет общедоступно в Marketplace. Состояние предложения будет изменено на **В списке**.

  ![рисунок][img-pubportal-publish-pushproduction]

> [AZURE.IMPORTANT]Всегда можно удалить предложение, пока оно находится в состоянии **черновик** (т. е. никогда не **перемещалось в промежуточную** или **рабочую среду**). На вкладке **Журнал** нажмите кнопку **Удалить черновик**, расположенную в нижней части страницы, чтобы удалить черновик.

> Репликация **образов виртуальных машин** между центрами обработки данных занимает от 24 до 48 часов. После завершения репликации предложение появится в [Azure Marketplace](http://azure.microsoft.com/marketplace).

## Дальнейшие действия
После вывода предложения в эксплуатацию протестируйте клиентские сценарии, чтобы проверить правильность работы всех контрактов и функциональных возможностей в рабочей среде, что было проверено и подтверждено в промежуточной среде.

## См. также
- [Приступая к работе: как опубликовать предложение в Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]: media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]: media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]: media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png

<!---HONumber=Oct15_HO3-->