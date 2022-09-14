<div class="entry-content" style="height: auto !important;">
    <h2 id="goto-h2-1"> Sự khác nhau giữa overloading và overriding trong java </h2>
    <p> Sự khác nhau giữa overloading và overriding phương thức trong java được thể hiện trong bảng sau:</p>
    <table class="alt">
        <tbody>
            <tr>
                <th> No. </th>
                <th> Nạp chồng phương thức (overloading) </th>
                <th> Ghi đè phương thức (overriding)</th>
            </tr>
            <tr>
                <td> 1) </td>
                <td> Nạp chồng phương thức được sử dụng để giúp code của chương trình <em>dễ đọc hơn</em>. </td>
                <td> Ghi đè phương thức được sử dụng để cung cấp <em>cài đặt cụ thể</em> cho phương thức được khai báo ở lớp cha. </td>
            </tr>
            <tr>
                <td> 2) </td>
                <td> Nạp chồng được thực hiện bên <em>trong một class</em>. </td>
                <td> Ghi đè phương thức xảy ra <em>trong 2 class</em> có quan hệ kế thừa. </td>
            </tr>
            <tr>
                <td> 3) </td>
                <td> Nạp chồng phương thức thì <em>tham số phải khác nhau</em>.</td>
                <td> Ghi đè phương thức thì <em>tham số phải giống nhau</em>. </td>
            </tr>
            <tr>
                <td> 4) </td>
                <td> Nạp chồng phương thức là ví dụ về <em>đa hình lúc biên dịch</em>.</td>
                <td> Ghi đè phương thức là ví dụ về <em>đa hình lúc runtime</em>. </td>
            </tr>
            <tr>
                <td> 5) </td>
                <td> Trong java, nạp chồng phương thức không thể được thực hiện khi chỉ thay đổi kiểu giá trị trả về của phương thức. Kiểu giá trị trả về có thể giống hoặc khác. <em>Giá trị trả về có thể giống hoặc khác</em>, nhưng tham số phải khác nhau. </td>
                <td> Giá trị trả về phải giống nhau. </td>
            </tr>
        </tbody>
    </table>
    <h2 id="goto-h2-2"> Ví dụ nạp chồng phương thức </h2>
    <div class="codeblock">
<div><div id="highlighter_398048" class="syntaxhighlighter  java"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div><div class="line number5 index4 alt2">5</div><div class="line number6 index5 alt1">6</div><div class="line number7 index6 alt2">7</div><div class="line number8 index7 alt1">8</div><div class="line number9 index8 alt2">9</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="java keyword">public</code> <code class="java keyword">class</code> <code class="java plain">OverloadingExample {</code></div><div class="line number2 index1 alt1"><code class="java spaces">&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="java keyword">static</code> <code class="java keyword">int</code> <code class="java plain">add(</code><code class="java keyword">int</code> <code class="java plain">a, </code><code class="java keyword">int</code> <code class="java plain">b) {</code></div><div class="line number3 index2 alt2"><code class="java spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="java keyword">return</code> <code class="java plain">a + b;</code></div><div class="line number4 index3 alt1"><code class="java spaces">&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="java plain">}</code></div><div class="line number5 index4 alt2">&nbsp;</div><div class="line number6 index5 alt1"><code class="java spaces">&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="java keyword">static</code> <code class="java keyword">int</code> <code class="java plain">add(</code><code class="java keyword">int</code> <code class="java plain">a, </code><code class="java keyword">int</code> <code class="java plain">b, </code><code class="java keyword">int</code> <code class="java plain">c) {</code></div><div class="line number7 index6 alt2"><code class="java spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="java keyword">return</code> <code class="java plain">a + b + c;</code></div><div class="line number8 index7 alt1"><code class="java spaces">&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="java plain">}</code></div><div class="line number9 index8 alt2"><code class="java plain">}</code></div></div></td></tr></tbody></table></div></div>
</div>
    <h2 id="goto-h2-3"> Ví dụ ghi đè phương thức </h2>
    <div class="codeblock">
<div><div id="highlighter_718647" class="syntaxhighlighter  java"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div><div class="line number5 index4 alt2">5</div><div class="line number6 index5 alt1">6</div><div class="line number7 index6 alt2">7</div><div class="line number8 index7 alt1">8</div><div class="line number9 index8 alt2">9</div><div class="line number10 index9 alt1">10</div><div class="line number11 index10 alt2">11</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="java keyword">class</code> <code class="java plain">Animal {</code></div><div class="line number2 index1 alt1"><code class="java spaces">&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="java keyword">void</code> <code class="java plain">eat() {</code></div><div class="line number3 index2 alt2"><code class="java spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="java plain">System.out.println(</code><code class="java string">"eating..."</code><code class="java plain">);</code></div><div class="line number4 index3 alt1"><code class="java spaces">&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="java plain">}</code></div><div class="line number5 index4 alt2"><code class="java plain">}</code></div><div class="line number6 index5 alt1">&nbsp;</div><div class="line number7 index6 alt2"><code class="java keyword">class</code> <code class="java plain">Dog </code><code class="java keyword">extends</code> <code class="java plain">Animal {</code></div><div class="line number8 index7 alt1"><code class="java spaces">&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="java keyword">void</code> <code class="java plain">eat() {</code></div><div class="line number9 index8 alt2"><code class="java spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="java plain">System.out.println(</code><code class="java string">"eating bread..."</code><code class="java plain">);</code></div><div class="line number10 index9 alt1"><code class="java spaces">&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="java plain">}</code></div><div class="line number11 index10 alt2"><code class="java plain">}</code></div></div></td></tr></tbody></table></div></div>
</div>
</div>
