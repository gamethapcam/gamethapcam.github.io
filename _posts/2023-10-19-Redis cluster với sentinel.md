<div class="entry">
					<p>Có nhiều công cụ hỗ trợ cho tạo redis cluster như: Sentinel, Dynomite, Reborn, … Trong bài viết này, chúng ta sẽ sử dụng sentinel (một tính năng có sẵn trong redis) để thực hiện giám sát trạng thái hoạt động của redis cluster và thực hiện failover tự động. Đảm bảo rằng hệ thống redis sẽ vẫn hoạt động khi có một trong số các redis server trong cụm cluster down.<img data-attachment-id="1707" data-permalink="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/redis-cluster-with-sentinel/" data-orig-file="https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg" data-orig-size="794,546" data-comments-opened="1" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="redis cluster with sentinel" data-image-description="" data-image-caption="" data-medium-file="https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg?w=300" data-large-file="https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg?w=614" class="alignnone size-full wp-image-1707" src="https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg?w=614" alt="redis cluster with sentinel" srcset="https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg 794w, https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg?w=150&amp;h=103 150w, https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg?w=300&amp;h=206 300w, https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg?w=768&amp;h=528 768w" sizes="(max-width: 794px) 100vw, 794px"></p>
<p>Mô hình thực hiện gồm 03 redis server. Trong đó:</p>
<ul>
<li>01 redis master: 192.168.10.111<p></p>
</li>
<li>
<p>02 redis slave: 192.168.10.112 và 192.168.10.186</p>
</li>
</ul>
<p>Một số công việc cần thực hiện: cài đặt redis, cấu hình redis sentinel.</p>
<h2>1. Cài đặt và cấu hình Redis từ source</h2>
<p><strong>Step1: Installing redis</strong></p>
<pre>cd /opt
curl -O http://download.redis.io/redis-stable.tar.gz
tar xzvf redis-stable.tar.gz cd redis-stable
make
cp src/redis-cli /usr/bin/redis-cli
cp src/redis-server /usr/bin/redis-server</pre>
<p><strong>Step2: Configure redis</strong></p>
<ul>
<li>Tạo thư mục chứa cấu hình redis</li>
</ul>
<p>sudo mkdir /etc/redis</p>
<ul>
<li>Copy tệp mẫu redis.conf</li>
</ul>
<p>sudo cp /opt/redis-stable/redis.conf /etc/redis</p>
<ul>
<li>Sửa đổi tệp /etc/redis/redis.conf với thay đổi sau</li>
</ul>
<p>Sử dụng supervised với hệ thống systemd. Chỉ định cố định thư mục chứa dump redis</p>
<pre>sed -i 's/supervised no/supervised systemd/' /etc/redis/redis.conf
sed -i 's/dir .\//dir \/var\/lib\/redis/' /etc/redis/redis.conf</pre>
<p>Sử dụng thông tin cho bash:</p>
<pre>mkdir /etc/redis
cp /opt/redis-stable/redis.conf /etc/redis
sed -i 's/supervised no/supervised systemd/' /etc/redis/redis.conf
sed -i 's/dir .\//dir \/var\/lib\/redis/' /etc/redis/redis.conf
sed -i '/bind 127.0.0.1/c\bind 0.0.0.0' /etc/redis/redis.conf</pre>
<p><strong>Step3: Thiết lập redis chạy với systemd</strong></p>
<div class="wordads-ad-wrapper" style="display: block;"><div class="wordads-ad"><div class="wordads-ad-title">Advertisement</div><div class="wordads-ad-content wordads-ad-inline" id="wordads-ad-890314"><script id="sas_script1" type="text/javascript" src="https://www15.smartadserver.com/ac?nwid=3905&amp;siteid=474853&amp;pgid=1572546&amp;fmtid=110354&amp;async=1&amp;visit=s&amp;tmstp=9160896765&amp;tgt=iab_category%3DIAB19%3Bwp_blog_id%3D91768504%3Blanguage%3Dvi%3B&amp;tag=wordads-ad-890314&amp;sh=1200&amp;sw=1920&amp;pgDomain=https%3A%2F%2Fvnsys.wordpress.com%2F2019%2F01%2F11%2Fredis-cluster-voi-sentinel%2F&amp;noadcbk=sas.noad&amp;ctsrcid=91768504&amp;isLazy=0&amp;isAdRefresh=0" async=""></script><iframe style="width: 300px; height: 250px; border: 0px; margin: 0px; padding: 0px;" srcdoc="<html lang=en>    <head>        <base target=_blank>        <meta charset=utf-8>        <meta name=viewport content=&quot;width=device-width,initial-scale=1&quot;>        <script src=//ns.sascdn.com/diff/templates/js/banner/sas-clicktag-3.1.js></script>        <style>html, body, div, span, applet, object, iframe,        h1, h2, h3, h4, h5, h6, p, blockquote, pre,        a, abbr, acronym, address, big, cite, code,        del, dfn, em, img, ins, kbd, q, s, samp,        small, strike, strong, sub, sup, tt, var,        b, u, i, center,        dl, dt, dd, ol, ul, li,        fieldset, form, label, legend,        table, caption, tbody, tfoot, thead, tr, th, td,        article, aside, canvas, details, embed,        figure, figcaption, footer, header, hgroup,        menu, nav, output, ruby, section, summary,        time, mark, audio, video {            margin: 0;            padding: 0;            border: 0;            font-size: 100%;            font: inherit;            vertical-align: baseline;        }        article, aside, details, figcaption, figure,        footer, header, hgroup, menu, nav, section {            display: block;        }        body {            line-height: 1;        }        ol, ul {            list-style: none;        }        blockquote, q {            quotes: none;        }        blockquote:before, blockquote:after,        q:before, q:after {            content: &quot;&quot;;            content: none;        }        table {            border-collapse: collapse;            border-spacing: 0;        }        </style>        <style>            .zokAxQFjZZ {              width: 300px;              height: 250px;              box-sizing: border-box;              align-content: start;              flex-wrap: nowrap;              display: flex;              flex-direction: column;              justify-content: space-between;              overflow: hidden;              background: #FFFFFF;              border: 1px solid rgba(0, 0, 0, 0.15);              border-radius: 3px;              font-family: -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, Roboto, Oxygen-Sans, Ubuntu, Cantarell, &quot;Helvetica Neue&quot;,sans-serif;              position: relative;            }            .QxWzPGbIAX {              background-color: #ffffff;              box-sizing: border-box;              font-weight: bold;              flex-shrink: 0;              flex-basis: auto;              min-height: 0;              padding: 12px;              font-size: 13px;              line-height: 16px;              z-index: 1;            }            .QxWzPGbIAX:empty {              visibility: hidden;            }            .XpzYzFbPiT {              position: absolute;              box-sizing: border-box;              flex-grow: 3;              flex-shrink: 2;              flex-basis: 146px;              min-height: 0;              overflow: hidden;              z-index: 0;            }            .XpzYzFbPiT img {              width: 100%;              height: 100%;              flex-shrink: 1;            }            .AjIWeuzhwX {              flex-basis: auto;              background-color: #ffffff;              white-space: pre-line;              font-weight: 400;              min-height: 0;              padding: 12px;              font-size: 15px;              flex-shrink: 0;              line-height: 21px;              z-index: 1;            }            .AjIWeuzhwX:empty {              visibility: hidden;            }            .no-image {              display: flex;              justify-content: center;              align-items: center;              height: 100%;              color: #50575E;              background-color: #fcfcfc;            }            .no-image span {              display: block;              margin-top: 10px;              font-style: italic;            }        </style>    </head>    <body>    <a href=&quot;https://itx5.smartadserver.com/click?imgid=30520256&amp;insid=11865144&amp;pgid=1572546&amp;fmtid=110354&amp;ckid=5984726182297457292&amp;uii=6366126238695234247&amp;acd=1695092331164&amp;opid=4a6329fc-62a4-4094-a944-ebf204736be7&amp;opdt=1695092331164&amp;tmstp=9160896765&amp;tgt=iab_category%3dIAB19%3bwp_blog_id%3d91768504%3blanguage%3dvi%3b%3b%24dt%3d1t&amp;systgt=%24qc%3d1500027877%3b%24ql%3dHigh%3b%24qpc%3d722720%3b%24qt%3d238_303_489816t%3b%24dma%3d0%3b%24b%3d14150%3b%24o%3d12100%3b%24sw%3d1920%3b%24sh%3d1200&amp;envtype=0&amp;imptype=2&amp;gdpr=0&amp;pgDomain=https%3a%2f%2fvnsys.wordpress.com%2f2019%2f01%2f11%2fredis-cluster-voi-sentinel%2f&amp;cappid=5984726182297457292&amp;ctsrcid=91768504&amp;go=https%3a%2f%2fthedailyworkfromhomeblog.wordpress.com%2f2023%2f07%2f30%2fwhat-is-the-super-affiliate-system-and-how-to-make-10000-a-month-from-home-as-a-super-affiliate-marketer%2f&quot; id=&quot;click-area1&quot; style=&quot;text-decoration: none; color: #000;&quot;>        <div class=&quot;zokAxQFjZZ click-area1&quot; onmouseover=&quot;this.style.cursor=pointer;&quot;>            <div class=&quot;QxWzPGbIAX&quot; id=&quot;title&quot;>What is The super affiliate system? A...</div>            <div class=&quot;XpzYzFbPiT&quot;><img crossorigin=&quot;anonymous&quot; style=&quot;width: 100%;&quot; id=&quot;imageUrl&quot; alt=&quot;image&quot; src=&quot;https://i0.wp.com/public-api.wordpress.com/wpcom/v2/wordads/dsp/api/v1/dsp/creatives/95590/image?w=600&amp;zoom=2&quot;></div>            <div class=&quot;AjIWeuzhwX&quot; id=&quot;snippet&quot;>The Super Affiliate System (SAS) is an online course developed by John Cresta...</div>        </div>    </a>    </body></html>"></iframe></div><div class="wordads-ad-controls"></div></div></div><p>Tạo tệp redis.service với thông tin sau để chạy với hệ thống systemd</p>
<pre>cat &gt;/etc/systemd/system/redis.service &lt;&lt;EOF
[Unit]
Description=Redis In-Memory Data Store
After=network.target

