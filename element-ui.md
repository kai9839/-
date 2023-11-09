## 1、el-menu侧边有白线

```
.el-menu {
	border-right:0!important;
}
```

为了更好的覆盖成功，使用 !important 可以有效解决



## 2、VUE Element-UI 的多级菜单动态渲染的组件



### 2.1.aside组件

```vue
<template>
    <div class="menu">

        <el-menu class="nav-menu" default-active="/" background-color="#002033" text-color="#fff"
            active-text-color="#ffd04b" router :collapse="isCollapse" :collapse-transition="isCollapse">
            <div class="logo">
                <img :class="{ 'no_collapse': isCollapse }" src="@/assets/img/logo.png">
            </div>
            <menuNav :menuData="menuData"></menuNav>
        </el-menu>
    </div>
</template>
  
<script>
import menuNav from './menuNav.vue';
export default {
    name: 'Aside',
    data() {
        return {
            menuData: [
                {
                    path: "/",
                    name: 'home',
                    label: '首页',
                    icon: 's-home',
                },
                {
                    path: "/article",
                    name: 'article',
                    label: '内容管理',
                    icon: 'document',
                },
                {
                    path: "/material",
                    name: 'material',
                    label: '素材管理',
                    icon: 'picture-outline',
                    children: [
                        {
                            path: "/imagess",
                            name: 'imagess',
                            label: '图片管理',
                            icon: 'picture-outline',
                        },
                        {
                            path: "/image",
                            name: 'image',
                            label: ' 音频管理',
                            icon: 'picture-outline',
                            children: [
                                {
                                    path: "/imagess",
                                    name: 'imagess',
                                    label: '图片管理',
                                    icon: 'picture-outline',
                                },
                                {
                                    path: "/image",
                                    name: 'image',
                                    label: ' 音频管理',
                                    icon: 'picture-outline',
                                }
                            ]
                        }
                    ]
                },
                {
                    path: "/publish",
                    name: 'publish',
                    label: '发布文章',
                    icon: 's-promotion',
                },
                {
                    path: "/settings",
                    name: 'settings',
                    label: '个人设置',
                    icon: 'setting',
                },
            ]
        }
    },
    components: { menuNav },
    computed: {
        isCollapse() {
            return this.$store.state.tab.isCollapse
        },

    }
}
</script>
  
<style scoped lang="less">
.menu {
    height: 100%;
    background-color: #002033;

    .nav-menu {
        height: 100%;
        transition: all 0.2s;
        border-right: 0;

        .el-menu-item {
            background-color: #fff;
        }

        .logo {
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            padding: 10px;

            img {
                width: 150px;
            }

            .no_collapse {
                width: 80px;
            }
        }

        .iconfont {
            margin-right: 10px;
            padding-left: 5px;
        }
    }
}
</style>
```

### 2.2.menuNav组件

```vue
<template>
    <div class="navMenu">
        <template v-for="item in menuData">
            
            <el-menu-item v-if="!item.children" :index="item.path">
                <i :class="'el-icon-' + item.icon"></i>
                <span slot="title">{{ item.label }}</span>
            </el-menu-item>

            <el-submenu v-else :index="item.path">
                <template slot="title">
                    <i :class="'el-icon-' + item.icon"></i>
                    <span>{{ item.label }}</span>
                </template>
                <menuNav :menuData="item.children"></menuNav>
            </el-submenu>
        </template>
    </div>
</template>

<script>
export default {
    name: 'menuNav',
    props: ['menuData'],
}
</script>

<style lang="less" scoped></style>
```

**总结**：当遇到多叉树或无限层级问题时，vue的递归组件是个比较好的解决方案，可以较大的节约开发时间降低开发成本。

