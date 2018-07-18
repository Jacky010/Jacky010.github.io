---
layout:     post
title:      Vue+ElementUI实现后台系统左侧层级菜单组件
subtitle:   后台系统菜单构建
date:       2018-07-17
author:     BY Jacky 
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - vue
    - element
    - 菜单
---


回顾之前项目中用到的后台管理系统左侧菜单构建。。。


## 正文

这里要涉及到的是使用vue+element来实现后台管理系统左侧层级菜单组件，vue是目前前端开发框架中使用最多的MVVM框架，而element是搭配vue使用的UI组件，
两者用于搭建后台管理系统在样式风格方面很有优势，那闲言碎语就到这，开始菜单的实现过程，Let's go!

#### 创建数据
由于在我之前所经历的项目，菜单的数据是后台人员存储在数据库中，然后通过请求再来渲染的。那现在由于没有对应的后台所需的服务，在此我就自己根据后台返
回来的数据格式自行编写数据用于实现。

数据形如以下：

```
  menuData: [
          {
            menuName: '商家中心', // 菜单显示名称
            menuId: '1',  // 菜单Id
            menuPid: '0', // 菜单父级Id
            menuUrl: '', // 菜单点击跳转的地址
            menuIcon: 'iconfont icon-Member' // 菜单名称前的图标
          },
          {
            menuName: '商家信息',
            menuId: '11',
            menuPid: '1',
            menuUrl: '/companyManage',
            menuIcon: ''
          },
          {
            menuName: '商家相册',
            menuId: '12',
            menuPid: '1',
            menuUrl: '/companyAlbum',
            menuIcon: ''
          },
          {
            menuName: '我的关注',
            menuId: '13',
            menuPid: '1',
            menuUrl: '/myFocus',
            menuIcon: ''
          },
          {
            menuName: '账户设置',
            menuId: '14',
            menuPid: '1',
            menuUrl: '/accountSetting',
            menuIcon: ''
          },
          {
            menuName: '活动管理',
            menuId: '2',
            menuPid: '0',
            menuUrl: '',
            menuIcon: 'iconfont icon-Enrolling'
          },
          {
            menuName: '活动促销',
            menuId: '21',
            menuPid: '2',
            menuUrl: '/activityPromotion',
            menuIcon: ''
          },
          {
            menuName: '优惠券',
            menuId: '22',
            menuPid: '2',
            menuUrl: '/couponList',
            menuIcon: ''
          },
          {
            menuName: '评论管理',
            menuId: '23',
            menuPid: '2',
            menuUrl: '/commentList',
            menuIcon: ''

          },
          {
            menuName: '订单管理',
            menuId: '3',
            menuPid: '0',
            menuUrl: '',
            menuIcon: 'iconfont icon-Enroll'
          },
          {
            menuName: '订单管理',
            menuId: '31',
            menuPid: '3',
            menuUrl: '/orderList',
            menuIcon: ''
          },
          {
            menuName: '财务管理',
            menuId: '4',
            menuPid: '0',
            menuUrl: '',
            menuIcon: 'iconfont icon-Works'
          },
          {
            menuName: '资产管理',
            menuId: '41',
            menuPid: '4',
            menuUrl: '/assetsList',
            menuIcon: ''
          },
          {
            menuName: '销售统计',
            menuId: '42',
            menuPid: '4',
            menuUrl: '/salesStatistics',
            menuIcon: ''
          },
          {
            menuName: '资金明细',
            menuId: '43',
            menuPid: '4',
            menuUrl: '/fundList',
            menuIcon: ''
          },
        ],
```

以上所展示的数据可以看到：1）如果是一级菜单，则menuPid为0，而二级菜单的menuPid则为一级菜单的menuId,这样就可以根据menuPid=0的找到一级菜单，
menuId=menuPid的则为二级菜单；2）一级菜单没有路由跳转地址，即menuUrl为空，而二级菜单是需要点击跳转的，menuUrl为vue-router中所写的路
由地址；3）一级菜单有小图标，二级菜单没有对应的小图标，menuIcon可以使用element自带的icon,也可使用iconfont进行替换；

#### 使用element组件

如何使用element组件？在vue项目中的main.js中引用，具体操作如下：