[Service]
User=redis
Group=redis
ExecStart=/usr/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/bin/redis-cli shutdown
Restart=always

[Install]
WantedBy=multi-user.target
EOF</pre>
<p>Tạo redis user và group để chạy redis</p>
<pre>adduser --system --no-create-home redis
mkdir /var/lib/redis
chown redis:redis /var/lib/redis
chmod 770 /var/lib/redis</pre>
<p>Start và enable redis service</p>
<pre>systemctl start redis.service
systemctl enable redis.service</pre>
<h2 class="western">2. Redis cluster with redis-sentinel</h2>
<p>Yêu cầu: Cài đặt redis trên 03 server. Mở firewall cho các port 6379 và 26389</p>
<p><strong>Step1: Cấu hình replication</strong></p>
<p>Chúng ta thiết lập cấu hình nhân bản redis trên các máy redis slave</p>
<p>Thực hiện thêm dòng sau vào tệp cấu hình redis.conf trên các máy redis slave:</p>
<pre>echo 'slaveof 192.168.10.111 6379' &gt;&gt;/etc/redis/redis.conf
systemctl restart redis.service</pre>
<p>Ở đây “<strong>slaveof</strong>”&nbsp;được sử dụng để thông báo cho redis cluster là server nào làm master và server nào được sử dụng làm slave. Khai báo thông tin master server là 192.168.10.111</p>
<p>Check thông tin replication trên một trong các redis server</p>
<pre>redis-cli info replication</pre>
<p style="text-align:justify;"><img data-attachment-id="1703" data-permalink="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/redis-cli-info-replication/" data-orig-file="https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png" data-orig-size="449,302" data-comments-opened="1" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="redis-cli info replication" data-image-description="" data-image-caption="" data-medium-file="https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png?w=300" data-large-file="https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png?w=449" class="  wp-image-1703 aligncenter" src="https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png?w=324&amp;h=218" alt="redis-cli info replication" width="324" height="218" srcset="https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png?w=324&amp;h=218 324w, https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png?w=150&amp;h=101 150w, https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png?w=300&amp;h=202 300w, https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png 449w" sizes="(max-width: 324px) 100vw, 324px"></p>
<div class="wordads-ad-wrapper" style="display: inherit;"><div id="atatags-26942-373942"></div><script>__ATA.cmd.push(function() {__ATA.initDynamicSlot({id: 'atatags-26942-373942',location: 310,formFactor: '001',label: {text: 'Quảng cáo',},creative: {reportAd: {text: 'Report this ad',},privacySettings: {text: 'Riêng tư',}}});});</script></div><p>Chúng ta thấy vai trò hiện tại của server02 là slave và địa chỉ IP của master hiện tại là 192.168.10.111.</p>
<ul>
<li>Tạo cập key để kiểm tra</li>
</ul>
<pre>redis-cli
127.0.0.1:6379&gt;set key value
127.0.0.1:6379&gt; SET key1 "Hello world!"
OK
127.0.0.1:6379&gt; SET key "Value"
OK</pre>
<p>Trên các máy slave thực hiện get key</p>
<pre>[root@server02 redis-stable]# redis-cli 
127.0.0.1:6379&gt; GET *
(nil)
127.0.0.1:6379&gt; KEYS *
1) "key"
2) "key1"
127.0.0.1:6379&gt; get key1
"Hello world!"
127.0.0.1:6379&gt;</pre>
<p><strong>Step2: Configure redis sentinel</strong></p>
<p>Trên 03 redis server, tạo tệp sentinel.conf trong thư mục /etc/redis/ với nội dung sau:</p>
<pre>cat &gt;/etc/redis/sentinel.conf&lt;&lt;EOF
daemonize yes
pidfile '/var/run/redis/redis-sentinel.pid'
logfile '/var/log/redis/redis-sentinel.log'
bind 0.0.0.0
port 26379
sentinel monitor mymaster 192.168.10.111 6379 2
sentinel down-after-milliseconds mymaster 2000
sentinel failover-timeout mymaster 60000
sentinel parallel-syncs mymaster 1
EOF</pre>
<p>Ở đây, chúng ta cấu hình redis-sentinel trên mỗi redis server với cùng các thông tin sau:</p>
<ul>
<li>Thiết lập redis cluster với tên “<strong>mymaster”</strong>. Chỉ định server <strong>192.168.10.111</strong> làm master hiện tại.</li>
<li><strong>down-after-milliseconds</strong>: thiết lập khoảng thời gian mili giây mà master server không phản hồi, khi đó nó được xác định là down.</li>
<li><strong>failover-timeout</strong>: khoảng thời gian mà failover lần tiếp theo sau khi thực hiện failover trước đó bị lỗi.</li>
<li><strong>parallel-syncs</strong>: thiết lập số slave mà có thể được cấu hình lại để sử dụng master mới sau khi failover tại một thời điểm. Ở đây, chúng ta thiết lập tại một thời điểm chỉ một slave được sử dụng.</li>
</ul>
<p><strong>Step3: Start redis-sentinel</strong></p>
<p>Chúng ta có thể tạo systemd cho redis-sentinel. Thực hiện tạo tệp như sau:</p>
<pre>cat &gt;/etc/systemd/system/redis-sentinel.service&lt;&lt;EOF
[Unit]
Description=Redis Sentinel
After=network.target
[Service]
ExecStart=/usr/bin/redis-server /etc/redis/sentinel.conf --sentinel --daemonize no
Restart=always
[Install]
WantedBy=multi-user.target
EOF</pre>
<p>Thực hiện start và enable redis-sentinel với systemd</p>
<pre>systemctl start redis-sentinel
systemctl enable redis-sentinel</pre>
<p><strong>Step4: Check redis-sentinel</strong></p>
<ul>
<li>Kiểm tra log redis-sentinel</li>
</ul>
<p><img data-attachment-id="1704" data-permalink="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/redis-log/" data-orig-file="https://vnsys.files.wordpress.com/2019/01/redis-log.png" data-orig-size="775,258" data-comments-opened="1" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="redis log" data-image-description="" data-image-caption="" data-medium-file="https://vnsys.files.wordpress.com/2019/01/redis-log.png?w=300" data-large-file="https://vnsys.files.wordpress.com/2019/01/redis-log.png?w=614" class="  wp-image-1704 aligncenter" src="https://vnsys.files.wordpress.com/2019/01/redis-log.png?w=566&amp;h=188" alt="redis log" width="566" height="188" srcset="https://vnsys.files.wordpress.com/2019/01/redis-log.png?w=566&amp;h=188 566w, https://vnsys.files.wordpress.com/2019/01/redis-log.png?w=150&amp;h=50 150w, https://vnsys.files.wordpress.com/2019/01/redis-log.png?w=300&amp;h=100 300w, https://vnsys.files.wordpress.com/2019/01/redis-log.png?w=768&amp;h=256 768w, https://vnsys.files.wordpress.com/2019/01/redis-log.png 775w" sizes="(max-width: 566px) 100vw, 566px"></p>
<div class="wordads-ad-wrapper" style="display: inherit;"><div id="atatags-26942-386971"></div><script>__ATA.cmd.push(function() {__ATA.initDynamicSlot({id: 'atatags-26942-386971',location: 310,formFactor: '001',label: {text: 'Quảng cáo',},creative: {reportAd: {text: 'Report this ad',},privacySettings: {text: 'Riêng tư',}}});});</script></div><p>Khi đó chúng ta thấy redis master hiện tại là 192.168.10.111 và các slave là 192.168.10.112 và 192.168.10.186. Nếu kiểm tra tệp cấu hình sentinel.conf lúc này, trên mỗi redis server tệp sentinel.conf sẽ tự động cập nhật thông tin cấu hình cuối và nội dung sẽ gồm thông tin các redis server còn lại mà sentinel sẽ giám sát và current-epoch 0.</p>
<ul>
<li>Kiểm tra redis nào hiện tại đang là master</li>
</ul>
<p>Chúng ta có thể thực hiện kiểm tra với dòng lệnh sau trên mất kỳ redis server.</p>
<pre>localhost:26379&gt; sentinel get-master-addr-by-name mymaster
1) "192.168.10.111"
2) "6379"</pre>
<ul><div class="entry">
					<p>Có nhiều công cụ hỗ trợ cho tạo redis cluster như: Sentinel, Dynomite, Reborn, … Trong bài viết này, chúng ta sẽ sử dụng sentinel (một tính năng có sẵn trong redis) để thực hiện giám sát trạng thái hoạt động của redis cluster và thực hiện failover tự động. Đảm bảo rằng hệ thống redis sẽ vẫn hoạt động khi có một trong số các redis server trong cụm cluster down.<img data-attachment-id="1707" data-permalink="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/redis-cluster-with-sentinel/" data-orig-file="https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg" data-orig-size="794,546" data-comments-opened="1" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="redis cluster with sentinel" data-image-description="" data-image-caption="" data-medium-file="https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg?w=300" data-large-file="https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg?w=614" class="alignnone size-full wp-image-1707" src="https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg?w=614" alt="redis cluster with sentinel" srcset="https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg 794w, https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg?w=150&amp;h=103 150w, https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg?w=300&amp;h=206 300w, https://vnsys.files.wordpress.com/2019/01/redis-cluster-with-sentinel.jpg?w=768&amp;h=528 768w" sizes="(max-width: 794px) 100vw, 794px"></p>
