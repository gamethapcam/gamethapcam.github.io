
<h2 id="goto-h2-1"> Lớp Object trong java </h2>
<p> Mặc định lớp Object là lớp cha của tất cả các lớp trong java. Nói cách khác nó là một lớp cáo nhất trong java. </p>
<img class="alignleft wp-image-10 size-full" src="https://viettuts.vn/images/java/lop-object-trong-java.jpg" alt="lớp object trong java">
<div class="clearer"></div>
<p> Sử dụng lớp Object là hữu ích nếu bạn muốn tham chiếu bất kỳ đối tượng nào mà bạn chưa biết kiểu dữ liệu của đối tượng đó. Chú ý rằng biến tham chiếu của lớp cha có thể tham chiếu đến đối tượng của lớp con được gọi là <em>upcasting</em>. </p>
<p> <strong>Ví dụ:</strong> giả sử phương thức getObject() trả về một đối tượng nhưng nó có thể là bất kỳ kiểu nào như Employee,Student, ... chúng ta có thể sử dụng biến tham chiếu của lớp Object để tham chiếu tới đối tượng đó.</p>
<div class="codeblock">
<div><div id="highlighter_132060" class="syntaxhighlighter  java"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="java plain">Object obj=getObject(); </code><code class="java comments">// chúng ta không biết đối tượng gì được trả về từ phương thức này.</code></div></div></td></tr></tbody></table></div></div>
</div>
<p> Lớp Object cung cấp một vài cách xử lý chung cho tất cả các đối tượng như đối tượng có thể được so sánh, đối tượng có thể được cloned, đối tượng có thể được notified... </p>

<br>
<h2 id="goto-h2-2"> Các phương thức của lớp Object </h2>
<p> Lớp Object cung cấp các phương thức như trong bảng sau: </p>
<table class="alt">
<tbody>
  <tr>
    <th> Phương thức </th>
    <th> Mô tả </th>
  </tr>
  <tr>
    <td> public final Class getClass() </td>
    <td> trả về đối tượng lớp Class của đối tượng hiện tại. Từ lớp Class đó có thể lấy được các thông tin metadata của class hiện tại. </td>
  </tr>
  <tr>
    <td> public int hashCode() </td>
    <td> trả về số hashcode cho đối tượng hiện tại. </td>
  </tr>
  <tr>
    <td> public boolean equals(Object obj) </td>
    <td> so sánh đối tượng đã cho với đối tượng hiện tại. </td>
  </tr>
  <tr>
    <td> protected Object clone() throws CloneNotSupportedException </td>
    <td> tạo và trả về bản sao chép (clone) của đối tượng hiện tại. </td>
  </tr>
  <tr>
    <td> public String toString() </td>
    <td> trả về chuỗi ký tự đại diện của đối tượng hiện tại. </td>
  </tr>
  <tr>
    <td> public final void notify() </td>
    <td> đánh thức một luồng, đợi trình giám sát của đối tượng hiện tại. </td>
  </tr>
  <tr>
    <td> public final void notifyAll() </td>
    <td> đánh thức tất cả các luồng. đợi trình giám sát của đối tượng hiện tại. </td>
  </tr>
  <tr>
    <td> public final void wait(long timeout)throws InterruptedException </td>
    <td> làm cho Thread hiện tại đợi trong khoảng thời gian là số mili giây cụ thể, tới khi Thread khác thông báo (gọi phương thức notify() hoặc notifyAll()). </td>
  </tr>
  <tr>
    <td> public final void wait(long timeout,int nanos)throws InterruptedException </td>
    <td> làm cho Thread hiện tại đợi trong khoảng thời gian là số mili giây và nano giây cụ thể, tới khi Thread khác thông báo (gọi phương thức notify() hoặc notifyAll()). </td>
  </tr>
  <tr>
    <td> public final void wait()throws InterruptedException </td>
    <td> làm Thread hiện tại đợi, tới khi Thread khác thông báo (gọi phương thức notify() hoặc notifyAll()). </td>
  </tr>
  <tr>
    <td> protected void finalize()throws Throwable </td>
    <td> Được gọi bởi Garbage Collector trước khi đối tượng bị dọn rác. </td>
  </tr>
</tbody>
</table>
