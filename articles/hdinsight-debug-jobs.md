<properties urlDisplayName="Debug HDInsight Hadoop Errors" pageTitle="Отладка Hadoop в HDInsight: Сообщения об ошибках | Azure" metaKeywords="hdinsight, hdinsight service, hdinsight azure, debug, error messages, errors" description="Learn about the error messages you might receive when administering HDInsight using PowerShell, and steps you can take to recover." services="hdinsight" title="Debug Hadoop in HDInsight: Error messages" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />

# Отладка Hadoop в HDInsight: Сообщения об ошибках

##Введение
Сообщения об ошибках, перечисленные в этом разделе, призваны помочь пользователям Hadoop в Azure HDInsight понять возможные ошибки, которые могут возникать при администрировании службы с помощью Azure PowerShell, и уведомить их о действиях, которые можно предпринять для устранения таких ошибок. 

Некоторые из этих сообщений об ошибке также могут отображаться на портале Azure, когда он используется для управления кластерами HDinsight. Существуют и другие сообщения об ошибках, которые менее подробны в связи с ограниченными возможностями по устранению таких ошибок в данном контексте. В контекстах, где подобные действия осуществимы, выводятся другие сообщения об ошибках. Например, при нарушении ограничений для параметра выводится всплывающее сообщение в правой стороне поля, где было введено значение. В этом случае было запрошено избыточное число узлов данных. Для устранения ошибки следует снизить это число до разрешенного значения (не более 22).

![HDI.Debugging.ErrorMessages.Portal][image-hdi-debugging-error-messages-portal]