<p>Mô hình thực hiện gồm 03 redis server. Trong đó:</p>
<ul>
<li>01 redis master: 192.168.10.111<p></p>
</li>
<li>
<p>02 redis slave: 192.168.10.112 và 192.168.10.186</p>
</li>
</ul>
<p>Một số công việc cần thực hiện: cài đặt redis, cấu hình redis sentinel.</p>
<h2>1. Cài đặt và cấu hình Redis từ source</h2>
<p><strong>Step1: Installing redis</strong></p>
<pre>cd /opt
curl -O http://download.redis.io/redis-stable.tar.gz
tar xzvf redis-stable.tar.gz cd redis-stable
make
cp src/redis-cli /usr/bin/redis-cli
cp src/redis-server /usr/bin/redis-server</pre>
<p><strong>Step2: Configure redis</strong></p>
<ul>
<li>Tạo thư mục chứa cấu hình redis</li>
</ul>
<p>sudo mkdir /etc/redis</p>
<ul>
<li>Copy tệp mẫu redis.conf</li>
</ul>
<p>sudo cp /opt/redis-stable/redis.conf /etc/redis</p>
<ul>
<li>Sửa đổi tệp /etc/redis/redis.conf với thay đổi sau</li>
</ul>
<p>Sử dụng supervised với hệ thống systemd. Chỉ định cố định thư mục chứa dump redis</p>
<pre>sed -i 's/supervised no/supervised systemd/' /etc/redis/redis.conf
sed -i 's/dir .\//dir \/var\/lib\/redis/' /etc/redis/redis.conf</pre>
<p>Sử dụng thông tin cho bash:</p>
<pre>mkdir /etc/redis
cp /opt/redis-stable/redis.conf /etc/redis
sed -i 's/supervised no/supervised systemd/' /etc/redis/redis.conf
sed -i 's/dir .\//dir \/var\/lib\/redis/' /etc/redis/redis.conf
sed -i '/bind 127.0.0.1/c\bind 0.0.0.0' /etc/redis/redis.conf</pre>
<p><strong>Step3: Thiết lập redis chạy với systemd</strong></p>
<p>Tạo tệp redis.service với thông tin sau để chạy với hệ thống systemd</p>
<pre>cat &gt;/etc/systemd/system/redis.service &lt;&lt;EOF
[Unit]
Description=Redis In-Memory Data Store
After=network.target

