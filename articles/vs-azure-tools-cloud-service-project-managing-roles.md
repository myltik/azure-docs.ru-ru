<properties
   pageTitle="Управление ролями в проектах облачных служб Azure с помощью Visual Studio | Microsoft Azure"
   description="Узнайте, как добавить роли в проект облачной службы Azure или удалить из него существующие роли с помощью Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/08/2015"
   ms.author="kempb" />

# Управление ролями в проектах облачных служб Azure с помощью Visual Studio

После создания проекта облачной службы Azure можно добавить в него новые роли или удалить из него существующие. Вы можете импортировать существующий проект и преобразовать его в роль. Например, можно импортировать веб-приложение ASP.NET и использовать его в качестве веб-роли.

## Добавление и удаление ролей

**Чтобы добавить роль:**

В **обозревателе решений** откройте контекстное меню узла **Роли** в проекте облачной службы и выберите команду **Добавить**. Вы можете выбрать существующую веб-роль или рабочую роль в текущем решении или создать новый проект рабочей или веб-роли. Кроме того, вы можете выбрать проект, например проект веб-приложения ASP.NET, и связать его с проектом роли.

**Чтобы удалить связь роли:**

В узле **Роли** проекта облачной службы в обозревателе решений откройте контекстное меню роли, которую требуется удалить, и выберите команду **Удалить**.

## Удаление и добавление ролей в облачной службе

Если вы удалите роль из своего проекта облачной службы, но позже захотите снова добавить ее в проект, то будут добавлены только объявления роли и ее основные атрибуты, такие как сведения диагностики и информация о конечных точках. В файлы ServiceDefinition.csdef или ServiceConfiguration.cscfg не добавляются другие ресурсы и ссылки. Если вы хотите добавить эту информацию в файлы, то придется сделать это вручную.

Предположим, вы удалили роль веб-службы, а затем решили вернуть ее обратно в решение. Если вы сделаете это, произойдет ошибка. Чтобы ее избежать, необходимо добавить элемент `<LocalResources>`, показанный в следующем XML-блоке, обратно в файл ServiceDefinition.csdef. Используйте имя роли веб-службы, добавленной в проект, в составе атрибута name в элементе **<LocalStorage>**. В этом примере используется роль веб-службы с именем **WCFServiceWebRole1**.

	<WebRole name="WCFServiceWebRole1">
	    <Sites>
	      <Site name="Web">
	        <Bindings>
	          <Binding name="Endpoint1" endpointName="Endpoint1" />
	        </Bindings>
	      </Site>
	    </Sites>
	    <Endpoints>
	      <InputEndpoint name="Endpoint1" protocol="http" port="80" />
	    </Endpoints>
	    <Imports>
	      <Import moduleName="Diagnostics" />
	    </Imports>
	   <LocalResources>
	      <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
	   </LocalResources>
	</WebRole>

## Дальнейшие действия

Сведения о настройке ролей в Visual Studio см. в статье [Настройка ролей для облачной службы Azure с помощью Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

<!---HONumber=Sept15_HO3-->