```
  import Element from 'element-ui'
  import 'element-ui/lib/theme-chalk/index.css'
  
  Vue.use(Element);
```

#### 编写组件模板

```
  <template>
    <article class="my-menu">
        <div class="logobox"  style="cursor: pointer"></div>
        <el-menu
            class="el-menu-vertical-demo"
            @open="handleOpen"
            @close="handleClose"
            background-color="#2b3541"
            text-color="rgba(255,255,255,0.5)"
            active-text-color="#fff"
            router
            unique-opened
            :default-active="getPath"
        >
          <el-submenu v-for="(item, num) in supLists" :key="num" :index="item.menuId">
            <template slot="title">
              <i :class="item.menuIcon" style="padding:0 18px 0 4px"></i><span
              slot="title">{{item.menuName}}</span>
            </template>
            <el-menu-item-group>
              <el-menu-item v-for="(list,index_num) in item.children" :key="index_num" :index="list.menuUrl">
                {{list.menuName}}
              </el-menu-item>
            </el-menu-item-group>
          </el-submenu>
        </el-menu>
    </article>
</template>
```

上面所使用的是element组件中的NavMenu导航菜单，具体请查看 [组件element](http://element-cn.eleme.io/#/zh-CN/component/menu).

注： 

router: 是否使用 vue-router 的模式，启用该模式会在激活导航时以 index 作为 path 进行路由跳转

default-active：当前激活菜单的 index（这里写了一个getPath方法用来获取当前激活菜单显示高亮）

unique-opened：保持一个子菜单的展开

### MyNav.vue组件详细代码

```
  <template>
      <article class="my-menu">
          <div class="logobox"  style="cursor: pointer"></div>
          <el-menu
              class="el-menu-vertical-demo"
              @open="handleOpen"
              @close="handleClose"
              background-color="#2b3541"
              text-color="rgba(255,255,255,0.5)"
              active-text-color="#fff"
              router
              unique-opened
              :default-active="getPath"
          >
            <el-submenu v-for="(item, num) in supLists" :key="num" :index="item.menuId">
              <template slot="title">
                <i :class="item.menuIcon" style="padding:0 18px 0 4px"></i><span
                slot="title">{{item.menuName}}</span>
              </template>
              <el-menu-item-group>
                <el-menu-item v-for="(list,index_num) in item.children" :key="index_num" :index="list.menuUrl">
                  {{list.menuName}}
                </el-menu-item>
              </el-menu-item-group>
            </el-submenu>
          </el-menu>
      </article>
  </template>

  <script>
    export default {
      name: "my-nav",
      data() {
        return {
          menuData: [
            {
              menuName: '商家中心',
              menuId: '1',
              menuPid: '0',
              menuUrl: '',
              menuIcon: 'iconfont icon-Member'
            },
            {
              menuName: '商家信息',
              menuId: '11',
              menuPid: '1',
              menuUrl: '/companyManage',
              menuIcon: ''
            },
            {
              menuName: '商家相册',
              menuId: '12',
              menuPid: '1',
              menuUrl: '/companyAlbum',
              menuIcon: ''
            },
            {
              menuName: '我的关注',
              menuId: '13',
              menuPid: '1',
              menuUrl: '/myFocus',
              menuIcon: ''
            },
            {
              menuName: '账户设置',
              menuId: '14',
              menuPid: '1',
              menuUrl: '/accountSetting',
              menuIcon: ''
            },
            {
              menuName: '活动管理',
              menuId: '2',
              menuPid: '0',
              menuUrl: '',
              menuIcon: 'iconfont icon-Enrolling'
            },
            {
              menuName: '活动促销',
              menuId: '21',
              menuPid: '2',
              menuUrl: '/activityPromotion',
              menuIcon: ''
            },
            {
              menuName: '优惠券',
              menuId: '22',
              menuPid: '2',
              menuUrl: '/couponList',
              menuIcon: ''
            },
            {
              menuName: '评论管理',
              menuId: '23',
              menuPid: '2',
              menuUrl: '/commentList',
              menuIcon: ''

            },
            {
              menuName: '订单管理',
              menuId: '3',
              menuPid: '0',
              menuUrl: '',
              menuIcon: 'iconfont icon-Enroll'
            },
            {
              menuName: '订单管理',
              menuId: '31',
              menuPid: '3',
              menuUrl: '/orderList',
              menuIcon: ''
            },
            {
              menuName: '财务管理',
              menuId: '4',
              menuPid: '0',
              menuUrl: '',
              menuIcon: 'iconfont icon-Works'
            },
            {
              menuName: '资产管理',
              menuId: '41',
              menuPid: '4',
              menuUrl: '/assetsList',
              menuIcon: ''
            },
            {
              menuName: '销售统计',
              menuId: '42',
              menuPid: '4',
              menuUrl: '/salesStatistics',
              menuIcon: ''
            },
            {
              menuName: '资金明细',
              menuId: '43',
              menuPid: '4',
              menuUrl: '/fundList',
              menuIcon: ''
            },
          ],
          menuUrls: [],
          supLists: [],// 一级菜单
          subLists: [],// 二级菜单
        }
      },
      methods: {
        handleOpen(key, keyPath) {
          //console.log(key, keyPath);
        },
        handleClose(key, keyPath) {
          //console.log(key, keyPath);
        },
        getMenu() {
          let me = this;
          me.menuUrls = me.menuData.map(item => item.menuUrl)
          me.menuData.forEach(function (item, index) {
            // 找出一级菜单
            if (item.menuPid == 0) {
              // 给一级菜单增加children属性
              me.supLists.push(Object.assign({children: []}, item))
            } else {
              me.subLists.push(item);
            }
          })


          // 往一级菜单里添加二级菜单，组成总菜单
          me.supLists.forEach((item, index) => {
            me.subLists.forEach((subItem, subIndex) => {
              if (subItem.menuPid == item.menuId) {
                item.children.push(subItem)
              }
            })
          })
          //console.log('supLists', me.supLists)
          //console.log(me.subLists)
        }
      },
      computed: {
        getPath() {
          let me = this;
          if (me.menuUrls.includes(me.$route.path)) {
            me.path = me.$route.path;
          }
          return me.path;
        }
      },
      created() {
        let me = this;
        me.getMenu();
      }
    }
  </script>

  <style scoped>
      .my-menu .el-submenu .el-menu-item {
          padding-left: 56px !important;
      }
  </style>
  <style scoped>
      .el-menu-vertical-demo:not(.el-menu--collapse) {
          width: 180px;
          min-height: 400px;
      }

      .logobox {
          height: 64px;
          width: 180px;
          transition: all ease-in-out 3s;
          /*background: #32ace3 url("../../../static/img/logo.png");*/
          float: left;
      }

      .my-menu .el-menu {
          border-right: 0px;
          /*height: 100%;*/
          /*margin-top: 64px;*/
          position: absolute;
          top: 64px;
          bottom: 0;
          left: 0;
      }

      .my-menu .el-submenu .el-menu-item {
          min-width: 179px !important;
      }

      .my-menu .el-menu-item.is-active {
          background-color: #2db7f5 !important;
      }
      .my-menu .el-menu {
          border-right: 0px;
          height: auto;
          overflow: auto;
          /* margin-top: 64px; */
          position: absolute;
          top: 64px;
          bottom: 0;
          left: 0;
      }
      .iconfont {
        padding-right: 8px;
      }
</style>
```

根据上述完整代码可以看出，层级菜单的实现思路是：根据menuPid=0找出一级菜单存放在supLists数组中，并在该数组中添加一个children属性用于存放
二级菜单的属性，menuPid不等于0的就是二级菜单放入sublists数组中；然后根据menuPid=menuId判断二级菜单属于哪个一级菜单push进children中存放；
在页面中通过v-for数组supLists渲染一级菜单，二级菜单v-for一级菜单的chrildren即可得到并渲染二级菜单，最终根据得到的数据展示出完整的左侧层级菜单。

#### 效果图

![Mou icon](https://cl.ly/0Q0v3g1C2y1d)

### 总结
这个组件不算怎么难，只要想明白其中的道道，实现起来还是很简单的，如果以后项目是通过从后台请求来的数据，那就可以把data里面的menuData设置为空数组
然后在methods里面请求过后替换menuData即可。路漫漫其修远兮，吾将上下而求索。。。