[Service]
User=redis
Group=redis
ExecStart=/usr/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/bin/redis-cli shutdown
Restart=always

[Install]
WantedBy=multi-user.target
EOF</pre>
<p>Tạo redis user và group để chạy redis</p>
<pre>adduser --system --no-create-home redis
mkdir /var/lib/redis
chown redis:redis /var/lib/redis
chmod 770 /var/lib/redis</pre>
<p>Start và enable redis service</p>
<pre>systemctl start redis.service
systemctl enable redis.service</pre>
<h2 class="western">2. Redis cluster with redis-sentinel</h2>
<p>Yêu cầu: Cài đặt redis trên 03 server. Mở firewall cho các port 6379 và 26389</p>
<p><strong>Step1: Cấu hình replication</strong></p>
<p>Chúng ta thiết lập cấu hình nhân bản redis trên các máy redis slave</p>
<p>Thực hiện thêm dòng sau vào tệp cấu hình redis.conf trên các máy redis slave:</p>
<pre>echo 'slaveof 192.168.10.111 6379' &gt;&gt;/etc/redis/redis.conf
systemctl restart redis.service</pre>
<p>Ở đây “<strong>slaveof</strong>”&nbsp;được sử dụng để thông báo cho redis cluster là server nào làm master và server nào được sử dụng làm slave. Khai báo thông tin master server là 192.168.10.111</p>
<p>Check thông tin replication trên một trong các redis server</p>
<pre>redis-cli info replication</pre>
<p style="text-align:justify;"><img data-attachment-id="1703" data-permalink="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/redis-cli-info-replication/" data-orig-file="https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png" data-orig-size="449,302" data-comments-opened="1" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="redis-cli info replication" data-image-description="" data-image-caption="" data-medium-file="https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png?w=300" data-large-file="https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png?w=449" class="  wp-image-1703 aligncenter" src="https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png?w=324&amp;h=218" alt="redis-cli info replication" width="324" height="218" srcset="https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png?w=324&amp;h=218 324w, https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png?w=150&amp;h=101 150w, https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png?w=300&amp;h=202 300w, https://vnsys.files.wordpress.com/2019/01/redis-cli-info-replication.png 449w" sizes="(max-width: 324px) 100vw, 324px"></p>
<div class="wordads-ad-wrapper" style="display: inherit;"><div id="atatags-26942-373942"></div><script>__ATA.cmd.push(function() {__ATA.initDynamicSlot({id: 'atatags-26942-373942',location: 310,formFactor: '001',label: {text: 'Quảng cáo',},creative: {reportAd: {text: 'Report this ad',},privacySettings: {text: 'Riêng tư',}}});});</script></div><p>Chúng ta thấy vai trò hiện tại của server02 là slave và địa chỉ IP của master hiện tại là 192.168.10.111.</p>
<ul>
<li>Tạo cập key để kiểm tra</li>
</ul>
<pre>redis-cli
127.0.0.1:6379&gt;set key value
127.0.0.1:6379&gt; SET key1 "Hello world!"
OK
127.0.0.1:6379&gt; SET key "Value"
OK</pre>
<p>Trên các máy slave thực hiện get key</p>
<pre>[root@server02 redis-stable]# redis-cli 
127.0.0.1:6379&gt; GET *
(nil)
127.0.0.1:6379&gt; KEYS *
1) "key"
2) "key1"
127.0.0.1:6379&gt; get key1
"Hello world!"
127.0.0.1:6379&gt;</pre>
<p><strong>Step2: Configure redis sentinel</strong></p>
<p>Trên 03 redis server, tạo tệp sentinel.conf trong thư mục /etc/redis/ với nội dung sau:</p>
<pre>cat &gt;/etc/redis/sentinel.conf&lt;&lt;EOF
daemonize yes
pidfile '/var/run/redis/redis-sentinel.pid'
logfile '/var/log/redis/redis-sentinel.log'
bind 0.0.0.0
port 26379
sentinel monitor mymaster 192.168.10.111 6379 2
sentinel down-after-milliseconds mymaster 2000
sentinel failover-timeout mymaster 60000
sentinel parallel-syncs mymaster 1
EOF</pre>
<p>Ở đây, chúng ta cấu hình redis-sentinel trên mỗi redis server với cùng các thông tin sau:</p>
<ul>
<li>Thiết lập redis cluster với tên “<strong>mymaster”</strong>. Chỉ định server <strong>192.168.10.111</strong> làm master hiện tại.</li>
<li><strong>down-after-milliseconds</strong>: thiết lập khoảng thời gian mili giây mà master server không phản hồi, khi đó nó được xác định là down.</li>
<li><strong>failover-timeout</strong>: khoảng thời gian mà failover lần tiếp theo sau khi thực hiện failover trước đó bị lỗi.</li>
<li><strong>parallel-syncs</strong>: thiết lập số slave mà có thể được cấu hình lại để sử dụng master mới sau khi failover tại một thời điểm. Ở đây, chúng ta thiết lập tại một thời điểm chỉ một slave được sử dụng.</li>
</ul>
<p><strong>Step3: Start redis-sentinel</strong></p>
<p>Chúng ta có thể tạo systemd cho redis-sentinel. Thực hiện tạo tệp như sau:</p>
<pre>cat &gt;/etc/systemd/system/redis-sentinel.service&lt;&lt;EOF
[Unit]
Description=Redis Sentinel
After=network.target
[Service]
ExecStart=/usr/bin/redis-server /etc/redis/sentinel.conf --sentinel --daemonize no
Restart=always
[Install]
WantedBy=multi-user.target
EOF</pre>
<p>Thực hiện start và enable redis-sentinel với systemd</p>
<pre>systemctl start redis-sentinel
systemctl enable redis-sentinel</pre>
<p><strong>Step4: Check redis-sentinel</strong></p>
<ul>
<li>Kiểm tra log redis-sentinel</li>
</ul>
<p><img data-attachment-id="1704" data-permalink="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/redis-log/" data-orig-file="https://vnsys.files.wordpress.com/2019/01/redis-log.png" data-orig-size="775,258" data-comments-opened="1" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="redis log" data-image-description="" data-image-caption="" data-medium-file="https://vnsys.files.wordpress.com/2019/01/redis-log.png?w=300" data-large-file="https://vnsys.files.wordpress.com/2019/01/redis-log.png?w=614" class="  wp-image-1704 aligncenter" src="https://vnsys.files.wordpress.com/2019/01/redis-log.png?w=566&amp;h=188" alt="redis log" width="566" height="188" srcset="https://vnsys.files.wordpress.com/2019/01/redis-log.png?w=566&amp;h=188 566w, https://vnsys.files.wordpress.com/2019/01/redis-log.png?w=150&amp;h=50 150w, https://vnsys.files.wordpress.com/2019/01/redis-log.png?w=300&amp;h=100 300w, https://vnsys.files.wordpress.com/2019/01/redis-log.png?w=768&amp;h=256 768w, https://vnsys.files.wordpress.com/2019/01/redis-log.png 775w" sizes="(max-width: 566px) 100vw, 566px"></p>
<div class="wordads-ad-wrapper" style="display: inherit;"><div id="atatags-26942-386971"></div><script>__ATA.cmd.push(function() {__ATA.initDynamicSlot({id: 'atatags-26942-386971',location: 310,formFactor: '001',label: {text: 'Quảng cáo',},creative: {reportAd: {text: 'Report this ad',},privacySettings: {text: 'Riêng tư',}}});});</script></div><p>Khi đó chúng ta thấy redis master hiện tại là 192.168.10.111 và các slave là 192.168.10.112 và 192.168.10.186. Nếu kiểm tra tệp cấu hình sentinel.conf lúc này, trên mỗi redis server tệp sentinel.conf sẽ tự động cập nhật thông tin cấu hình cuối và nội dung sẽ gồm thông tin các redis server còn lại mà sentinel sẽ giám sát và current-epoch 0.</p>
<ul>
<li>Kiểm tra redis nào hiện tại đang là master</li>
</ul>
<p>Chúng ta có thể thực hiện kiểm tra với dòng lệnh sau trên mất kỳ redis server.</p>
<pre>localhost:26379&gt; sentinel get-master-addr-by-name mymaster
1) "192.168.10.111"
2) "6379"</pre>
<ul>
<li>Stop redis master và check log trên redis slave</li>
</ul>
<pre>systemctl stop redis.service
tail -f /var/log/redis/redis-sentinel.log</pre>
<p>Chúng ta xem log trên từng redis slave sau:</p>
<p><strong>Trên Server 192.168.10.112</strong></p>
<p><img data-attachment-id="1705" data-permalink="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/redis-sentinel-log-slave01/" data-orig-file="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png" data-orig-size="787,542" data-comments-opened="1" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="redis sentinel log slave01" data-image-description="" data-image-caption="" data-medium-file="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png?w=300" data-large-file="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png?w=614" class="  wp-image-1705 aligncenter" src="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png?w=511&amp;h=352" alt="redis sentinel log slave01" width="511" height="352" srcset="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png?w=511&amp;h=352 511w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png?w=150&amp;h=103 150w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png?w=300&amp;h=207 300w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png?w=768&amp;h=529 768w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png 787w" sizes="(max-width: 511px) 100vw, 511px"></p>
<p><strong>Trên Server 192.168.10.186</strong></p>
<p><img data-attachment-id="1706" data-permalink="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/redis-sentinel-log-slave02/" data-orig-file="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png" data-orig-size="873,352" data-comments-opened="1" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="redis sentinel log slave02" data-image-description="" data-image-caption="" data-medium-file="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png?w=300" data-large-file="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png?w=614" class="  wp-image-1706 aligncenter" src="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png?w=554&amp;h=224" alt="redis sentinel log slave02" width="554" height="224" srcset="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png?w=554&amp;h=224 554w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png?w=150&amp;h=60 150w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png?w=300&amp;h=121 300w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png?w=768&amp;h=310 768w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png 873w" sizes="(max-width: 554px) 100vw, 554px"></p>
<p>Quan sát log, chúng ta thấy tuần tự:</p>
<p>sentinel giám sát thấy master down được đánh dấu lần đầu là +sdown (subjectively down state). Sau khi các redis server, ở đây sử dụng sentinel để giám sát xác nhận redis master đã down thật, khi đó trạng thái được đánh dấu là +odown ( objectively down state). Tiếp theo, sentinel vote (+vote) một sentinel khác mà thực hiện cố gắng failover lần đầu. Chúng ta thấy có quá trình chuyển đổi failover giữa các redis server. Và slave 192.168.10.186 được chọn làm master.</p>
<p>Sử dụng lệnh để check redis server nào hiện tại đang có vai trò là master:</p>
<pre>[root@server02 redis-stable]# redis-cli -p 26379
127.0.0.1:26379&gt; SENTINEL get-master-addr-by-name mymaster
1) "192.168.10.186"
2) "6379"</pre>
<p>Tham khảo thêm về redis sentinel tại: <a href="https://redis.io/topics/sentinel" rel="nofollow">https://redis.io/topics/sentinel</a>.</p>
<div id="atatags-370373-65090e69954f5">
		<script type="text/javascript">
			__ATA.cmd.push(function() {
				__ATA.initVideoSlot('atatags-370373-65090e69954f5', {
					sectionId: '370373',
					format: 'inread'
				});
			});
		</script>
	</div>	<div id="ob_holder" style="display: none;"><iframe id="ob_iframe" style="display: none; width: 1px; height: 1px;" src="https://widgets.outbrain.com/widgetOBUserSync/obUserSync.html#pid=198143&amp;dmpenabled=true&amp;filterDMP=&amp;d=8wQCCBWuV8gJd6vwVAj6Pe-cgnpSdQr3IzndUob1hC2n41mFyU3D5zoTWtxIGK0j&amp;gdpr=0&amp;cmpNeeded=false&amp;gdprVer=2&amp;ccpa=1---&amp;country=VN&amp;obRecsAbtestAndVars=1024-3192,833-3367,386-1123,1090-3454,1125-3606,998-3234,1323-4540,1164-3777,1103-3503,1328-4554,784-2402,1169-3791,1203-3987,1300-4501,981-4504,792-2426,1309-4483,1149-3716,927-3026"></iframe></div><div class="OUTBRAIN wa-ob-widget" data-ob-contenturl="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/" data-widget-id="AR_1" data-ob-installation-key="WORDP263NC92GIANECJP6HEPM" data-ob-psub="tech_computing" data-ob-mark="true" data-browser="safari" data-os="macintel" data-dynload="" data-idx="0" id="outbrain_widget_0"><div class="ob-widget ob-grid-layout AR_1" data-dynamic-truncate="true">
                        </div></div>
	<script>
		window._stq = window._stq || [];
		window._stq.push( [ 'extra', { x_wordads_outbrain: 'widget_render_ar_1', }, ] );
	</script><div id="jp-post-flair" class="sharedaddy sd-like-enabled sd-sharing-enabled"><div class="sharedaddy sd-sharing-enabled"><div class="robots-nocontent sd-block sd-social sd-social-icon-text sd-sharing"><h3 class="sd-title">Chia sẻ:</h3><div class="sd-content"><ul data-sharing-events-added="true"><li class="share-twitter"><a rel="nofollow noopener noreferrer" data-shared="sharing-twitter-1702" class="share-twitter sd-button share-icon" href="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/?share=twitter&amp;nb=1" target="_blank" title="Bấm để chia sẻ trên Twitter"><span>Twitter</span></a></li><li class="share-facebook"><a rel="nofollow noopener noreferrer" data-shared="sharing-facebook-1702" class="share-facebook sd-button share-icon" href="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/?share=facebook&amp;nb=1" target="_blank" title="Nhấn vào chia sẻ trên Facebook"><span>Facebook</span></a></li><li class="share-telegram"><a rel="nofollow noopener noreferrer" data-shared="" class="share-telegram sd-button share-icon" href="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/?share=telegram&amp;nb=1" target="_blank" title="Nhấn để chia sẻ lên Telegram"><span>Telegram</span></a></li><li class="share-end"></li></ul></div></div></div><div class="sharedaddy sd-block sd-like jetpack-likes-widget-wrapper jetpack-likes-widget-loaded" id="like-post-wrapper-91768504-1702-65090e6996614" data-src="//widgets.wp.com/likes/index.html?ver=20230906#blog_id=91768504&amp;post_id=1702&amp;origin=vnsys.wordpress.com&amp;obj_id=91768504-1702-65090e6996614" data-name="like-post-frame-91768504-1702-65090e6996614" data-title="Thích hoặc Đăng lại"><h3 class="sd-title">Thích bài này:</h3><div class="likes-widget-placeholder post-likes-widget-placeholder" style="height: 55px; display: none;"><span class="button"><span>Thích</span></span> <span class="loading">Đang tải...</span></div><iframe class="post-likes-widget jetpack-likes-widget" name="like-post-frame-91768504-1702-65090e6996614" src="//widgets.wp.com/likes/index.html?ver=20230906#blog_id=91768504&amp;post_id=1702&amp;origin=vnsys.wordpress.com&amp;obj_id=91768504-1702-65090e6996614" height="55px" width="100%" frameborder="0" scrolling="no" title="Thích hoặc Đăng lại"></iframe><span class="sd-text-color"></span><a class="sd-link-color"></a></div>
