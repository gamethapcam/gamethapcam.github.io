<div id="ftwp-postcontent"><p><strong>Java là một ngôn ngữ cho phép người dùng tạo ra các đối tượng có thể tái sử dụng trong <a href="https://wiki.tino.org/rom-va-ram-la-gi/" target="_blank" data-type="post" data-id="1530" rel="noreferrer noopener">bộ nhớ</a>. Bạn đang muốn tạo ra một đối tượng nằm ngoài vòng đời của JVM, liệu điều này có khả thi? Vậy Serializable trong Java sẽ là giải pháp dành cho bạn. Không để bạn đợi lâu, chúng ta sẽ tìm hiểu về Serializable trong Java nhé!</strong></p><h2 id="post-37961-_b9kbbedokuap" class="ftwp-heading"><a></a><strong>Tìm hiểu về Serializable trong Java</strong></h2><h3 id="post-37961-_klq3ms5p4fx5" class="ftwp-heading"><a></a><strong>Serializable trong Java là gì?</strong></h3><p><strong>Serializable trong Java</strong> hay <em>tuần tự hóa trong Java </em>là một cơ chế giúp lưu trữ và chuyển đổi trạng thái của 1 đối tượng (<strong>Object</strong>) vào 1 byte stream sao cho byte stream này có thể chuyển đổi ngược trở lại thành một Object.</p><p>Quá trình chuyển đổi byte stream trở thành 1 Object được gọi là DeSerialization.</p><p>Để một Object <strong>có thể thực hiện Serialization</strong> hay gọi tắt là <strong>Serializable</strong>, class của Object cần phải thực hiện implements interface <strong>java.io.Serializable</strong>.</p><div class="wp-block-image"><figure class="aligncenter is-resized"><img loading="lazy" src="https://wiki.tino.org/wp-content/uploads/2021/10/word-image-376.png" alt="tim-hieu-ve-serializable-trong-java" class="wp-image-37963" width="725" height="404" srcset="https://wiki.tino.org/wp-content/uploads/2021/10/word-image-376.png 461w, https://wiki.tino.org/wp-content/uploads/2021/10/word-image-376-300x167.png 300w" sizes="(max-width: 725px) 100vw, 725px" title="Tìm hiểu về Serializable trong Java 42" data-pin-no-hover="true"></figure></div><p>Một số từ khóa Tino Group sẽ giữ nguyên để đồng bộ nội dung trong bài viết bao gồm:</p><ul><li><strong>Class: </strong>lớp</li><li><strong>Interface: </strong>giao diện</li><li><strong>Object: </strong>đối tượng</li><li><strong>Fields: </strong>trường</li><li><strong>implement: </strong>chỉ sự kế thừa trong Java, chỉ các <strong>class </strong>được kế thừa từ <strong>Interface.</strong></li></ul><h3 id="post-37961-_9peiuwxzemay" class="ftwp-heading"><a></a><strong>Interface java.io.Serializable là gì?</strong></h3><p><strong>Serializable </strong>là một <strong>Interface </strong>(giao diện) đánh dấu không có các dữ liệu và phương thức. Thông thường, Serializable được sử dụng để đánh dấu các class trong Java để các Object trong class có thể nhận được những khả năng kế thừa nhất định.</p><p>Ví dụ: class <strong>HocSinh </strong>implements interface <strong>java.io.Serializable. </strong>Nhờ đó, các <strong>Object </strong>bên trong class <strong>HocSinh </strong>có thể chuyển đổi thành stream.</p><pre class="wp-block-code"><code>import java.io.Serializable;
 
