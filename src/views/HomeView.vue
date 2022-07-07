<template>
  <div class="tdesign-demo-item--layout">
    <t-layout>
      <t-header>
        <t-head-menu value="item1" height="120px">
          <img
              slot="logo"
              width="136"
              class="logo"
              src="https://www.tencent.com/img/index/menu_logo_hover.png"
              alt="logo"
          >

          <t-menu-item value="item1">已选内容</t-menu-item>
          <t-menu-item value="item2">菜单内容一</t-menu-item>
          <t-menu-item value="item3">菜单内容二</t-menu-item>
          <t-menu-item value="item4" :disabled="true">菜单内容三</t-menu-item>

          <template #operations>
            <a href="javascript:;">
              <icon class="t-menu__operations-icon" name="search"/>
            </a>
            <a href="javascript:;">
              <icon class="t-menu__operations-icon" name="notification-filled"/>
            </a>
            <a href="javascript:;">
              <icon class="t-menu__operations-icon" name="home"/>
            </a>
          </template>
        </t-head-menu>
      </t-header>
      <t-layout>
        <t-aside :width="asideWidth" >
          <t-menu theme="light" defaultValue="3-2" expandMutex :defaultExpanded="expanded" height="550px"
                  :collapsed="collapsed" @mouseenter.native="openMenu" @mouseleave.native="closeMenu">
            <t-submenu title="消息区" value="3">
              <template #icon>
                <icon name="mail"/>
              </template>
              <t-submenu title="二级菜单" value="3-1">
                <t-menu-item value="3-1-1">三级菜单内容</t-menu-item>
                <t-menu-item value="3-1-2">三级菜单内容</t-menu-item>
                <t-menu-item value="3-1-3">三级菜单内容</t-menu-item>
              </t-submenu>
              <t-submenu title="二级菜单" value="3-5">
                <t-menu-item value="3-5-1">三级菜单内容</t-menu-item>
                <t-menu-item value="3-5-2">三级菜单内容</t-menu-item>
                <t-menu-item value="3-5-3">三级菜单内容</t-menu-item>
              </t-submenu>
              <t-menu-item value="3-2">二级菜单内容</t-menu-item>
              <t-menu-item value="3-3">二级菜单内容</t-menu-item>
              <t-menu-item value="3-4">二级菜单内容</t-menu-item>
            </t-submenu>
            <t-menu-item value="user-circle">
              <template #icon>
                <icon name="user-circle"/>
              </template>
              个人中心
            </t-menu-item>
            <t-submenu disabled title="视频区" value="4">
              <template #icon>
                <icon name="play-circle"/>
              </template>
              <t-menu-item value="4-1">二级菜单内容</t-menu-item>
              <t-menu-item value="4-2">二级菜单内容</t-menu-item>
              <t-menu-item value="4-3">二级菜单内容</t-menu-item>
            </t-submenu>
            <t-menu-item value="edit1">
              <template #icon>
                <icon name="edit-1"/>
              </template>
              资源编辑
            </t-menu-item>
            <template #operations>
              <icon class="t-menu__operations-icon" name="view-list" @click.native="changeCollapsed"/>
            </template>
          </t-menu>
        </t-aside>
        <t-layout>
          <t-content>
            <app-deployment/>
          </t-content>
          <t-footer>Copyright @ 2019-{{ new Date().getFullYear() }} Tencent. All Rights Reserved</t-footer>
        </t-layout>
      </t-layout>
    </t-layout>
  </div>
</template>
<script>
import {Icon} from 'tdesign-icons-vue';
import AppDeployment from '@/components/AppDeployment'



export default {
  components: {
    Icon,
    AppDeployment
  },
  data() {
    return {
      collapsed: true,
      expanded: ['2', '3'],
      asideWidth: '80px'
    }
  },
  methods: {
    changeCollapsed() {
      this.collapsed = !this.collapsed
      if (this.collapsed) {
        this.asideWidth = '80px'
      } else {
        this.asideWidth = '232px'
      }
    },
    openMenu() {
      this.collapsed = false
      this.asideWidth = '232px'
    },
    closeMenu() {
      this.collapsed = true
      this.asideWidth = '80px'
    }
  }
};
</script>

<style lang="scss" scoped>
::v-deep .t-head-menu .t-menu {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