<div id="jp-relatedposts" class="jp-relatedposts" style="display: block;">
	<h3 class="jp-relatedposts-headline"><em>Có liên quan</em></h3>
<div class="jp-relatedposts-items jp-relatedposts-items-visual jp-relatedposts-grid "><div class="jp-relatedposts-post jp-relatedposts-post0 jp-relatedposts-post-thumbs" data-post-id="1711" data-post-format="false"><a class="jp-relatedposts-post-a" href="/2019/01/16/ha-redis-sentinel-su-dung-haproxy/?relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0" title="HA redis sentinel sử dụng&amp;nbsp;HAProxy" data-origin="1702" data-position="0"><img class="jp-relatedposts-post-img" loading="lazy" src="https://vnsys.files.wordpress.com/2019/01/check-redis-ha-with-haproxy1-1.png?w=350&amp;h=200&amp;crop=1" width="350" height="200" srcset="https://vnsys.files.wordpress.com/2019/01/check-redis-ha-with-haproxy1-1.png?w=350&amp;h=200&amp;crop=1 1x, https://vnsys.files.wordpress.com/2019/01/check-redis-ha-with-haproxy1-1.png?w=525&amp;h=300&amp;crop=1 1.5x, https://vnsys.files.wordpress.com/2019/01/check-redis-ha-with-haproxy1-1.png?w=700&amp;h=400&amp;crop=1 2x" alt="check redis ha with haproxy"></a><h4 class="jp-relatedposts-post-title"><a class="jp-relatedposts-post-a" href="/2019/01/16/ha-redis-sentinel-su-dung-haproxy/?relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0" title="HA redis sentinel sử dụng&amp;nbsp;HAProxy" data-origin="1702" data-position="0">HA redis sentinel sử dụng&nbsp;HAProxy</a></h4><p class="jp-relatedposts-post-excerpt">Trong phần trước "Redis cluster với sentinel", chúng ta đã cài đặt và cấu hình redis cluster mà sử dụng sentinel. Trong bài viết này chúng ta sẽ sử dụng HAProxy để cấu hình chạy HA cho redis sentinel.</p><time class="jp-relatedposts-post-date" datetime="Tháng Một 16, 2019" style="display: block;">Tháng Một 16, 2019</time><p class="jp-relatedposts-post-context">Trong "HAProxy"</p></div><div class="jp-relatedposts-post jp-relatedposts-post1 jp-relatedposts-post-thumbs" data-post-id="1827" data-post-format="false"><a class="jp-relatedposts-post-a" href="/2019/11/03/redis-cluster-voi-nhieu-master-node/?relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1" title="Redis cluster với nhiều master&amp;nbsp;node" data-origin="1702" data-position="1"><img class="jp-relatedposts-post-img" loading="lazy" src="https://vnsys.files.wordpress.com/2019/11/redis-cluster.png?w=350&amp;h=200&amp;crop=1" width="350" height="200" srcset="https://vnsys.files.wordpress.com/2019/11/redis-cluster.png?w=350&amp;h=200&amp;crop=1 1x, https://vnsys.files.wordpress.com/2019/11/redis-cluster.png?w=525&amp;h=300&amp;crop=1 1.5x" alt="redis cluster"></a><h4 class="jp-relatedposts-post-title"><a class="jp-relatedposts-post-a" href="/2019/11/03/redis-cluster-voi-nhieu-master-node/?relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1" title="Redis cluster với nhiều master&amp;nbsp;node" data-origin="1702" data-position="1">Redis cluster với nhiều master&nbsp;node</a></h4><p class="jp-relatedposts-post-excerpt">Redis cluster cung cấp cách thức cài đặt redis mà dữ liệu tự động sharding qua nhiều node</p><time class="jp-relatedposts-post-date" datetime="Tháng Mười Một 3, 2019" style="display: block;">Tháng Mười Một 3, 2019</time><p class="jp-relatedposts-post-context">Trong "Redis"</p></div><div class="jp-relatedposts-post jp-relatedposts-post2 jp-relatedposts-post-thumbs" data-post-id="1849" data-post-format="false"><a class="jp-relatedposts-post-a" href="/2020/03/29/monitoring-redis-cluster-voi-prometheus/?relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2" title="Monitoring Redis Cluster với&amp;nbsp;prometheus" data-origin="1702" data-position="2"><img class="jp-relatedposts-post-img" loading="lazy" src="https://i0.wp.com/raw.githubusercontent.com/keepwalking86/prometheus/master/images/prometheus-granfana-redis-cluster-diagram.jpg?resize=350%2C200&amp;ssl=1" width="200" height="114" alt="" srcset="https://i0.wp.com/raw.githubusercontent.com/keepwalking86/prometheus/master/images/prometheus-granfana-redis-cluster-diagram.jpg?resize=350%2C200&amp;ssl=1&amp;zoom=2 2x" scale="2"></a><h4 class="jp-relatedposts-post-title"><a class="jp-relatedposts-post-a" href="/2020/03/29/monitoring-redis-cluster-voi-prometheus/?relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2" title="Monitoring Redis Cluster với&amp;nbsp;prometheus" data-origin="1702" data-position="2">Monitoring Redis Cluster với&nbsp;prometheus</a></h4><p class="jp-relatedposts-post-excerpt">Monitoring Redis Cluster với prometheus</p><time class="jp-relatedposts-post-date" datetime="Tháng Ba 29, 2020" style="display: block;">Tháng Ba 29, 2020</time><p class="jp-relatedposts-post-context">Trong "Monitor"</p></div></div></div></div>														</div>
<li>Stop redis master và check log trên redis slave</li>
</ul>
<pre>systemctl stop redis.service
tail -f /var/log/redis/redis-sentinel.log</pre>
<p>Chúng ta xem log trên từng redis slave sau:</p>
<p><strong>Trên Server 192.168.10.112</strong></p>
<p><img data-attachment-id="1705" data-permalink="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/redis-sentinel-log-slave01/" data-orig-file="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png" data-orig-size="787,542" data-comments-opened="1" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="redis sentinel log slave01" data-image-description="" data-image-caption="" data-medium-file="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png?w=300" data-large-file="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png?w=614" class="  wp-image-1705 aligncenter" src="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png?w=511&amp;h=352" alt="redis sentinel log slave01" width="511" height="352" srcset="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png?w=511&amp;h=352 511w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png?w=150&amp;h=103 150w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png?w=300&amp;h=207 300w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png?w=768&amp;h=529 768w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave01.png 787w" sizes="(max-width: 511px) 100vw, 511px"></p>
<p><strong>Trên Server 192.168.10.186</strong></p>
<p><img data-attachment-id="1706" data-permalink="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/redis-sentinel-log-slave02/" data-orig-file="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png" data-orig-size="873,352" data-comments-opened="1" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="redis sentinel log slave02" data-image-description="" data-image-caption="" data-medium-file="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png?w=300" data-large-file="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png?w=614" class="  wp-image-1706 aligncenter" src="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png?w=554&amp;h=224" alt="redis sentinel log slave02" width="554" height="224" srcset="https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png?w=554&amp;h=224 554w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png?w=150&amp;h=60 150w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png?w=300&amp;h=121 300w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png?w=768&amp;h=310 768w, https://vnsys.files.wordpress.com/2019/01/redis-sentinel-log-slave02.png 873w" sizes="(max-width: 554px) 100vw, 554px"></p>
<p>Quan sát log, chúng ta thấy tuần tự:</p>
<p>sentinel giám sát thấy master down được đánh dấu lần đầu là +sdown (subjectively down state). Sau khi các redis server, ở đây sử dụng sentinel để giám sát xác nhận redis master đã down thật, khi đó trạng thái được đánh dấu là +odown ( objectively down state). Tiếp theo, sentinel vote (+vote) một sentinel khác mà thực hiện cố gắng failover lần đầu. Chúng ta thấy có quá trình chuyển đổi failover giữa các redis server. Và slave 192.168.10.186 được chọn làm master.</p>
<p>Sử dụng lệnh để check redis server nào hiện tại đang có vai trò là master:</p>
<pre>[root@server02 redis-stable]# redis-cli -p 26379
127.0.0.1:26379&gt; SENTINEL get-master-addr-by-name mymaster
1) "192.168.10.186"
2) "6379"</pre>
<p>Tham khảo thêm về redis sentinel tại: <a href="https://redis.io/topics/sentinel" rel="nofollow">https://redis.io/topics/sentinel</a>.</p>
<div id="atatags-370373-65090e69954f5">
		<script type="text/javascript">
			__ATA.cmd.push(function() {
				__ATA.initVideoSlot('atatags-370373-65090e69954f5', {
					sectionId: '370373',
					format: 'inread'
				});
			});
		</script>
	</div>	<div id="ob_holder" style="display: none;"><iframe id="ob_iframe" style="display: none; width: 1px; height: 1px;" src="https://widgets.outbrain.com/widgetOBUserSync/obUserSync.html#pid=198143&amp;dmpenabled=true&amp;filterDMP=&amp;d=8wQCCBWuV8gJd6vwVAj6Pe-cgnpSdQr3IzndUob1hC2n41mFyU3D5zoTWtxIGK0j&amp;gdpr=0&amp;cmpNeeded=false&amp;gdprVer=2&amp;ccpa=1---&amp;country=VN&amp;obRecsAbtestAndVars=1024-3192,833-3367,386-1123,1090-3454,1125-3606,998-3234,1323-4540,1164-3777,1103-3503,1328-4554,784-2402,1169-3791,1203-3987,1300-4501,981-4504,792-2426,1309-4483,1149-3716,927-3026"></iframe></div><div class="OUTBRAIN wa-ob-widget" data-ob-contenturl="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/" data-widget-id="AR_1" data-ob-installation-key="WORDP263NC92GIANECJP6HEPM" data-ob-psub="tech_computing" data-ob-mark="true" data-browser="safari" data-os="macintel" data-dynload="" data-idx="0" id="outbrain_widget_0"><div class="ob-widget ob-grid-layout AR_1" data-dynamic-truncate="true">
                        </div></div>
	<script>
		window._stq = window._stq || [];
		window._stq.push( [ 'extra', { x_wordads_outbrain: 'widget_render_ar_1', }, ] );
	</script><div id="jp-post-flair" class="sharedaddy sd-like-enabled sd-sharing-enabled"><div class="sharedaddy sd-sharing-enabled"><div class="robots-nocontent sd-block sd-social sd-social-icon-text sd-sharing"><h3 class="sd-title">Chia sẻ:</h3><div class="sd-content"><ul data-sharing-events-added="true"><li class="share-twitter"><a rel="nofollow noopener noreferrer" data-shared="sharing-twitter-1702" class="share-twitter sd-button share-icon" href="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/?share=twitter&amp;nb=1" target="_blank" title="Bấm để chia sẻ trên Twitter"><span>Twitter</span></a></li><li class="share-facebook"><a rel="nofollow noopener noreferrer" data-shared="sharing-facebook-1702" class="share-facebook sd-button share-icon" href="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/?share=facebook&amp;nb=1" target="_blank" title="Nhấn vào chia sẻ trên Facebook"><span>Facebook</span></a></li><li class="share-telegram"><a rel="nofollow noopener noreferrer" data-shared="" class="share-telegram sd-button share-icon" href="https://vnsys.wordpress.com/2019/01/11/redis-cluster-voi-sentinel/?share=telegram&amp;nb=1" target="_blank" title="Nhấn để chia sẻ lên Telegram"><span>Telegram</span></a></li><li class="share-end"></li></ul></div></div></div><div class="sharedaddy sd-block sd-like jetpack-likes-widget-wrapper jetpack-likes-widget-loaded" id="like-post-wrapper-91768504-1702-65090e6996614" data-src="//widgets.wp.com/likes/index.html?ver=20230906#blog_id=91768504&amp;post_id=1702&amp;origin=vnsys.wordpress.com&amp;obj_id=91768504-1702-65090e6996614" data-name="like-post-frame-91768504-1702-65090e6996614" data-title="Thích hoặc Đăng lại"><h3 class="sd-title">Thích bài này:</h3><div class="likes-widget-placeholder post-likes-widget-placeholder" style="height: 55px; display: none;"><span class="button"><span>Thích</span></span> <span class="loading">Đang tải...</span></div><iframe class="post-likes-widget jetpack-likes-widget" name="like-post-frame-91768504-1702-65090e6996614" src="//widgets.wp.com/likes/index.html?ver=20230906#blog_id=91768504&amp;post_id=1702&amp;origin=vnsys.wordpress.com&amp;obj_id=91768504-1702-65090e6996614" height="55px" width="100%" frameborder="0" scrolling="no" title="Thích hoặc Đăng lại"></iframe><span class="sd-text-color"></span><a class="sd-link-color"></a></div>
