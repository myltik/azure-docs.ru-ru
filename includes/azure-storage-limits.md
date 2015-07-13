<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ресурс<sup>1</sup></th>
   <th align="left" valign="middle">Ограничение по умолчанию</th>
</tr>
<tr>
   <td valign="middle"><p>ТБ на учетную запись хранилища</p></td>
   <td valign="middle"><p>500 ТБ</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимальный размер одного контейнера BLOB-объектов, таблицы или очереди</p></td>
   <td valign="middle"><p>500 ТБ</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимальное количество контейнеров BLOB-объектов, BLOB-объектов, общих папок, таблиц, очередей, сущностей или сообщений для учетной записи хранения</p></td>
   <td valign="middle"><p>Ограничение касается только размера учетной записи хранения, который не может превышать 500 ТБ</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимальный размер общей папки</p></td>
   <td valign="middle"><p>5 ТБ</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимальное количество файлов в общей папке</p></td>
   <td valign="middle"><p>Ограничение касается только размера общей папки, который не может превышать 5 ТБ</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимум 8 КБ на операции ввода вывода в секунду для постоянного диска (уровень Basic)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимум 8 КБ на операции ввода вывода в секунду для постоянного диска (уровень Standard)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Частота запросов (предполагается размер объекта в 1 КБ) для учетной записи хранения</p></td>
   <td valign="middle"><p>До 20 000 сущностей или сообщений в секунду</p></td>
</tr>
<tr>
   <td valign="middle"><p>Предельная пропускная способность для отдельного BLOB-объекта</p></td>
   <td valign="middle"><p>До 60 МБ в секунду или до 500 запросов в секунду</p></td>
</tr>
<tr>
   <td valign="middle"><p>Предельная пропускная способность для отдельной очереди (размер сообщения 1 КБ)</p></td>
   <td valign="middle"><p>До 2 000 сообщений в секунду</p></td>
</tr>
<tr>
   <td valign="middle"><p>Предельная пропускная способность для отдельного табличного раздела (размер сущностей 1 КБ)</p></td>
   <td valign="middle"><p>До 2 000 сущностей в секунду</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимум входов на учетную запись хранилища (регион — США)</p></td>
   <td valign="middle"><p>10 Гбит/c, если включен GRS<sup>3</sup>, 20 Гбит/c для LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимум выходов на учетную запись хранилища (регион — США)</p></td>
   <td valign="middle"><p>20 Гбит/c, если включен GRS<sup>3</sup>, 30 Гбит/c для LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимум входов на учетную запись хранилища (регионы — Европа и Азия)</p></td>
   <td valign="middle"><p>5 Гбит/c, если включен GRS<sup>3</sup>, 10 Гбит/c для LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимум выходов на учетную запись хранилища (регионы — Европа и Азия)</p></td>
   <td valign="middle"><p>10 Гбит/c, если включен GRS<sup>3</sup>, 15 Гбит/c для LRS</p></td>
</tr>
</table>

<sup>1</sup>Подробнее см. в разделе [Цели масштабирования и производительности хранилища Azure](../articles/storage/storage-scalability-targets.md).

<sup>2</sup>Для виртуальных машин базового уровня не размещайте более 66 активно используемых VHD-образов на учетной записи хранилища, чтобы избежать превышения лимита на 20 000 запросов (20 000/300). Для виртуальных машин на уровне Standard не рекомендуется размещать в учетной записи хранилища более 40 виртуальных жестких дисков (20 000/500). Дополнительные сведения см. в разделе [Размеры виртуальных машин и облачных служб в Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).

<sup>3</sup>GRS означает [геоизбыточное хранилище](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx) (Geo Redundant Storage, GRS). LRS означает [Locally Redundant Storage (Локально избыточное хранилище)](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx). Следует иметь в виду, что GRS обладает также и локальной избыточностью.

<!---HONumber=62-->