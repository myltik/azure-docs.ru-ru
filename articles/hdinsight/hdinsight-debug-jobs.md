<properties
	pageTitle="Отладка Hadoop в HDInsight: сообщения об ошибках | Microsoft Azure"
	description="Познакомьтесь с сообщениями об ошибках, которые могут поступать во время администрирования HDInsight с помощью PowerShell, а также с мерами, которые следует предпринять для восстановления нормальной работоспособности."
	services="hdinsight"
	tags="azure-portal"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="jgao"/>

# Отладка Hadoop в среде HDInsight: разъяснение сообщений об ошибках

Сообщения об ошибках, перечисленные в этом разделе, призваны помочь пользователям Hadoop в Azure HDInsight понять возможные ошибки, которые могут возникать при администрировании службы с помощью Azure PowerShell, и уведомить их о действиях, которые можно предпринять для устранения таких ошибок.

Некоторые из этих сообщений об ошибке также могут отображаться на портале предварительной версии Azure, когда он используется для управления кластерами HDInsight. Существуют и другие сообщения об ошибках, которые менее подробны в связи с ограниченными возможностями по устранению таких ошибок в данном контексте. В контекстах, где подобные действия осуществимы, выводятся другие сообщения об ошибках. Например, при нарушении ограничений для параметра выводится всплывающее сообщение в правой стороне поля, где было введено значение. В этом случае было запрошено избыточное число узлов данных. Для устранения ошибки следует уменьшить это число до разрешенного значения (не более 33).

![Сообщение об ошибке на предварительной версии портала HDInsight][image-hdi-debugging-error-messages-portal]

