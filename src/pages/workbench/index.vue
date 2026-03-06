<template>
  <div class="main">
    <div class="menu fc jb">
      <div class="logoBox c">
        <img class="logo" src="@/assets/logo.png" />
      </div>
      <div class="itemBox fc ac">
        <t-tooltip
          :content="menu.label"
          placement="right"
          theme="light"
          destroyOnClose
          :showArrow="false"
          v-for="(menu, index) in menuList"
          :key="index">
          <div
            class="item fc c"
            v-if="menu.type === 'btn'"
            :class="{ active: activeMenu == menu.path, disabled: menu.needProject && !project }"
            @click="handleClick(menu)">
            <component :is="menu.icon" class="icon" />
          </div>
          <div class="divider" v-if="menu.type === 'divider'"></div>
        </t-tooltip>
      </div>
      <div class="footItem fc ac">
        <t-tooltip content="设置" placement="right" theme="light" destroyOnClose :showArrow="false">
          <div class="item c" @click="showSetting = true">
            <i-setting-one class="icon" />
          </div>
        </t-tooltip>
        <t-avatar style="margin-top: 8px" image="https://tdesign.gtimg.com/site/avatar.jpg" />
      </div>
    </div>
    <div class="view">
      <router-view />
    </div>
  </div>
  <setting />
</template>

<script setup lang="ts">
import setting from '@/components/setting/index.vue';

import projectStore from "@/stores/project";
const { project } = storeToRefs(projectStore());
import settingStore from "@/stores/setting";
const { showSetting } = storeToRefs(settingStore());

const menuList = ref([
  { type: "btn", path: "/project", label: "我的项目", icon: "i-folder-close" },
  { type: "btn", path: "/task", label: "任务中心", icon: "i-view-list" },
  { type: "divider" },
  // { type: "btn", path: "/detail", label: "项目总览", icon: "i-more-app", showTitle: true },
  { type: "btn", path: "/novel", label: "小说原文", icon: "i-notebook", needProject: true },
  { type: "btn", path: "/agent", label: "剧本Agent", icon: "i-color-filter", needProject: true },
  { type: "btn", path: "/script", label: "剧本管理", icon: "i-document-folder", needProject: true },
  { type: "btn", path: "/production", label: "视频生产", icon: "i-carousel-video", needProject: true },
  { type: "divider" },
  { type: "btn", path: "/assets", label: "资产中心", icon: "i-receive", needProject: true },
]);

const router = useRouter();
const route = useRoute();
const activeMenu = ref(route.path);

watch(
  () => route.path,
  (newPath) => {
    activeMenu.value = newPath;
  },
);

function handleClick(menu: any) {
  if (menu.needProject && !project.value) return;
  router.push(menu.path);
  activeMenu.value = menu.path;
}
</script>

<style lang="scss" scoped>
.main {
  height: 100vh;
  width: 100vw;
  padding: 1rem;
  background-color: #ebebeb;
  display: flex;

  .menu {
    width: 64px;
    height: 100%;
    background-color: #fff;
    border-radius: 16px;
    padding-top: 1rem;
    padding-bottom: 1rem;
    .logoBox {
      width: 100%;
      height: fit-content;
      .logo {
        width: 60%;
        height: auto;
      }
    }
    .itemBox {
      flex: 1;
      margin-top: 1rem;
      margin-bottom: 1rem;
      padding-top: 1rem;
      padding-bottom: 1rem;
      width: 100%;
      height: 100%;
      .item {
        margin-bottom: 4px;
        margin-top: 4px;
        cursor: pointer;
        width: 50px;
        height: 50px;
        .icon {
          font-size: 24px;
        }
        .title {
          font-size: 10px;
          white-space: nowrap;
        }
        &:hover {
          background-color: #ecedef;
          border-radius: 16px;
        }
      }
      .disabled {
        opacity: 0.3;
        cursor: not-allowed;
      }
      .active {
        background-color: #000 !important;
        color: #ebebeb;
        border-radius: 16px;
      }
      .divider {
        width: 50px;
        height: 1px;
        background-color: #ecedef;
        margin: 8px 0;
      }
    }
    .footItem {
      width: 100%;
      height: fit-content;
      .item {
        margin-top: 8px;
        cursor: pointer;
        width: 50px;
        height: 50px;
        .icon {
          font-size: 24px;
        }
        &:hover {
          background-color: #ecedef;
          border-radius: 16px;
        }
      }
      .active {
        background-color: #000 !important;
        color: #ebebeb;
        border-radius: 16px;
      }
    }
  }
  .view {
    flex: 1;
    margin-left: 1rem;
    background-color: #fff;
    border-radius: 16px;
    width: 100%;
    height: 100%;
    overflow-x: hidden;
    overflow-y: auto;
    padding-left: 2rem;
    padding-right: 2rem;
  }
}
</style>
