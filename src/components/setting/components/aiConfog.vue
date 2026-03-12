<template>
  <div class="aiConfog">
    <!-- 工具栏：搜索 + 类型筛选 -->
    <div class="toolbar">
      <t-input v-model="searchKeyword" placeholder="搜索模型..." clearable size="small" class="searchInput">
        <template #prefix-icon><i-search theme="outline" size="14" /></template>
      </t-input>
    </div>

    <!-- 卡片列表 -->
    <div class="cardGrid">
      <div v-for="item in filteredModels" :key="item.key" class="skillCard" @click="startConfig(item)">
        <div class="skillCardHeader">
          <span class="skillName">{{ item.name }}</span>
          <t-tag v-if="item.model" theme="primary" variant="light" size="small">{{ item.model }}</t-tag>
          <t-tag v-else theme="warning" variant="light" size="small">未配置</t-tag>
        </div>
        <div class="skillCardBody">
          <t-avatar size="36px" v-if="getProviderLogo(item.manufacturer)" :image="getProviderLogo(item.manufacturer)!" />
          <t-avatar size="36px" v-else shape="round">
            <template #icon><i-robot theme="outline" size="20" fill="currentColor" /></template>
          </t-avatar>
          <span class="descText">{{ item.desc }}</span>
        </div>
        <div class="skillCardFooter">
          <div class="footerLeft">
            <t-tag theme="primary" variant="outline" size="small">语言模型</t-tag>
            <span v-if="item.manufacturer" class="mfr">{{ item.manufacturer }}</span>
          </div>
          <t-dropdown :options="MENU_OPTIONS" trigger="click" @click="(v: any) => onMenu(v, item)">
            <t-button variant="text" shape="circle" size="small" @click.stop>
              <template #icon><i-more theme="outline" size="16" /></template>
            </t-button>
          </t-dropdown>
        </div>
      </div>
      <div v-if="filteredModels.length === 0" class="emptyTip">暂无匹配的模型</div>
    </div>
  </div>

  <modelDataDom v-model:modelDataShow="modelDataShow" currentType="text" v-model:configingModel="configingModel" />
</template>

<script setup lang="ts">
import modelDataDom from "../model/modelData.vue";
import providersLogo from "@/utils/ai/providersLogo";

interface ModelType {
  id: number;
  model: string;
  name: string;
  key: string;
  manufacturer: string;
  desc: string;
}

const MENU_OPTIONS = [
  { content: "配置模型", value: "config" },
];

// ===== 假数据（仅语言模型） =====
const modelData = ref<ModelType[]>([
  { id: 1, model: "deepseek-v3", name: "分镜生成", key: "storyboardAgent", manufacturer: "deepSeek", desc: "根据剧本自动生成分镜描述，将文字转化为画面指令" },
  { id: 2, model: "qwen-max", name: "大纲剧本", key: "outlineScriptAgent", manufacturer: "qwen", desc: "从小说原文提取关键情节，生成结构化剧本大纲" },
  { id: 3, model: "glm-4-plus", name: "素材提示词", key: "assetsPrompt", manufacturer: "zhipu", desc: "根据角色和场景要素，生成精准的素材提示词" },
  { id: 4, model: "gpt-4o", name: "剧本生成", key: "generateScript", manufacturer: "openai", desc: "将大纲扩展为完整剧本脚本，包含对话和场景描写" },
  { id: 5, model: "gemini-2.0-flash", name: "视频提示词", key: "videoPrompt", manufacturer: "gemini", desc: "为视频生成提供画面描述和风格化提示词" },
]);

const modelDataShow = ref(false);
const configingModel = ref<ModelType>();
const searchKeyword = ref("");

const filteredModels = computed(() => {
  if (!searchKeyword.value) return modelData.value;
  const kw = searchKeyword.value.toLowerCase();
  return modelData.value.filter((item) =>
    item.name.toLowerCase().includes(kw) || item.key.toLowerCase().includes(kw)
  );
});

function getProviderLogo(manufacturer: string) {
  if (!manufacturer) return null;
  const key = Object.keys(providersLogo).find((k) => k.toLowerCase() === manufacturer.toLowerCase());
  return key ? providersLogo[key as keyof typeof providersLogo] : null;
}

function startConfig(item: ModelType) {
  configingModel.value = item;
  modelDataShow.value = true;
}

function onMenu(opt: { value: string }, model: ModelType) {
  if (opt.value === "config") startConfig(model);
}


</script>

<style lang="scss" scoped>
.aiConfog {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.toolbar {
  display: flex;
  align-items: center;
  gap: 12px;
  .searchInput {
    max-width: 240px;
  }
}

.cardGrid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px;
}

.skillCard {
  cursor: pointer;
  border: 1px solid var(--td-component-border);
  border-radius: 12px;
  padding: 16px 20px;
  display: flex;
  flex-direction: column;
  gap: 10px;
  transition: all 0.2s ease;
  background: var(--td-bg-color-container);
  &:hover {
    box-shadow: var(--td-shadow-2);
    border-color: var(--td-brand-color-light);
    transform: translateY(-1px);
  }
}

.skillCardHeader {
  display: flex;
  align-items: center;
  justify-content: space-between;
  .skillName {
    font-size: 15px;
    font-weight: 600;
  }
}

.skillCardBody {
  display: flex;
  align-items: flex-start;
  gap: 10px;
  .descText {
    flex: 1;
    font-size: 13px;
    color: var(--td-text-color-secondary);
    line-height: 1.5;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
  }
}

.skillCardFooter {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding-top: 8px;
  border-top: 1px solid var(--td-component-border);
  .footerLeft {
    display: flex;
    align-items: center;
    gap: 8px;
    .mfr {
      font-size: 12px;
      color: var(--td-text-color-placeholder);
    }
  }
}

.emptyTip {
  grid-column: 1 / -1;
  text-align: center;
  padding: 40px;
  color: var(--td-text-color-placeholder);
  font-size: 14px;
}
</style>