<div id="jp-relatedposts" class="jp-relatedposts" style="display: block;">
	<h3 class="jp-relatedposts-headline"><em>Có liên quan</em></h3>
<div class="jp-relatedposts-items jp-relatedposts-items-visual jp-relatedposts-grid "><div class="jp-relatedposts-post jp-relatedposts-post0 jp-relatedposts-post-thumbs" data-post-id="1711" data-post-format="false"><a class="jp-relatedposts-post-a" href="/2019/01/16/ha-redis-sentinel-su-dung-haproxy/?relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0" title="HA redis sentinel sử dụng&amp;nbsp;HAProxy" data-origin="1702" data-position="0"><img class="jp-relatedposts-post-img" loading="lazy" src="https://vnsys.files.wordpress.com/2019/01/check-redis-ha-with-haproxy1-1.png?w=350&amp;h=200&amp;crop=1" width="350" height="200" srcset="https://vnsys.files.wordpress.com/2019/01/check-redis-ha-with-haproxy1-1.png?w=350&amp;h=200&amp;crop=1 1x, https://vnsys.files.wordpress.com/2019/01/check-redis-ha-with-haproxy1-1.png?w=525&amp;h=300&amp;crop=1 1.5x, https://vnsys.files.wordpress.com/2019/01/check-redis-ha-with-haproxy1-1.png?w=700&amp;h=400&amp;crop=1 2x" alt="check redis ha with haproxy"></a><h4 class="jp-relatedposts-post-title"><a class="jp-relatedposts-post-a" href="/2019/01/16/ha-redis-sentinel-su-dung-haproxy/?relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=0" title="HA redis sentinel sử dụng&amp;nbsp;HAProxy" data-origin="1702" data-position="0">HA redis sentinel sử dụng&nbsp;HAProxy</a></h4><p class="jp-relatedposts-post-excerpt">Trong phần trước "Redis cluster với sentinel", chúng ta đã cài đặt và cấu hình redis cluster mà sử dụng sentinel. Trong bài viết này chúng ta sẽ sử dụng HAProxy để cấu hình chạy HA cho redis sentinel.</p><time class="jp-relatedposts-post-date" datetime="Tháng Một 16, 2019" style="display: block;">Tháng Một 16, 2019</time><p class="jp-relatedposts-post-context">Trong "HAProxy"</p></div><div class="jp-relatedposts-post jp-relatedposts-post1 jp-relatedposts-post-thumbs" data-post-id="1827" data-post-format="false"><a class="jp-relatedposts-post-a" href="/2019/11/03/redis-cluster-voi-nhieu-master-node/?relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1" title="Redis cluster với nhiều master&amp;nbsp;node" data-origin="1702" data-position="1"><img class="jp-relatedposts-post-img" loading="lazy" src="https://vnsys.files.wordpress.com/2019/11/redis-cluster.png?w=350&amp;h=200&amp;crop=1" width="350" height="200" srcset="https://vnsys.files.wordpress.com/2019/11/redis-cluster.png?w=350&amp;h=200&amp;crop=1 1x, https://vnsys.files.wordpress.com/2019/11/redis-cluster.png?w=525&amp;h=300&amp;crop=1 1.5x" alt="redis cluster"></a><h4 class="jp-relatedposts-post-title"><a class="jp-relatedposts-post-a" href="/2019/11/03/redis-cluster-voi-nhieu-master-node/?relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=1" title="Redis cluster với nhiều master&amp;nbsp;node" data-origin="1702" data-position="1">Redis cluster với nhiều master&nbsp;node</a></h4><p class="jp-relatedposts-post-excerpt">Redis cluster cung cấp cách thức cài đặt redis mà dữ liệu tự động sharding qua nhiều node</p><time class="jp-relatedposts-post-date" datetime="Tháng Mười Một 3, 2019" style="display: block;">Tháng Mười Một 3, 2019</time><p class="jp-relatedposts-post-context">Trong "Redis"</p></div><div class="jp-relatedposts-post jp-relatedposts-post2 jp-relatedposts-post-thumbs" data-post-id="1849" data-post-format="false"><a class="jp-relatedposts-post-a" href="/2020/03/29/monitoring-redis-cluster-voi-prometheus/?relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2" title="Monitoring Redis Cluster với&amp;nbsp;prometheus" data-origin="1702" data-position="2"><img class="jp-relatedposts-post-img" loading="lazy" src="https://i0.wp.com/raw.githubusercontent.com/keepwalking86/prometheus/master/images/prometheus-granfana-redis-cluster-diagram.jpg?resize=350%2C200&amp;ssl=1" width="200" height="114" alt="" srcset="https://i0.wp.com/raw.githubusercontent.com/keepwalking86/prometheus/master/images/prometheus-granfana-redis-cluster-diagram.jpg?resize=350%2C200&amp;ssl=1&amp;zoom=2 2x" scale="2"></a><h4 class="jp-relatedposts-post-title"><a class="jp-relatedposts-post-a" href="/2020/03/29/monitoring-redis-cluster-voi-prometheus/?relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2&amp;relatedposts_hit=1&amp;relatedposts_origin=1702&amp;relatedposts_position=2" title="Monitoring Redis Cluster với&amp;nbsp;prometheus" data-origin="1702" data-position="2">Monitoring Redis Cluster với&nbsp;prometheus</a></h4><p class="jp-relatedposts-post-excerpt">Monitoring Redis Cluster với prometheus</p><time class="jp-relatedposts-post-date" datetime="Tháng Ba 29, 2020" style="display: block;">Tháng Ba 29, 2020</time><p class="jp-relatedposts-post-context">Trong "Monitor"</p></div></div></div></div>														</div>
