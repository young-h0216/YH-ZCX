<template>
    <section class="bread">
        <div class="bread-wrap">
            <nav class="">
                <a href="">
                   精选与推荐
                </a>
                <slot name="bread"></slot>
            </nav>
        </div>
     
    </section>
</template>
<style>
  .bread{
      height: 50px;
      line-height: 45px;
      background-color:  #25282e;
  }
  .bread-wrap{
      padding: 0 10px;
      font-size: 14px;
      text-align: center;
      color: #f5f7fa;
  }
  .bread-wrap a{
    position: relative;
      margin-right: 20px;
  }
  .bread-wrap a:after{
      position: absolute;
      top: 0px;
      
      height:20px;
      line-height: 20px;
  }
  
</style>
<script>

    export default{
        data(){
            return{
                msg:'hello vue'
            }
        },
        beforeCreate: function() {
    //这里出现了生命周期钩子函数，如果是created就无效
    //对于created和mounted的区别，总结就是created的dom还没被vue的dom替换,其他都是一样的
    let self = this;
    let leftMenu = [ ];
    this.postRequest("admin/leftMenu", {
      token: ""
    }).then(function(response) {
      for (let num of response.data.data) {
        let menuItem = {};
        menuItem.subs = [];
        menuItem.index = num.menuName;
        menuItem.title = num.menuNameCn;
        menuItem.icon = `iconfont icon-${num.menuName}`;
        menuItem.isShow = num.isShow;
        if (num.childMenu !== null) {
          for (let subNum of num.childMenu) {
            let subMenu = {};
            subMenu.index = subNum.menuName;
            subMenu.isShow = subNum.isShow;
            subMenu.title = subNum.menuNameCn; //关于对象数组的遍历获取，肯定有更便捷的方法……
            menuItem.subs.push(subMenu); //这里是转换数据格式而不是遍历判断，模板中判断了一次有没有二三级菜单，JS中也需要，来转换数据格式
          }
        }
        leftMenu.push(menuItem);
      }
      self.items = leftMenu.concat(self.items);
    });
  },
  data() {
    return {
      collapse: false,
      // items:[]
      items: [],
    };
  },
  computed: {
    onRoutes() {
      return this.$route.path.replace("/", "");
    }
  },
  created() {
    // 通过 Event Bus 进行组件间通信，来折叠侧边栏
    bus.$on("collapse", msg => {
      this.collapse = msg;
    });
  }
};
    
</script>
