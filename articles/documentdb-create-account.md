<properties title="How to Create a DocumentDB Account" pageTitle="Создание учетной записи базы данных | Azure" description="Узнайте, как создать учетную запись базы данных NoSQL использования documentdb по плану документа и выберите параметры учетной записи на портале предварительной версии Azure."  metaKeywords="NoSQL, DocumentDB, database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="mimig" manager="jhubbard" editor="monicar" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/09/2014" ms.author="mimig" />

#Создание учетной записи базы данных
Для использования Microsoft Azure DocumentDB вы должны создать учетную запись DocumentDB.  В этом разделе описывается создание учетной записи DocumentDB на портале управления предварительным просмотром Azure.  


1.	Войдите на [портал управления предварительным просмотром](https://portal.azure.com/#gallery/Microsoft.DocumentDB).
2.	Нажмите Создать -> Учетная запись DocumentDB.  
	![][1]  

	Кроме того, с начальной панели вы можете просматривать Azure Marketplace, для этого выберите категорию "Данные + аналитика", выберите **DocumentDB**, а затем нажмите **Создать**.  

	![][2]   

3. В выноске **Новый DocumentDB (предварительный просмотр)** задайте желаемую конфигурацию учетной записи DocumentDB. 
 
	![][3] 


	- В поле **Идентификатор** введите имя для идентификации учетной записи DocumentDB. Это значение становится именем узла в URI. В идентификаторе могут использоваться только строчные буквы, цифры и символ "-", длина его должна быть от 3 до 50 символов. Обратите внимание, что к выбранному вами имени конечной точки добавляется documents.azure.com, результатом чего станет конечная точка вашей учетной записи DocumentDB.

	- Область **Ценовая категория** заблокирована, поскольку предварительный просмотр DocumentDB поддерживает единственную ценовую категорию. Дополнительные сведения см. в разделе [Цены на DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- Область **Дополнительная настройка** используется для указания начальной мощности, выделенной вашей учетной записи DocumentDB.  DocumentDB использует единицы мощности, чтобы позволить масштабирование учетной записи DocumentDB, при котором каждая единица мощности включает зарезервированное хранилище базы данных и пропускную способность.  По умолчанию подготавливается 1 единица мощности.  Вы можете настроить число единиц мощности, доступных в вашей учетной записи DocumentDB с помощью [портала управления предварительным просмотром](https://portal.azure.com/#gallery/Microsoft.DocumentDB). Сведения о мощности и пропускной способности учетной записи DocumentDB см. в статье [Управление мощностью и пропускной способностью DocumentDB][documentdb-manage].

	- В поле **Группа ресурсов** выберите или создайте группу ресурсов для вашей учетной записи DocumentDB.  По умолчанию будет создана новая группа ресурсов.  Однако можно выбрать существующую группу ресурсов, к которой планируется добавить учетную запись DocumentDB. За дополнительной информацией обратитесь к разделу [Использование групп ресурсов для управления вашими ресурсами Azure](http://azure.microsoft.com/ru-ru/documentation/articles/azure-preview-portal-using-resource-groups/).

	- В поле **Подписка** выберите подписку Azure, которую вы хотите использовать для учетной записи DocumentDB. Если ваша учетная запись имеет только одну подписку, эта подписка будет выбрана автоматически.*
 
	- В поле **Расположение** укажите географическое расположение, где будет размещена ваша учетная запись DocumentDB.   

3.	После настройки параметров учетной записи DocumentDB нажмите **Создать**.  Создание учетной записи DocumentDB может занять несколько минут.  Для проверки состояния можно отслеживать прогресс на начальной панели.  
	![][4]  
  
	Или вы можете наблюдать за ходом выполнения на портале "Уведомления".  

	![][5]  

	![][6]

4.	После создания учетной записи DocumentDB она готова для использования с параметрами по умолчанию.

	*Обратите внимание, что по умолчанию для уровня согласованности учетной записи DocumentDB будет установлено значение "Сеанс".  Этот параметр согласованности по умолчанию вы можете изменить на [портале управления предварительным просмотром](https://portal.azure.com/#gallery/Microsoft.DocumentDB).*  
	![][7]  

5.	Вы можете также получить доступ к вашим уже имеющимся учетным записям DocumentDB в выноске **Обзор**.  
	![][8]

##<a id="NextSteps"></a>Дальнейшие действия
Приступая к работе с Azure DocumentDB, изучите следующие ресурсы:

-	[Модель ресурсов и основные понятия DocumentDB](/documentation/articles/documentdb-resources/)
-	[Взаимодействие с ресурсами DocumentDB](/documentation/articles/documentdb-interactions-with-resources/)
-	[Создание учетной записи DocumentDB](/documentation/articles/documentdb-create-account/)
-	[Начало работы с учетной записью DocumentDB](/documentation/articles/documentdb-get-started/)

Дополнительные сведения о DocumentDB см. в документации по Azure DocumentDB на [azure.com](http://go.microsoft.com/fwlink/p/?LinkID=402319)

[Практическое руководство. Создание учетной записи DocumentDB]: #Howto
[Дальнейшие действия]: #NextSteps
[documentdb-manage]:../documentdb-manage/

<!--Image references-->
[1]: ./media/documentdb-create-account/ca1.png
[2]: ./media/documentdb-create-account/ca2.png
[3]: ./media/documentdb-create-account/ca3.png
[4]: ./media/documentdb-create-account/ca4.png
[5]: ./media/documentdb-create-account/ca5.png
[6]: ./media/documentdb-create-account/ca6.png
[7]: ./media/documentdb-create-account/ca7.png
[8]: ./media/documentdb-create-account/ca8.png

<!--HONumber=35.2-->
