---
title: "Управление кэшем Redis с помощью Azure Explorer для IntelliJ | Документация Майкрософт"
description: "Узнайте, как управлять кэшами Redis Azure с помощью Azure Explorer для IntelliJ."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 06/14/2017
ms.author: robmcm
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 9ab8ae17ee2a92b5b16d2210366c00b5b8023fa8
ms.contentlocale: ru-ru
ms.lasthandoff: 07/28/2017

---

# <a name="managing-redis-caches-using-the-azure-explorer-for-intellij"></a>Управление кэшами Redis с помощью Azure Explorer для IntelliJ

Azure Explorer, входящий в состав набора средств Azure для IntelliJ, предоставляет разработчикам на Java удобное решение для управления кэшами Redis в их учетной записи Azure из интегрированной среды разработки IntelliJ.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-intellij-show-azure-explorer](../includes/azure-toolkit-for-intellij-show-azure-explorer.md)]

## <a name="create-a-redis-cache-by-using-intellij"></a>Создание кэша Redis с помощью IntelliJ

Приведенные ниже инструкции помогут вам поэтапно создать кэш Redis с использованием Azure Explorer.

1. Войдите в свою учетную запись Azure, следуя инструкциям из статьи [Инструкции по входу для набора средств Azure для IntelliJ].

1. В окне средства **Azure Explorer** разверните узел **Azure**, щелкните правой кнопкой мыши элемент **Кэши Redis** и выберите **Create Redis Cache** (Создать кэш Redis).

   ![Меню создания кэша Redis][CR01]

1. Когда отобразится диалоговое окно **New Redis Cache** (Новый кэш Redis), укажите значения для параметров ниже.

   ![Диалоговое окно New Redis Cache (Новый кэш Redis)][CR02]

   а. **DNS-имя**: определяет поддомен DNS для нового кэша Redis, который добавляется в начало redis.cache.windows.net, например: *wingtiptoys.redis.cache.windows.net*.

   b. **Подписка**: указывает подписку Azure, которую нужно использовать для нового кэша Redis.

   c. **Группа ресурсов**: указывает группу ресурсов для кэша Redis. Нужно выбрать один из следующих параметров:
      * **Создать**: указывает, что нужно создать группу ресурсов.
      * **Использовать существующую**: указывает, что будет выбрана группа ресурсов, связанная с учетной записью Azure.

   d. **Расположение**: указывает расположение, в котором создается кэш Redis (например, *западная часть США*).

   д. **Ценовая категория**: указывает ценовую категорию, которую использует кэш Redis. Этот параметр определяет число клиентских подключений. (Дополнительные сведения см. на [странице с ценами на кэш Redis].)

   f. **Порт без SSL**: указывает, разрешает ли кэш Redis соединения без использования SSL. По умолчанию разрешены только соединения SSL.

1. Когда будут указаны значения для всех параметров кэша Redis, нажмите кнопку **ОК**.

После создания кэша Redis он отображается в обозревателе Azure.

   ![Кэш Redis в обозревателе Azure][CR03]

> [!NOTE]
>
> Дополнительные сведения о настройке кэша Redis для Azure см. в статье [Настройка кэша Redis для Azure].
>

## <a name="display-the-properties-for-your-redis-cache-in-intellij"></a>Отображение свойств для кэша Redis в IntelliJ

1. В обозревателе Azure щелкните правой кнопкой мыши кэш Redis и выберите **Показать свойства**.

   ![Контекстное меню обозревателя Azure для отображения свойств кэша Redis][SP01]

1. В обозревателе Azure отобразятся свойства кэша Redis.

   ![Свойства кэша Redis][SP02]

## <a name="delete-your-redis-cache-by-using-intellij"></a>Удаление кэша Redis с помощью IntelliJ

1. В обозревателе Azure щелкните правой кнопкой мыши кэш Redis и выберите **Удалить**.

   ![Контекстное меню обозревателя Azure для удаления кэша Redis][DE01]

1. При появлении запроса выберите **Да**, чтобы удалить кэш Redis.

   ![Запрос на удаление кэша Redis][DE02]

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

Дополнительные сведения о кэшах Redis для Azure, параметрах конфигурации и ценах см. по следующим ссылкам:

* [кэш Azure Redis]
* [Документация по кэшу Redis]
* [странице с ценами на кэш Redis]
* [Настройка кэша Redis для Azure]

<!-- URL List -->

[странице с ценами на кэш Redis]: https://azure.microsoft.com/pricing/details/cache/
[кэш Azure Redis]: https://azure.microsoft.com/services/cache/
[Документация по кэшу Redis]: ./redis-cache/index.md
[Настройка кэша Redis для Azure]: ./redis-cache/cache-configure.md
[Инструкции по входу для набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md

<!-- IMG List -->

[CR01]: ./media/azure-toolkit-for-intellij-managing-redis-caches-using-azure-explorer/CR01.png
[CR02]: ./media/azure-toolkit-for-intellij-managing-redis-caches-using-azure-explorer/CR02.png
[CR03]: ./media/azure-toolkit-for-intellij-managing-redis-caches-using-azure-explorer/CR03.png

[SP01]: ./media/azure-toolkit-for-intellij-managing-redis-caches-using-azure-explorer/SP01.png
[SP02]: ./media/azure-toolkit-for-intellij-managing-redis-caches-using-azure-explorer/SP02.png

[DE01]: ./media/azure-toolkit-for-intellij-managing-redis-caches-using-azure-explorer/DE01.png
[DE02]: ./media/azure-toolkit-for-intellij-managing-redis-caches-using-azure-explorer/DE02.png

