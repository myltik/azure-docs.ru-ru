
Узнайте больше о глобальном распределении Azure Cosmos DB в этом видео из цикла "Пятница с Azure" от Скотта Хенсельмана (Scott Hanselman) и главного технического руководителя Картика Рамана (Karthik Raman).

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

Дополнительные сведения о том, как функционирует репликация глобальной базы данных в Cosmos DB, см. в статье о [глобальном распределении данных в Cosmos DB](../articles/documentdb/documentdb-distribute-data-globally.md).

## <a id="addregion"></a>Добавление регионов глобальной базы данных с помощью портала Azure
База данных Azure Cosmos DB доступна во всех [регионах Azure][azureregions] по всему миру. После выбора уровня согласованности по умолчанию для учетной записи базы данных вы можете связать один или несколько регионов (в зависимости от выбранного уровня согласованности по умолчанию и потребностей глобального распространения).

1. На левой панели на [портале Azure](https://portal.azure.com/) щелкните **Azure Cosmos DB**.
2. В колонке **Azure Cosmos DB** выберите учетную запись базы данных, которую нужно изменить.
3. В меню колонки учетной записи щелкните **Глобальная репликация данных**.
4. В колонке **Глобальная репликация данных** выберите регионы для добавления или удаления, щелкнув их на карте, и нажмите кнопку **Сохранить**. Добавление регионов оплачивается. Для получения дополнительной информации перейдите на [страницу цен](https://azure.microsoft.com/pricing/details/documentdb/) или прочитайте статью [Глобальное распространение данных с помощью DocumentDB](../articles/documentdb/documentdb-distribute-data-globally.md).
   
    ![Можно щелкать регионы на карте, чтобы добавить или удалить их.][1]
    
После добавления второго региона на портале в колонке **Replicate data locally** (Локальная репликация данных) активируется параметр **Ручная отработка отказа**. Этот параметр можно использовать для тестирования отработки отказа или изменения основного региона записи. После добавления третьего региона в той же колонке активируется параметр **Приоритеты при отработке отказа**, позволяющий изменить порядок отработки отказов для операций чтения.  

### <a name="selecting-global-database-regions"></a>Выбор регионов глобальной базы данных
Существуют два распространенных сценария настройки нескольких регионов:

1. Обеспечение низкой задержки при обращении пользователей к данным по всему миру не зависимо от расположения.
2. Добавление региональной устойчивости для непрерывности бизнес-процессов и аварийного восстановления (BCDR)

Чтобы обеспечить низкую задержку для пользователей, мы советуем развернуть приложение и добавить Azure Cosmos DB в регионы, соответствующие расположению пользователей приложения.

Для BCDR мы рекомендуем добавлять регионы исходя из пар регионов, описанных в статье [Непрерывность бизнес-процессов и аварийное восстановление в службах BizTalk: пары регионов Azure][bcdr].

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmosdb-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmosdb-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmosdb-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/documentdb/
