Действие копирования в фабрике данных копирует данные из хранилища-источника в хранилище-приемник. Фабрика данных поддерживает приведенные ниже хранилища данных. Данные из любого источника можно записывать в любой приемник. Щелкните название хранилища, чтобы узнать, как скопировать данные из него или в него.

> [!NOTE] 
> Если необходимо переместить данные в хранилище или из хранилища данных, которое не поддерживается действием копирования, вы можете использовать в фабрике данных **настраиваемое действие**. В нем для копирования и перемещения данных используется логика, которую задаете вы. Сведения о создании и использовании настраиваемого действия см. в статье [Использование настраиваемых действий в конвейере фабрики данных Azure](../articles/data-factory/data-factory-use-custom-activities.md).

| Категория | Хранилище данных | Поддерживается в качестве источника | Поддерживается в качестве приемника |
|:--- |:--- |:--- |:--- |
| **Таблицы Azure** |[хранилище BLOB-объектов Azure](../articles/data-factory/data-factory-azure-blob-connector.md) |✓  |✓  |
| &nbsp; |[Хранилище озера данных Azure](../articles/data-factory/data-factory-azure-datalake-connector.md) |✓  |✓  |
| &nbsp; |[Azure DocumentDB](../articles/data-factory/data-factory-azure-documentdb-connector.md) |✓ |✓  |
| &nbsp; |[База данных SQL Azure;](../articles/data-factory/data-factory-azure-sql-connector.md) |✓  |✓  |
| &nbsp; |[Хранилище данных Azure SQL](../articles/data-factory/data-factory-azure-sql-data-warehouse-connector.md) |✓  |✓  |
| &nbsp; |[Индекс службы поиска Azure](../articles/data-factory/data-factory-azure-search-connector.md) | |✓  |
| &nbsp; |[Хранилище таблиц Azure](../articles/data-factory/data-factory-azure-table-connector.md) |✓  |✓  |
| **Базы данных** |[Amazon Redshift](../articles/data-factory/data-factory-amazon-redshift-connector.md) |✓  | |
| &nbsp; |[DB2](../articles/data-factory/data-factory-onprem-db2-connector.md)* |✓  | |
| &nbsp; |[MySQL](../articles/data-factory/data-factory-onprem-mysql-connector.md)* |✓  | |
| &nbsp; |[Oracle](../articles/data-factory/data-factory-onprem-oracle-connector.md)* |✓  |✓  |
| &nbsp; |[PostgreSQL](../articles/data-factory/data-factory-onprem-postgresql-connector.md)* |✓  | |
| &nbsp; |[SAP Business Warehouse](../articles/data-factory/data-factory-sap-business-warehouse-connector.md)* |✓  | |
| &nbsp; |[SAP HANA](../articles/data-factory/data-factory-sap-hana-connector.md)* |✓  | |
| &nbsp; |[SQL Server](../articles/data-factory/data-factory-sqlserver-connector.md)* |✓  |✓  |
| &nbsp; |[Sybase](../articles/data-factory/data-factory-onprem-sybase-connector.md)* |✓  | |
| &nbsp; |[Teradata](../articles/data-factory/data-factory-onprem-teradata-connector.md)* |✓  | |
| **NoSQL** |[Cassandra](../articles/data-factory/data-factory-onprem-cassandra-connector.md)* |✓  | |
| &nbsp; |[MongoDB](../articles/data-factory/data-factory-on-premises-mongodb-connector.md)* |✓  | |
| **File** |[Amazon S3](../articles/data-factory/data-factory-amazon-simple-storage-service-connector.md) |✓  | |
| &nbsp; |[Файловая система](../articles/data-factory/data-factory-onprem-file-system-connector.md)* |✓  |✓  |
| &nbsp; |[FTP](../articles/data-factory/data-factory-ftp-connector.md) |✓  | |
| &nbsp; |[HDFS](../articles/data-factory/data-factory-hdfs-connector.md)* |✓  | |
| &nbsp; |[SFTP](../articles/data-factory/data-factory-sftp-connector.md) |✓  | |
| **Прочее** |[Базовый HTTP-запрос](../articles/data-factory/data-factory-http-connector.md) |✓  | |
| &nbsp; |[Generic OData](../articles/data-factory/data-factory-odata-connector.md) |✓  | |
| &nbsp; |[Generic ODBC](../articles/data-factory/data-factory-odbc-connector.md)* |✓  | |
| &nbsp; |[Salesforce](../articles/data-factory/data-factory-salesforce-connector.md) |✓  | |
| &nbsp; |[Веб-таблица (таблица на основе HTML)](../articles/data-factory/data-factory-web-table-connector.md) |✓  | |
| &nbsp; |[GE Historian](../articles/data-factory/data-factory-odbc-connector.md#ge-historian-store)* |✓  | | |

> [!NOTE]
> Хранилища данных, отмеченные звездочкой (*), могут находиться в локальном расположении или в IaaS Azure и требовать установки [шлюза управления данными](../articles/data-factory/data-factory-data-management-gateway.md) на локальном компьютере или компьютере IaaS Azure.
>
>