Ошибки, с которыми может столкнуться пользователь при использовании Azure PowerShell или портала Azure, перечислены по имени в алфавитном порядке в разделе [Ошибки HDInsight],(#hdinsight-error-messages) где они связаны с записью в разделе [Описание и устранение ошибок],(#discription-mitigation-errors) в котором содержится следующая информация об ошибках:
 	
- **описание** - сообщение об ошибке, отображаемое для пользователя;	
- **устранение** - действия, которые требуется выполнить для устранения этой ошибки. 

###Ошибки HDInsight

[AtleastOneSqlMetastoreMustBeProvided](#AtleastOneSqlMetastoreMustBeProvided)
[AzureRegionNotSupported](#AzureRegionNotSupported)		
[ClusterContainerRecordNotFound](#ClusterContainerRecordNotFound)	 
[ClusterDnsNameInvalidReservedWord](#ClusterDnsNameInvalidReservedWord)		
[ClusterNameUnavailable](#ClusterNameUnavailable)	
[ClusterUserNameInvalid](#ClusterUserNameInvalid)	
[ClusterUserNameInvalidReservedWord](#ClusterUserNameInvalidReservedWord)	
[ContainerNameMisMatchWithDnsName](#ContainerNameMisMatchWithDnsName)	
[DataNodeDefinitionNotFound](#DataNodeDefinitionNotFound)	
[DeploymentDeletionFailure](#DeploymentDeletionFailure)	
[DnsMappingNotFound](#DnsMappingNotFound)	
[DuplicateClusterContainerRequest](#DuplicateClusterContainerRequest)	
[DuplicateClusterInHostedService](#DuplicateClusterInHostedService)		
[FailureToUpdateDeploymentStatus](#FailureToUpdateDeploymentStatus)		
[HdiRestoreClusterAltered](#HdiRestoreClusterAltered)	
[HeadNodeConfigNotFound](#HeadNodeConfigNotFound)	
[HeadNodeConfigNotFound](#HeadNodeConfigNotFound)	 
[HostedServiceCreationFailure](#HostedServiceCreationFailure)	
[HostedServiceHasProductionDeployment](#HostedServiceHasProductionDeployment)	
[HostedServiceNotFound](#HostedServiceNotFound)		
[HostedServiceWithNoDeployment](#HostedServiceWithNoDeployment)		
[InsufficientResourcesCores](#InsufficientResourcesCores)	
[InsufficientResourcesHostedServices](#InsufficientResourcesHostedServices)		
[InternalErrorRetryRequest](#InternalErrorRetryRequest)		
[InvalidAzureStorageLocation](#InvalidAzureStorageLocation)		
[InvalidNodeSizeForDataNode](#InvalidNodeSizeForDataNode)	
[InvalidNodeSizeForHeadNode](#InvalidNodeSizeForHeadNode)	
[InvalidRightsForDeploymentDeletion](#InvalidRightsForDeploymentDeletion)	
[InvalidStorageAccountBlobContainerName](#InvalidStorageAccountBlobContainerName)	
[InvalidStorageAccountConfigurationSecretKey](#InvalidStorageAccountConfigurationSecretKey)	
[InvalidVersionHeaderFormat](#InvalidVersionHeaderFormat)	
[MoreThanOneHeadNode](#MoreThanOneHeadNode)	
[OperationTimedOutRetryRequest](#OperationTimedOutRetryRequest)	
[ParameterNullOrEmpty](#ParameterNullOrEmpty)	
[PreClusterCreationValidationFailure](#PreClusterCreationValidationFailure)	
[RegionCapabilityNotAvailable](#RegionCapabilityNotAvailable)	
[StorageAccountNotColocated](#StorageAccountNotColocated)	
[SubscriptionIdNotActive](#SubscriptionIdNotActive)	
[SubscriptionIdNotFound](#SubscriptionIdNotFound)	
[UnableToResolveDNS](#UnableToResolveDNS)	
[UnableToVerifyLocationOfResource](#UnableToVerifyLocationOfResource)	
[VersionCapabilityNotAvailable](#VersionCapabilityNotAvailable)	
[VersionNotSupported](#VersionNotSupported)	
[VersionNotSupportedInRegion](#VersionNotSupportedInRegion)	
[WasbAccountConfigNotFound](#WasbAccountConfigNotFound)	



<h2><a id="discription-mitigation-errors"></a>Диагностика и устранение ошибок</h2> 


<h3><a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided</h3>
- **Описание**. Чтобы использовать пользовательские настройки для метахранилищ Hive и Oozie, следует указать информацию о базе данных SQL Azure по меньшей мере для одного компонента.   
- **Устранение**. Пользователь должен указать допустимое метахранилище SQL Azure и повторить запрос.  

<h3><a id="AzureRegionNotSupported"></a>AzureRegionNotSupported</h3>
- **Описание**. Не удалось создать кластер в области nameOfYourRegion. Выберите допустимую область HDInsight и повторите запрос.   
- **Устранение**. Клиент должен создать область, которая в настоящее время поддерживает кластеры: Юго-Восточная Азия Западная Европа, Северная Европа, Восток США или Запад США.  

<h3><a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound</h3>
- **Описание**. Серверу не удалось найти запрошенную запись кластера.  
- **Устранение**. Повторите операцию. 

<h3><a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord</h3>
- **Описание**. Недопустимое DNS-имя кластера yourDnsName. Убедитесь, что имя начинается и оканчивается цифрой или буквой и не содержит специальных символов отличных от '-'  
- **Устранение**. Убедитесь, что для кластера используется допустимое DNS-имя, которое начинается с цифры или буквы и заканчивается на цифру или букву и при этом не содержит специальных символов кроме дефиса "-", а затем повторите операцию.

<h3><a id="ClusterNameUnavailable"></a>ClusterNameUnavailable</h3>
- **Описание**. Недоступное имя кластера yourClusterName. Выберите другое имя.  
- **Устранение**. Пользователь должен указать уникальное имя кластера, которое не существует, а затем повторить попытку. Если пользователь использует портал, в процессе создания кластера ему будет выведено уведомление в пользовательском интерфейсе о том, что это имя кластера уже существует. 
 

<h3><a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid</h3>
- **Описание**. Недопустимый пароль для кластера. Пароль должен быть не короче 10 символов и содержать по крайней мере одну цифру, строчную букву, прописную букву и специальный символ без пробелов, а также не может включать имя пользователя.  
- **Устранение**. Укажите допустимый пароль кластера и повторите операцию. 

<h3><a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid</h3>
- **Описание**. Недопустимое имя пользователя для кластера. Убедитесь, что имя пользователя не содержит специальных символов или пробелов.  
- **Устранение**. Укажите допустимое имя пользователя для кластера и повторите операцию.

<h3><a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord</h3>
- **Описание**. Недопустимое DNS-имя кластера yourDnsClusterName. Убедитесь, что имя начинается и оканчивается цифрой или буквой и не содержит специальных символов отличных от '-'  
- **Устранение**. Укажите допустимое имя пользователя для DNS кластера и повторите операцию.

<h3><a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName</h3>
- **Описание**. Имя контейнера в URI yourcontainerURI и DNS-имя yourDnsName в тексте запроса должны быть одинаковыми.  
- **Устранение**. Убедитесь, что DNS-имя и имя контейнера одинаковы и повторите операцию.

<h3><a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound</h3>
- **Описание**. Недопустимая конфигурация кластера. Не удалось найти определения узлов данных в размерах узлов.  
- **Устранение**. Повторите операцию.

<h3><a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure</h3> 	
- **Описание**. Ошибка при удалении развертывания для кластера.  
- **Устранение**. Повторите операцию удаления.

<h3><a id="DnsMappingNotFound"></a>DnsMappingNotFound</h3> 
- **Описание**. Ошибка конфигурации службы. Не удалось найти требуемые сведения о сопоставлении DNS.  
- **Устранение**. Удалите кластер и создайте новый.

<h3><a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest</h3>
- **Описание**. Попытка создать повторяющийся контейнер кластера. Для контейнера nameOfYourContainer существует запись, но теги eTag не совпадают.   
- **Устранение**. Укажите уникальное имя для контейнера и повторите операцию создания. 

<h3><a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService</h3>
- **Описание**. Размещенная служба nameOfYourHostedService уже содержит кластер. Размещенная служба не может содержать несколько кластеров.  
- **Устранение**. Разместите узел кластера в другой размещенной службе. 

<h3><a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus</h3>
- **Описание**. Серверу не удалось обновить состояние развертывания кластера.  
- **Устранение**. Повторите операцию. Если ошибка повторится несколько раз, обратитесь в службу поддержки. 

<h3><a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered</h3>
- **Описание**. Кластер yourClusterName был удален в процессе обслуживания. Создайте кластер снова.     
- **Устранение**. Создайте кластер повторно.

<h3><a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound</h3>
- **Описание**. Недопустимая конфигурация кластера. Требуемые настройки головного узла не найдены в размерах узлов.
- **Устранение**. Повторите операцию.

<h3><a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure</h3>
- **Описание**. Не удалось создать размещенную службу nameOfYourHostedService. Повторите запрос.  
- **Устранение**. Повторите запрос.

<h3><a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment</h3>
- **Описание**. Размещенная служба nameOfYourHostedService уже содержит рабочее развертывание. Размещенная служба не может содержать несколько рабочих развертываний. Повторите запрос с другим именем кластера.   
- **Устранение**. Воспользуйтесь другим именем кластера и повторите запрос.

<h3><a id="HostedServiceNotFound"></a>HostedServiceNotFound</h3>
- **Описание**. Не удалось найти размещенную службу nameOfYourHostedService для кластера.  
- **Устранение**. Если кластер находится в состоянии ошибки, удалите его и повторите попытку. 

<h3><a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment</h3>
- **Описание**. Размещенная служба nameOfYourHostedService не содержит связанное развертывание.  
- **Устранение**. Если кластер находится в состоянии ошибки, удалите его и повторите попытку. 

<h3><a id="InsufficientResourcesCores"></a>InsufficientResourcesCores</h3>
- **Описание**. В подписке с идентификатором yourSubscriptionId не осталось ядер для создания кластера yourClusterName. Требуется: resourcesRequired, доступно: resourcesAvailable.  
- **Устранение**. Освободите ресурсы в своей подписке или увеличьте количество ресурсов, доступных для подписки, а затем попробуйте создать кластер снова.

<h3><a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices</h3>
- **Описание**. В подписке с идентификатором yourSubscriptionId нет квоты на новую размещенную службу для создания кластера yourClusterName.  
- **Устранение**. Освободите ресурсы в своей подписке или увеличьте количество ресурсов, доступных для подписки, а затем попробуйте создать кластер снова.

<h3><a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest</h3>
- **Описание**. Сервер обнаружил внутреннюю ошибку. Повторите запрос.  
- **Устранение**. Повторите запрос. 

<h3><a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation</h3>
- **Описание**. Недопустимое расположение службы хранилища Azure dataRegionName. Убедитесь, что регион указан правильно, и повторите попытку.   
- **Устранение**. Выберите расположение хранилища, которое поддерживает HDInsight, убедитесь, что кластер находится в совмещенном расположении, и повторите операцию. 

<h3><a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode</h3>
- **Описание**. Недопустимый размер виртуальной машины для узлов данных. Для всех узлов данных поддерживается только размер 'Большая ВМ'.  
- **Устранение**. Укажите поддерживаемый размер узла данных и повторите операцию. 

<h3><a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode</h3>
- **Описание**. Недопустимый размер виртуальной машины для головного узла. Для головного узла поддерживается только размер "Виртуальная машина ExtraLarge".  
- **Устранение**. Укажите поддерживаемый размер узла данных и повторите операцию.

<h3><a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion</h3>
- **Описание**. У используемого идентификатора подписки yourSubscriptionId нет достаточных разрешений на выполнение операции удаления для кластера yourClusterName.  
- **Устранение**. Если кластер находится в состоянии ошибки, удалите его и повторите попытку.  

<h3><a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName</h3>
- **Описание**. Недопустимое имя контейнера больших двоичных объектов для внешней учетной записи хранения yourContainerName. Убедитесь, что имя начинается с буквы и содержит только строчные буквы, цифры и дефис.  
- **Устранение**. Укажите допустимое имя учетной записи хранения контейнера двоичных объектов большого размера и повторите операцию.

<h3><a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey</h3>
- **Описание**. Для конфигурации внешней учетной записи хранения yourStorageAccountName требуется задать данные секретного ключа.  
- **Устранение**. Укажите допустимый секретный ключ для учетной записи хранения и повторите операцию.

<h3><a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat</h3>
- **Описание**. Формат заголовка версии yourVersionHeader отличается от допустимого формата "гггг-мм-дд".  
- **Устранение**. Укажите допустимый формат для заголовка версии и повторите запрос. 

<h3><a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode</h3>
- **Описание**. Недопустимая конфигурация кластера. Обнаружена конфигурация с несколькими головными узлами.  
- **Устранение**. Измените конфигурацию таким образом, чтобы был указан только один головной узел. 

<h3><a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest</h3>
- **Описание**. Не удалось завершить операцию в течение допустимого времени или в пределах максимального количества повторных попыток. Повторите запрос.  
- **Устранение**. Повторите запрос. 

<h3><a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty</h3>
- **Описание**. Значение параметра yourParameterName не может быть неопределенным или пустым.  
- **Устранение**. Укажите допустимое значение для этого параметра. 

<h3><a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure</h3>
- **Описание**. Недопустимые входные данные для запроса на создание кластера. Убедитесь, что указаны правильные входные значения, и повторите запрос.  
- **Устранение**. Убедитесь, что указаны правильные входные значения, и повторите запрос. 

<h3><a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable</h3>
- **Описание**. Недоступна поддержка области yourRegionName и идентификатора подписки yourSubscriptionId.  
- **Устранение**. Укажите регион, который поддерживает кластеры HDInsight. Поддерживаемые регионы: Юго-Восточная Азия Западная Европа, Северная Европа, Восток США или Запад США. 

<h3><a id="StorageAccountNotColocated"></a>StorageAccountNotColocated</h3>
- **Описание**. Учетная запись хранения yourStorageAccountName находится в области currentRegionName. Она должна совпадать с областью кластера yourClusterRegionName.  
- **Устранение**. Укажите учетную запись хранения в том же регионе, где находится ваш кластер или, если ваши данные уже находятся в этой учетной записи хранения, создайте новый кластер в регионе существующей учетной записи хранения. Если работа ведется на портале, пользователь заблаговременно получит уведомление об этой проблеме в пользовательском интерфейсе. 

<h3><a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive</h3>
- **Описание**. Указанный идентификатор подписки yourSubscriptionId неактивен.  
- **Устранение**. Повторно активируйте свою подписку или получите новую действующую подписку.

<h3><a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound</h3>
- **Описание**. Не удалось найти идентификатор подписки yourSubscriptionId.  
- **Устранение**. Убедитесь, что указан допустимый идентификатор подписки, а затем повторите операцию. 

<h3><a id="UnableToResolveDNS"></a>UnableToResolveDNS</h3>
- **Описание**. Не удалось разрешить DNS yourDnsUrl. Убедитесь, что для конечной точки большого двоичного объекта указан полный URL-адрес.  
- **Устранение**. Укажите допустимый URL-адрес двоичного объекта большого размера. Этот URL-адрес ДОЛЖЕН быть записан полностью, включая префикс http:// и окончание .com. Полный URL-адрес обычно находится на вкладке хранилища на портале manage.windowsazure.com.  

<h3><a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource</h3>
- **Описание**. Не удалось проверить расположение ресурса yourDnsUrl. Убедитесь, что для конечной точки большого двоичного объекта указан полный URL-адрес.  
- **Устранение**. Укажите допустимый URL-адрес двоичного объекта большого размера. Этот URL-адрес ДОЛЖЕН быть записан полностью, включая префикс http:// и окончание .com. Полный URL-адрес обычно находится на вкладке хранилища на портале manage.windowsazure.com. 

<h3><a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable</h3>
- **Описание**. Недоступна поддержка версии specifiedVersion и идентификатора подписки yourSubscriptionId.  
- **Устранение**. Выберите доступную версию и повторите операцию. 

<h3><a id="VersionNotSupported"></a>VersionNotSupported</h3>
- **Описание**. Версия specifiedVersion не поддерживается.   
- **Устранение**. Выберите поддерживаемую версию и повторите операцию.

<h3><a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion</h3>
- **Описание**. Версия specifiedVersion недоступна в области Azure specifiedRegion.  
- **Устранение**. Выберите версию, которая поддерживается в указанном регионе, и повторите операцию. 

<h3><a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound</h3>
- **Описание**. Недопустимая конфигурация кластера. Требуемая конфигурация учетной записи WASB не найдена во внешних учетных записях.  
- **Устранение**. Убедитесь, что учетная запись существует и правильно указана в конфигурации, а затем повторите операцию. 

<h2><a id="resources"></a>Дополнительные ресурсы для отладки</h2> 

* [Документация по пакету SDK для Azure для HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ru-ru/library/dn479185.aspx

[image-hdi-debugging-error-messages-portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png







<!--HONumber=35.1-->