Ошибки, с которыми пользователь может столкнуться в Azure PowerShell или на портале предварительной версии Azure, отображаются в списке, упорядоченном по алфавиту, в разделе [Ошибки HDInsight](#hdinsight-error-messages). Там они связаны с записью в разделе [Описание и устранение ошибок](#discription-mitigation-errors), где приводятся следующие сведения об ошибке:

- **Описание**: сообщение об ошибке, отображаемое для пользователя.
- **Устранение**: действия, которые требуется выполнить для устранения этой ошибки.

###Коды ошибок HDInsight

- [AtleastOneSqlMetastoreMustBeProvided](#AtleastOneSqlMetastoreMustBeProvided)
- [AzureRegionNotSupported](#AzureRegionNotSupported)
- [ClusterContainerRecordNotFound](#ClusterContainerRecordNotFound)
- [ClusterDnsNameInvalidReservedWord](#ClusterDnsNameInvalidReservedWord)
- [ClusterNameUnavailable](#ClusterNameUnavailable)
- [ClusterUserNameInvalid](#ClusterUserNameInvalid)
- [ClusterUserNameInvalidReservedWord](#ClusterUserNameInvalidReservedWord)
- [ContainerNameMisMatchWithDnsName](#ContainerNameMisMatchWithDnsName)
- [DataNodeDefinitionNotFound](#DataNodeDefinitionNotFound)
- [DeploymentDeletionFailure](#DeploymentDeletionFailure)
- [DnsMappingNotFound](#DnsMappingNotFound)
- [DuplicateClusterContainerRequest](#DuplicateClusterContainerRequest)
- [DuplicateClusterInHostedService](#DuplicateClusterInHostedService)
- [FailureToUpdateDeploymentStatus](#FailureToUpdateDeploymentStatus)
- [HdiRestoreClusterAltered](#HdiRestoreClusterAltered)
- [HeadNodeConfigNotFound](#HeadNodeConfigNotFound)
- [HeadNodeConfigNotFound](#HeadNodeConfigNotFound)
- [HostedServiceCreationFailure](#HostedServiceCreationFailure)
- [HostedServiceHasProductionDeployment](#HostedServiceHasProductionDeployment)
- [HostedServiceNotFound](#HostedServiceNotFound)
- [HostedServiceWithNoDeployment](#HostedServiceWithNoDeployment)
- [InsufficientResourcesCores](#InsufficientResourcesCores)
- [InsufficientResourcesHostedServices](#InsufficientResourcesHostedServices)
- [InternalErrorRetryRequest](#InternalErrorRetryRequest)
- [InvalidAzureStorageLocation](#InvalidAzureStorageLocation)
- [InvalidNodeSizeForDataNode](#InvalidNodeSizeForDataNode)
- [InvalidNodeSizeForHeadNode](#InvalidNodeSizeForHeadNode)
- [InvalidRightsForDeploymentDeletion](#InvalidRightsForDeploymentDeletion)
- [InvalidStorageAccountBlobContainerName](#InvalidStorageAccountBlobContainerName)
- [InvalidStorageAccountConfigurationSecretKey](#InvalidStorageAccountConfigurationSecretKey)
- [InvalidVersionHeaderFormat](#InvalidVersionHeaderFormat)
- [MoreThanOneHeadNode](#MoreThanOneHeadNode)
- [OperationTimedOutRetryRequest](#OperationTimedOutRetryRequest)
- [ParameterNullOrEmpty](#ParameterNullOrEmpty)
- [PreClusterCreationValidationFailure](#PreClusterCreationValidationFailure)
- [RegionCapabilityNotAvailable](#RegionCapabilityNotAvailable)
- [StorageAccountNotColocated](#StorageAccountNotColocated)
- [SubscriptionIdNotActive](#SubscriptionIdNotActive)
- [SubscriptionIdNotFound](#SubscriptionIdNotFound)
- [UnableToResolveDNS](#UnableToResolveDNS)
- [UnableToVerifyLocationOfResource](#UnableToVerifyLocationOfResource)
- [VersionCapabilityNotAvailable](#VersionCapabilityNotAvailable)
- [VersionNotSupported](#VersionNotSupported)
- [VersionNotSupportedInRegion](#VersionNotSupportedInRegion)
- [WasbAccountConfigNotFound](#WasbAccountConfigNotFound)



## <a id="discription-mitigation-errors"></a>Диагностика и устранение ошибок


### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
- **Описание**. Предоставьте сведения о базе данных SQL Azure по меньшей мере для одного компонента, чтобы использовать настраиваемые параметры для метахранилищ Hive и Oozie.
- **Устранение**. Пользователь должен указать допустимое метахранилище SQL Azure и повторить запрос.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
- **Описание**. Не удалось создать кластер в регионе *имяВашегоРегиона*. Выберите допустимую область HDInsight и повторите запрос.
- **Устранение**. Клиент должен создать кластерный регион, который в настоящее время поддерживает кластеры: Юго-Восточная Азия, Западная Европа, Северная Европа, Восток США или Запад США.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
- **Описание**. Серверу не удалось найти запрошенную запись кластера.  
- **Устранение**. Повторите операцию.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
- **Описание**. Недопустимое DNS-имя кластера *вашеDnsИмя*. Убедитесь, что имя начинается и оканчивается цифрой или буквой и не содержит специальных символов отличных от "-"  
- **Устранение**. Убедитесь, что для кластера используется допустимое DNS-имя, которое начинается с цифры или буквы и заканчивается на цифру или букву и при этом не содержит специальных символов, кроме дефиса «-», а затем повторите операцию.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
- **Описание**. Недоступное имя кластера *имяВашегоКластера*. Выберите другое имя.  
- **Устранение**. Пользователь должен указать уникальное имя кластера, которое не существует, а затем повторить попытку. Если имя кластера уже существует, в процессе создания кластера на портале предварительной версии появится соответствующее уведомление.


### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
- **Описание**. Недопустимый пароль кластера. Пароль должен быть не короче 10 символов и содержать по крайней мере одну цифру, строчную букву, прописную букву и специальный символ без пробелов, а также не может включать имя пользователя.  
- **Устранение**. Укажите допустимый пароль кластера и повторите операцию.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
- **Описание**. Недопустимое имя пользователя для кластера. Убедитесь, что имя пользователя не содержит специальных символов или пробелов.  
- **Устранение**. Укажите допустимое имя пользователя для кластера и повторите операцию.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
- **Описание**. Недопустимое DNS-имя кластера *вашеDnsИмяКластера*. Убедитесь, что имя начинается и оканчивается цифрой или буквой и не содержит специальных символов отличных от "-"  
- **Устранение**. Укажите допустимое имя пользователя для кластера DNS и повторите операцию.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
- **Описание**. Имя контейнера в URI *URIВашегоКонтейнера* и DNS-имя *вашеDNSимя* в тексте запроса должны быть одинаковыми.  
- **Устранение**. Убедитесь, что DNS-имя и имя контейнера одинаковы, и повторите операцию.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
- **Описание**. Недопустимая конфигурация кластера. Не удалось найти определения узлов данных в размерах узлов.  
- **Устранение**. Повторите операцию.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
- **Описание**. Ошибка при удалении развертывания для кластера  
- **Устранение**. Повторите операцию удаления.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
- **Описание**. Ошибка конфигурации службы. Не удалось найти требуемые сведения о сопоставлении DNS.  
- **Устранение**. Удалите кластер и создайте новый.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
- **Описание**. Попытка создать повторяющийся контейнер кластера. Для контейнера *имяВашегоКонтейнера* существует запись, но теги eTag не совпадают.
- **Устранение**. Укажите уникальное имя для контейнера и повторите операцию создания.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
- **Описание**. Размещенная служба *имяВашейРазмещеннойСлужбы* уже содержит кластер. Размещенная служба не может содержать несколько кластеров.  
- **Устранение**. Разместите узел кластера в другой размещенной службе.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
- **Описание**. Серверу не удалось обновить состояние развертывания кластера.  
- **Устранение**. Повторите операцию. Если ошибка повторится несколько раз, обратитесь в службу поддержки.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
- **Описание**. Кластер *имяВашегоКластера* был удален в процессе обслуживания. Создайте кластер снова.
- **Устранение**. Создайте кластер повторно.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
- **Описание**. Недопустимая конфигурация кластера. Требуемые настройки головного узла не найдены в размерах узлов.
- **Устранение**. Повторите операцию.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
- **Описание**. Не удалось создать размещенную службу *имяВашейРазмещеннойСлужбы*. Повторите запрос.  
- **Устранение**. Повторите запрос.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
- **Описание**. Размещенная служба *имяВашейРазмещеннойСлужбы* уже содержит рабочее развертывание. Размещенная служба не может содержать несколько рабочих развертываний. Повторите запрос с другим именем кластера.
- **Устранение**. Воспользуйтесь другим именем кластера и повторите запрос.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
- **Описание**. Не удалось найти размещенную службу *имяВашейРазмещеннойСлужбы* для кластера.  
- **Устранение**. Если кластер находится в состоянии ошибки, удалите его и повторите попытку.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
- **Описание**. Размещенная служба *имяВашейРазмещеннойСлужбы* не имеет связанного развертывания.  
- **Устранение**. Если кластер находится в состоянии ошибки, удалите его и повторите попытку.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
- **Описание**. В подписке с идентификатором *ИДВашейПодписки* не осталось ядер для создания кластера *имяВашегоКластера*. Требуется: *требуемыеРесурсы*, доступно: *доступныеРесурсы*.  
- **Устранение**. Освободите ресурсы в своей подписке или увеличьте количество ресурсов, доступных для подписки, а затем попробуйте создать кластер снова.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
- **Описание**. В подписке с идентификатором *ИДВашейПодписки* нет квоты на новую размещенную службу для создания кластера *имяВашегоКластера*.  
- **Устранение**. Освободите ресурсы в своей подписке или увеличьте количество ресурсов, доступных для подписки, а затем попробуйте создать кластер снова.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
- **Описание**. Сервер обнаружил внутреннюю ошибку. Повторите запрос.  
- **Устранение**. Повторите запрос.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
- **Описание**. Недопустимое расположение хранилища Azure *имяРегионаДанных*. Убедитесь, что регион указан правильно, и повторите попытку.
- **Устранение**. Выберите расположение хранилища, которое поддерживает HDInsight, убедитесь, что кластер находится в совмещенном расположении, и повторите операцию.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
- **Описание**. Недопустимый размер виртуальной машины для узлов данных. Для всех узлов данных поддерживается только размер "Большая ВМ".  
- **Устранение**. Укажите поддерживаемый размер узла данных и повторите операцию.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
- **Описание**. Недопустимый размер виртуальной машины для головного узла. Для головного узла поддерживается только размер "Виртуальная машина ExtraLarge".  
- **Устранение**. Укажите поддерживаемый размер для головного узла и повторите операцию.

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
- **Описание**. Используемый идентификатор подписки *ИДВашейПодписки* не имеет достаточных разрешений для выполнения операции удаления для кластера *имяВашегоКластера*.  
- **Устранение**. Если кластер находится в состоянии ошибки, отключите его и повторите попытку.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
- **Описание**. Недопустимое имя контейнера двоичных объектов большого размера для внешней учетной записи хранения *имяВашегоКонтейнера*. Убедитесь, что имя начинается с буквы и содержит только строчные буквы, цифры и дефис.  
- **Устранение**. Укажите допустимое имя контейнера двоичных объектов большого размера и повторите операцию.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
- **Описание**. Для конфигурации внешней учетной записи хранения *имяВашейУчетнойЗаписиХранения* требуется задать данные секретного ключа.  
- **Устранение**. Укажите допустимый секретный ключ для учетной записи хранения и повторите операцию.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
- **Описание**. Формат заголовка версии *вашЗаголовокВерсии* отличается от допустимого формата гггг-мм-дд.  
- **Устранение**. Укажите допустимый формат для заголовка версии и повторите запрос.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
- **Описание**. Недопустимая конфигурация кластера. Обнаружена конфигурация с несколькими головными узлами.  
- **Устранение**. Измените конфигурацию таким образом, чтобы был указан только один головной узел.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
- **Описание**. Не удалось завершить операцию в течение допустимого времени или в пределах максимального количества повторных попыток. Повторите запрос.  
- **Устранение**. Повторите запрос.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
- **Описание**. Параметр *имяВашегоПараметра* не может быть неопределенным или пустым.  
- **Устранение**. Укажите допустимое значение для этого параметра.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
- **Описание**. Недопустимые входные данные для запроса на создание кластера. Убедитесь, что указаны правильные входные значения, и повторите запрос.  
- **Устранение**. Убедитесь, что указаны правильные входные значения, и повторите запрос.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
- **Описание**. Недоступна поддержка региона *имяВашегоРегиона* и идентификатора подписки *ИДВашейПодписки*.  
- **Устранение**. Укажите регион, который поддерживает кластеры HDInsight. Поддерживаемые регионы: Юго-Восточная Азия, Западная Европа, Северная Европа, Восток США или Запад США.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
- **Описание**. Учетная запись хранения *имяВашейУчетнойЗаписиХранения* находится в регионе *имяТекущегоРегиона*. Этот регион должен совпадать с регионом кластера *имяРегионаВашегоКластера*.  
- **Устранение**. Укажите учетную запись хранения в том же регионе, где находится ваш кластер, или, если ваши данные уже находятся в этой учетной записи хранения, создайте новый кластер в регионе существующей учетной записи хранения. Если работа ведется на портале предварительной версии, пользователь заблаговременно получит уведомление об этой проблеме в пользовательском интерфейсе.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
- **Описание**. Указанный идентификатор подписки *ИДВашейПодписки* неактивен.  
- **Устранение**. Повторно активируйте свою подписку или получите новую действующую подписку.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
- **Описание**. Не удалось найти идентификатор подписки *ИДВашейПодписки*.  
- **Устранение**. Убедитесь, что указан допустимый идентификатор подписки, а затем повторите операцию.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
- **Описание**. Не удается разрешить DNS *URLВашегоDNS*. Убедитесь, что для конечной точки большого двоичного объекта указан полный URL-адрес.  
- **Устранение**. Укажите допустимый URL-адрес большого двоичного объекта. Этот URL-адрес ДОЛЖЕН быть записан полностью, включая префикс **http://* и окончание *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
- **Описание**. Невозможно проверить расположение ресурса *URLВашегоDNS*. Убедитесь, что для конечной точки большого двоичного объекта указан полный URL-адрес.  
- **Устранение**. Укажите допустимый URL-адрес большого двоичного объекта. Этот URL-адрес ДОЛЖЕН быть записан полностью, включая префикс **http://* и окончание *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
- **Описание**. Недоступна поддержка версии для версии *указаннаяВерсия* и идентификатора подписки *ИДВашейПодписки*.  
- **Устранение**. Выберите доступную версию и повторите операцию.

### <a id="VersionNotSupported"></a>VersionNotSupported
- **Описание**. Версия *указаннаяВерсия* не поддерживается.
- **Устранение**. Выберите поддерживаемую версию и повторите операцию.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
- **Описание**. Версия *указаннаяВерсия* недоступна в регионе Azure *указанныйРегион*.  
- **Устранение**. Выберите версию, которая поддерживается в указанном регионе, и повторите операцию.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
- **Описание**. Недопустимая конфигурация кластера. Требуемая конфигурация учетной записи WASB не найдена во внешних учетных записях.  
- **Устранение**. Убедитесь, что учетная запись существует и правильно указана в конфигурации, а затем повторите операцию.

## <a id="resources"></a>Дополнительные ресурсы для отладки

* [Документация по пакету SDK для Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[image-hdi-debugging-error-messages-portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png

<!---HONumber=August15_HO8-->