<h2>谁在使用avalon</h2>

<p>欢迎各位使用者到QQ群找作者提交你们公司的LOGO与链接</p>

<script src="//cdn.bootcss.com/avalon.js/2.1.0/avalon.js"></script>


{% div class="row jumbotron" %}
    <ul class="logos" ms-controller="logos">
        <li ms-for="el in @logos">
            <a ms-attr="{href: el.link}" target="_blank" >
                <img ms-attr="{src: 'http://avalonjs.coding.me/share/img/logos/'+el.src}"/>
                <!--[if lt IE 8]><span></span><![endif]-->
            </a>
        </li>
    </ul>
{% enddiv %}
