title: Hello World
date: 2018-11-07 03:44:01
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)

### How to build md

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |

``` bash
function showFadePop(){
    var url = "/j/api/detail/barrage";
    $.ajax({
        url: url,
        type: 'get',
        data: {
            'taobaoId':window.taobao_id,
            'page':1,
        },
        dataType: 'json',
        success: function(res) {
            if (res && res.statusCode == 200) {
                if(res.data && res.data.length){
                    var popData=[];
                    var errorImage = location.protocol + __uri('../../common/img/popImage.png');
                    for(var i=0;i<res.data.length;i++){
                        var item = res.data[i];
                        item.image = item.image ? item.image : errorImage;
                        var html = "<img src="+ item.image +" alt='' onerror='this.onerror=null;this.src="+errorImage+"' >"+ item.text;
                        popData.push(html);
                    }
                    var $box = $('.midpage-dealcontent');
                    fadePop.initPop(popData,$box,true);
                }
                
            } else {
                $.zheui.toast(res.displayErrorInfo);
            }
        },
        error: function() {
        }
    });
}
```