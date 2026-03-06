<template>
  <div class="detail">
    <h2>{{ project?.name }}</h2>
    <t-row :gutter="16">
      <t-col :span="3" v-for="(item, index) in statsList" :key="index">
        <t-card shadow>
          <div class="card">
            <div class="label">{{ item.name }}</div>
            <div class="view f ac jb">
              <div class="num">{{ item.num }}</div>
              <div class="icon">
                <component :is="item.icon" :size="32" />
              </div>
            </div>
          </div>
        </t-card>
      </t-col>
    </t-row>
  </div>
</template>

<script setup lang="ts">
import projectStore from "@/stores/project";
const { project } = storeToRefs(projectStore());

interface StatsItem {
  name: string;
  num: number;
  icon: string;
}
const statsList = ref<StatsItem[]>([
  { name: "角色数", num: 0, icon: "i-user" },
  { name: "剧本数", num: 0, icon: "i-document-folder" },
  { name: "分镜数", num: 0, icon: "i-carousel-video" },
  { name: "视频数", num: 0, icon: "i-video" },
]);
</script>

<style lang="scss" scoped>
.detail {
  .card {
    .label {
      font-size: 16px;
      font-weight: bold;
    }
    .view {
      margin-top: 1rem;
      .num {
        font-size: 32px;
        font-weight: bold;
      }
    }
  }
}
</style>