public class HocSinh implements Serializable {
int id;
String name;
 
public HocSinh(int id, String name) {
this.id = id;
this.name = name;
}
}</code></pre><h3 id="post-37961-_xcsor72e8swu" class="ftwp-heading"><a></a><strong>Tại sao nên sử dụng Serializable?</strong></h3><p>Khi lập trình với Java, quá trình trao đổi dữ liệu giữa các module khác nhau nhưng đều viết bằng Java, dữ liệu sẽ được thể hiện dưới dạng byte chứ không phải là <strong>Object. </strong>Do đó, chúng ta sẽ cần một cơ chế có thể hiểu các Object được nhận hoặc gửi đi. Serializable trong Java chính là cơ chế đảm nhiệm việc chuyển đổi.</p><p>Quá trình Serialization chuyển đổi giữa Object và byte stream giữa các module vận hành hoàn toàn độc lập với bất cứ nền tảng nào.</p><div class="wp-block-image"><figure class="aligncenter is-resized"><img loading="lazy" src="https://wiki.tino.org/wp-content/uploads/2021/10/word-image-377.png" alt="tim-hieu-ve-serializable-trong-java" class="wp-image-37964" width="720" height="477" srcset="https://wiki.tino.org/wp-content/uploads/2021/10/word-image-377.png 629w, https://wiki.tino.org/wp-content/uploads/2021/10/word-image-377-300x199.png 300w" sizes="(max-width: 720px) 100vw, 720px" title="Tìm hiểu về Serializable trong Java 43" data-pin-no-hover="true"></figure></div><h2 id="post-37961-_manccaguehd4" class="ftwp-heading"><a></a><strong>Ví dụ về Serializable trong Java</strong></h2><p>Quá trình giải thích về Serializable trong Java sẽ rất khó hiểu đối với những bạn mới làm quen với Java. Vì thế, Tino Group sẽ thực hiện một ví dụ để bạn có thể hiểu hơn về Serializable trong Java. Nếu vẫn thấy khó hiểu, bạn có thể tìm hiểu về <a href="https://docs.oracle.com/javase/7/docs/api/java/io/ObjectInputStream.html" target="_blank" rel="noreferrer noopener nofollow">ObjectInputStream </a>để quá trình tìm hiểu về Serializable trong Java dễ hơn nhé!</p><div class="jeg_ad jeg_ad_article jnews_content_inline_2_ads  "><div class="ads-wrapper align-center "><a href="https://tinohost.com/cloud-hosting/" target="_blank" rel="nofollow noopener" class="adlink ads_image align-center">
<img src="https://wiki.tino.org/wp-content/uploads/2021/06/Banner_Tinohost_web.png" alt="Tìm hiểu về Serializable trong Java 3" data-pin-no-hover="true" title="Tìm hiểu về Serializable trong Java 44">
</a><div class="ads-text">QUẢNG CÁO</div></div></div><h3 id="post-37961-_cdhypseh4gfv" class="ftwp-heading"><a></a><strong>Khi nào một class được xem là Serializable thành công?</strong></h3><p>Để một class được xem là Serializable thành công, class sẽ cần phải đáp ứng đầy đủ 2 điều kiện sau:</p><ul><li>Class phải được implement interface java.io.Serializable</li><li>Tất cả các <strong>field </strong>trong class sẽ cần phải <strong>Serializable</strong>. Trong trường hợp <strong>field </strong>không Serializable, field đó sẽ phải được đánh dấu là <em>tạm thời </em><strong><em>– </em>transient.</strong></li></ul><p>Nếu bạn muốn biết một class có Serializable hay không, bạn chỉ cần test class đó là được. Trong trường hợp class có thể thực hiện implements java.io.Serializable thì class Serializable và ngược lại.</p><p>Ví dụ:</p><pre class="wp-block-code"><code>public class NhanVien implements java.io.Serializable {
public String name;
public String address;
public transient int CMND;
public int number;
 
public void mailCheck() {
System.out.println("Gửi mail đến " + name + " " + address);
}
}</code></pre><h3 id="post-37961-_1b099wqnh3im" class="ftwp-heading"><a></a><strong>Serializing một Object</strong></h3><p>Code ví dụ:</p><pre class="wp-block-code"><code>import java.io.*;
public class SerializeDemo {
public static void main(String [] args) {
NhanVien e = new NhanVien();
e.name = "Jame Bond";
e.address = "Ho Chi Minh, Viet Nam";
e.CMND = 11122333;
e.number = 113;
 
try {
FileOutputStream fileOut =
new FileOutputStream("/tmp/employee.ser");
ObjectOutputStream out = new ObjectOutputStream(fileOut);
out.writeObject(e);
out.close();
fileOut.close();
System.out.printf("Dữ liệu sau serialized được lưu tại /tmp/employee.ser");
} catch (IOException i) {
i.printStackTrace();
}
}
}</code></pre><p>Trong đó, ta có thể thấy rằng:</p><ul><li><strong>Class ObjectOutputStream </strong>được sử dụng để serialize một Object. Chương trình <strong>SerializeDemo </strong>khởi tạo Object <strong>NhanVien </strong>và tuần tự hoá Object này thành một tệp.</li><li>Khi chương trình <strong>SerializeDemo </strong>chạy xong, một file có tên là <strong>worker.ser </strong>sẽ được tạo ra.</li></ul><p>Trong Java, quy ước khi một Object được tạo ra, tệp của Object đó sẽ có phần mở rộng là <strong>.ser.</strong></p><h3 id="post-37961-_qgxhb1gvz0c5" class="ftwp-heading"><a></a><strong>Deserializing một Object</strong></h3><p>Sau khi ta đã Serializing một Object, chúng ta sẽ thực hiện Deserializing một Object với ví dụ như sau:</p><pre class="wp-block-code"><code>import java.io.*;
public class DeserializeDemo {
public static void main(String [] args) {
NhanVien e = null;
try {
FileInputStream fileIn = new FileInputStream("/tmp/employee.ser");
ObjectInputStream in = new ObjectInputStream(fileIn);
e = (NhanVien) in.readObject();
in.close();
fileIn.close();
} catch (IOException i) {
i.printStackTrace();
return;
} catch (ClassNotFoundException c) {
System.out.println("Không tìm thấy class NhanVien");
c.printStackTrace();
return;
}
 
System.out.println("Deserialized NhanVien...");
System.out.println("Name: " + e.name);
System.out.println("DiaChi: " + e.address);
System.out.println("CMND: " + e.CMND);
System.out.println("Number: " + e.number);
}
}</code></pre><p>Sau khi chương trình thực hiện, chúng ta sẽ có <strong>kết quả như sau:</strong></p><ul><li>Deserialized NhanVien…</li><li>Name: Jame Bond</li><li>DiaChi:Ho Chi Minh, Viet Nam</li><li>CMND: 0</li><li>Number:113</li></ul><p>Trong đó, chúng ta sẽ có những điều cần lưu ý như sau:</p><ul><li>Kết quả trả về của <strong>readObject() </strong>được tham chiếu đến Object <strong>NhanVien.</strong></li><li>Khối <strong>ClassNotFoundException </strong>được khai báo bởi phương thức <strong>readObject(). </strong>Khi <strong>JVM </strong>– <strong>Java Virtual Machine </strong>không thể tìm thấy mã <strong>bytecode </strong>của <strong>class </strong>trong khi giải mã <strong>Object, </strong>JVM sẽ “ném” Object đó vào <strong>ClassNotFoundException.</strong></li><li>Khi nhìn vào kết quả của field <strong>CMND, </strong>bạn có thể thấy giá trị ban đầu của đối tượng là 11122333 ở ví dụ của “Serializing một Object”. Tuy nhiên, field <strong>CMND </strong>là <strong>transient. </strong>Vì thế, giá trị không thể gửi vào stream ở đầu ra và giá trị của field <strong>CMND </strong>sau khi được deserialize sẽ là 0.</li></ul><h3 id="post-37961-_hqhqp7j3m0kc" class="ftwp-heading"><a></a><strong>Những lưu ý về Serializable trong Java</strong></h3><p>Sau khi tìm hiểu qua các ví dụ, Tino Group sẽ trích ra những lưu ý đang quan tâm về Serializable trong Java như sau:</p><ul><li>Nếu class mẹ đã <strong>implement Serializable,</strong> class con sẽ không cần phải thực hiện implement Serializable lần nữa.</li><li>Ngoài thuộc tính <strong>transient </strong>không thể Serialization còn có thuộc tính <strong>static.</strong></li><li><strong>Hàm constructor – </strong><em>hàm khởi tạo </em>sẽ không được gọi nếu một Object được DeSerialization.</li><li>Nếu bạn muốn Serializable một Object, toàn bộ thuộc tính trong Object đó đều phải Serializable. Ví dụ, thuộc tính <strong>DiaChi </strong>của Object <strong>NhanVien </strong>phải <strong>implement Serializable. </strong>Nếu không, khi Serialization Object <strong>NhanVien</strong>, Java sẽ báo lỗi <strong>java.io.NotSerializableException.</strong></li></ul><div class="wp-block-image"><figure class="aligncenter is-resized"></figure></div>  </div>
