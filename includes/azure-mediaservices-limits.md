<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ресурс</th>
   <th align="left" valign="middle">Ограничение по умолчанию</th>
   <th align="left" valign="middle">Максимальное ограничение</th>
</tr>
<tr>
   <td valign="middle"><p>Учетные записи служб мультимедиа Azure (AMS) в одной подписке</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p>Ресурсов на учетную запись AMS</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>1 000 000</p></td>
</tr>
<tr>
   <td valign="middle"><p>Цепных задач для задания</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>30</p></td>
</tr>
<tr>
   <td valign="middle"><p>50 ресурсов на задачу</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Ресурсов на задание</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Заданий на учетную запись AMS </p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50 000<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Уникальных указателей, связанных с ресурсом за один раз</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>5<sup>4</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Динамических каналов на учетную запись AMS </p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>н/д<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Программ в остановленном состоянии на канал </p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>н/д<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>Программ в запущенном состоянии на канал </p></td>
   <td valign="middle"><p>3</p></td>
   <td valign="middle"><p>н/д<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>Конечных точек потоковой передачи в запущенном состоянии на учетную запись AMS</p></td>
   <td valign="middle"><p>2</p></td>
   <td valign="middle"><p>н/д<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Единиц потоковой передачи на конечную точку потоковой передачи </p></td>
   <td valign="middle"><p>10 </p></td>
   <td valign="middle"><p>н/д<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Единиц кодирования на учетную запись AMS </p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>н/д<sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup> Чтобы запросить обновление предельных значений для этой квоты, откройте запрос в службу поддержки. Чтобы увеличивать предельные значения, не нужно создавать дополнительные учетные записи AMS. Достаточно просто отправить запрос в службу поддержки.

<sup>2</sup> Это число включает задания в очереди, завершенные, активные и отмененные задания. Удаленные задания не учитываются. Старые задания можно удалить с помощью **IJob.Delete** или HTTP-запроса **DELETE**.

<sup>3</sup> При запросе сущностей Job списка, возвращается не более 1000 записей на запрос. Если необходимо отслеживать все отправленные задания, можно использовать top и skip, как описано в статье [Параметры системных запросов OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Указатели не предназначены для управления доступом на уровне пользователей. Для предоставления различных прав доступа отдельным пользователям используйте решения для управления цифровыми правами (DRM).

<!---HONumber=August15_HO6-->