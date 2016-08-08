<properties
	pageTitle="Как определить конечную точку настраиваемого интерфейса API в серверной мобильной службе .NET | Мобильные службы Azure"
	description="Узнайте, как определить конечную точку настраиваемого интерфейса API в серверной мобильной службе .NET."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="glenga"/>


# Как определить конечную точку настраиваемого интерфейса API в серверной мобильной службе .NET
> [AZURE.SELECTOR]
- [Серверная служба JavaScript](./mobile-services-javascript-backend-define-custom-api.md)
- [Сервер .NET.](./mobile-services-dotnet-backend-define-custom-api.md)

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Аналогичные сведения для мобильных приложений см. в разделе [Практическое руководство. Определение настраиваемого контроллера API](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-custom-api-controller).

В этом разделе показано, как определить конечную точку настраиваемого интерфейса API в серверной мобильной службе .NET. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки с функциональными возможностями сервера, но он не сопоставляет их с операциями вставки, обновления, удаления или чтения базы данных. Настраиваемый интерфейс API предоставляет больше возможностей для контроля над сообщениями, включая заголовки HTTP и формат текста сообщения.

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

Сведения о том, как вызывать настраиваемый интерфейс API в приложении с помощью клиентской библиотеки мобильных служб, см. в разделе [Вызов настраиваемого API](mobile-services-windows-dotnet-how-to-use-client-library.md#custom-api) в справочнике клиентского пакета SDK.


<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0727_2016-->