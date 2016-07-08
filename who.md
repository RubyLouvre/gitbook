# who

<h2>谁在使用avalon</h2>
<p>欢迎各位使用者到QQ群找作者提交你们公司的LOGO与链接</p>
<script src="//cdn.bootcss.com/avalon.js/2.1.0/avalon.js"></script>
<script type="text/javascript">
    var logos = [
        {
            src: "qunar.jpg",
            link: "http://www.qunar.com/"
        }, {
            src: "wuxian.baidu.jpg",
            link: "http://wuxian.baidu.com/"
        }, {
            src: "market.baidu.jpg",
            link: "javascript:void 0"
        }, {
            src: "baidutuiguang.jpeg",
            link: "http://tuiguang.baidu.com"
        },
        {
            src: "bianfeng.png",
            link: "http://www.bianfeng.com/"
        },
        {
            src: "octmami_logo.png",
            link: "http://octmami.com/"
        },
        {
            src: "ccssoft.jpg",
            link: "http://www.ccssoft.com.cn/"
        }, {
            src: "maimaiys.png",
            link: "http://www.maimaiys.com/"
        }, {
            src: "quanshi_logo.jpg",
            link: "http://www.quanshi.com/"
        }, {
            src: "ronglian.png",
            link: "http://www.ronglian.com/"
        }, {
            src: "sdl_logo.png",
            link: "http://www.cloudary.com.cn/"
        }, {
            src: "sohulogo.png",
            link: "http://www.sohu.com/"
        }, {
            src: "ustack.png",
            link: "http://www.ustack.com/"
        }, {
            src: "wiz.jpg",
            link: "https://note.wiz.cn/"
        }, {
            src: "wps.jpg",
            link: "https://vip.wps.cn/"
        }, {
            src: "xiaojukeji.png",
            link: "http://www.xiaojukeji.com/website/index.html"
        }, {
            src: "isoftstone.png",
            link: "http://www.isoftstone.com/cn/industries/insurance.aspx?id=insurance"
        }, {
            src: "cevlink.png",
            link: "http://www.cevlink.com/"
        }, {
            src: "xiezhe.png",
            link: "http://xizhe.it"
        }, {
            src: "chinaedu.jpg",
            link: "http://www.chinaedu.net/"
        }, {
            src: "itangyuan.png",
            link: "http://itangyuan.com"
        }, {
            src: "syntop_logo.png",
            link: "http://www.syntop.com"
        }, {
            src: "angelcrunch.png",
            link: "http://angelcrunch.com/"
        }, {
            src: "newwwedu.png",
            link: "http://www.newwwedu.com/"
        }, {
            src: "vcb.jpg",
            link: "http://crmdemo.vcb.cn/loginview.aspx?ReturnUrl=/default.aspx"
        }, {
            src: "niaobushi.png",
            link: "http://www.niaobushi360.com/"
        }, {
            src: "mokylin.png",
            link: "http://www.mokylin.com/"
        }, {
            src: "aiispo.jpg",
            link: "http://aiispo.cn/"
        }, {
            src: "wohuizhong.png",
            link: "http://www.wohuizhong.com/"
        }, {
            src: "kagou.png",
            link: "http://www.cargopm.com/dzg-system-front/#!/home"
        }, {
            src: "easyzhx.png",
            link: "http://www.easyzhx.com/"
        },
        {
            src: "aoyou.jpg",
            title: "中青旅遨游网",
            link: "http://www.aoyou.com/"
        },
        {
            src: "86shop.jpg",
            title: "86店管家",
            link: "http://shop.86583.com/Login"
        },
        {
            src: "goopal.png",
            title: "果仁市场",
            link: "https://www.goopal.com.cn/wx/about-us.html"
        },
        {
            src: "qixin.png",
            title: "启信宝",
            link: "http://www.qixin.com/"
        }

    ];

    avalon.define({
        $id: "logos",
        logos: logos
    });
</script>
<style>
    .logos {
        width: 980px;
        margin: 0 auto;
        padding: 20px;
        overflow: hidden;
        *zoom: 1;
    }

    .logos li{
        float: left;
        width: 140px;
        height: 140px;
        line-height: 140px;
        overflow: hidden;
        margin: 24px;
        text-align:center;
        background: #fff;
        border-radius: 10px;
    }

    .logos li:after,.logos li span{
        display:inline-block;
        *display:inline;
        *zoom:1;
        width:0;
        height:100%;
        vertical-align:middle;
    }
    .logos li:after{
        content:'';
    }

    .logos li img{
        display:inline-block;
        *display:inline;
        *zoom:1;
        vertical-align:middle;
        font-size:16px;
        width: 120px;
    }
    .logos li a{
        display: inline-block;
        width: 100%;
        height: 100%;
    }
    .container {
        padding-right: 15px;
        padding-left: 15px;
        margin-right: auto;
        margin-left: auto;
    }
    .jumbotron{
        border-radius: 6px;

        position: relative;
        padding: 30px 0;
        color: rgba(255, 255, 255, 0.6);
        text-shadow: 0 1px 0 rgba(0, 0, 0, 0.1);
        background-color: #1ba9ba;
        background-image: -webkit-gradient(linear, left top, left bottom, from(#0088a4), to(#1ba9ba));
        background-image: -webkit-linear-gradient(top, #0088a4 0, #1ba9ba 100%);
        background-image: -o-linear-gradient(top, #0088a4 0, #1ba9ba 100%);
        background-image: linear-gradient(to bottom, #0088a4 0, #1ba9ba 100%);
        filter: progid:DXImageTransform.Microsoft.gradient(startColorstr='#0088a4', endColorstr='#1ba9ba', GradientType=0);
        background-repeat: repeat-x;

    }
    .jumbotron:after{
        position: static;
    }
    .projects-header {

        text-align: center;
        margin: 60px 0 10px;
        font-weight: 200;
        margin-bottom: 40px;
        display: block;
        margin-left: auto;
        margin-right: auto;
    }
    .projects-header h2{
        color:#1ba9ba;
    }
    .projects-header p{
        color:#ccc;
    }
    .page-header {
        padding-bottom: 9px;
        margin: 40px 0 20px;
        border-bottom: 1px solid #eee;
    }
</style>
<div class="row jumbotron">
    <ul class="logos" ms-controller="logos">

        <li ms-for="el in @logos">
            <a ms-attr="{href: el.link}" target="_blank" >
                <img ms-attr="{src: 'http://avalonjs.coding.me/share/img/logos/'+el.src}"/>
                <!--[if lt IE 8]><span></span><![endif]-->
            </a>
        </li>
    </ul>
</div>