<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ресурс</th>
   <th align="left" valign="middle">Ограничение по умолчанию</th>
</tr>
<tr>
   <td valign="middle"><p>Размер базы данных</p></td>
   <td valign="middle"><p>Зависит от уровня производительности<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Количество входов в систему</p></td>
   <td valign="middle"><p>Зависит от уровня производительности<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Использование памяти</p></td>
   <td valign="middle"><p>Предоставление 16&#160;МБ памяти на время более 20&#160;с</p></td>
</tr>
<tr>
   <td valign="middle"><p>Сеансы:</p></td>
   <td valign="middle"><p>Зависит от уровня производительности<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Размер tempdb</p></td>
   <td valign="middle"><p>5&#160;ГБ</p></td>
</tr>
<tr>
   <td valign="middle"><p>Длительность транзакции</p></td>
   <td valign="middle"><p>24&#160;ч<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Количество блокировок на одну транзакцию</p></td>
   <td valign="middle"><p>1&#160;млн</p></td>
</tr>
<tr>
   <td valign="middle"><p>Размер на одну транзакцию</p></td>
   <td valign="middle"><p>2&#160;ГБ</p></td>
</tr>
<tr>
   <td valign="middle"><p>Процент общего пространства журнала, используемого на одну транзакцию</p></td>
   <td valign="middle"><p>20&#160;%</p></td>
</tr>
<tr>
   <td valign="middle"><p>Максимальное количество одновременных запросов (рабочих потоков)</p></td>
   <td valign="middle"><p>Зависит от уровня производительности<sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup>База данных SQL имеет несколько уровней производительности, например Basic, Standard и Premium. Уровни Standard и Premium, в свою очередь, тоже разделены на уровни производительности. Подробные сведения об ограничениях для уровней производительности и обслуживания см. в статье [Уровни служб и уровни производительности в базе данных SQL Azure](https://msdn.microsoft.com/library/azure/dn741336.aspx).

<sup>2</sup>Если транзакция блокирует ресурс, необходимый базовой системной задаче, то максимальная длительность составит 20 с.

<!---HONumber=July15_HO5-->