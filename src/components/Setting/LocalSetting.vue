<!-- 本地设置 -->
<template>
  <div class="setting-type">
    <div class="set-list">
      <n-h3 prefix="bar"> 本地歌曲 </n-h3>
      <n-card class="set-item">
        <div class="label">
          <n-text class="name">显示本地歌曲封面</n-text>
          <n-text class="tip" :depth="3">当数量过多时请勿开启，会严重影响性能</n-text>
        </div>
        <n-switch class="set" v-model:value="settingStore.showLocalCover" :round="false" />
      </n-card>
      <n-card class="set-item">
        <div class="label">
          <n-text class="name">显示本地默认歌曲目录</n-text>
        </div>
        <n-switch class="set" v-model:value="settingStore.showDefaultLocalPath" :round="false" />
      </n-card>
      <n-card class="set-item" id="local-list-choose" content-style="flex-direction: column">
        <n-flex justify="space-between">
          <div class="label">
            <n-text class="name">本地歌曲目录</n-text>
            <n-text class="tip" :depth="3">可在此增删本地歌曲目录，歌曲增删实时同步</n-text>
          </div>
          <n-button strong secondary @click="changeLocalPath()">
            <template #icon>
              <SvgIcon name="Folder" />
            </template>
            更改
          </n-button>
        </n-flex>
        <n-collapse-transition :show="settingStore.localFilesPath.length > 0">
          <n-card
            v-for="(item, index) in settingStore.localFilesPath"
            :key="index"
            class="set-item"
          >
            <div class="label">
              <n-text class="name">{{ item }}</n-text>
            </div>
            <n-button strong secondary @click="changeLocalPath(index)">
              <template #icon>
                <SvgIcon name="Delete" />
              </template>
            </n-button>
          </n-card>
        </n-collapse-transition>
      </n-card>
    </div>
    <div class="set-list">
      <n-h3 prefix="bar"> 下载配置 </n-h3>
      <n-card class="set-item">
        <div class="label">
          <n-text class="name">默认下载目录</n-text>
          <n-text class="tip" :depth="3">
            {{ settingStore.downloadPath || "若不设置则无法进行下载" }}
          </n-text>
        </div>
        <n-flex>
          <Transition name="fade" mode="out-in">
            <n-button
              v-if="settingStore.downloadPath"
              type="primary"
              strong
              secondary
              @click="settingStore.downloadPath = ''"
            >
              清除选择
            </n-button>
          </Transition>
          <n-button strong secondary @click="choosePath">
            <template #icon>
              <SvgIcon name="Folder" />
            </template>
            更改
          </n-button>
        </n-flex>
      </n-card>
      <n-card class="set-item">
        <div class="label">
          <n-text class="name">下载歌曲元信息</n-text>
          <n-text class="tip" :depth="3">为当前下载歌曲附加封面及歌词等元信息</n-text>
        </div>
        <n-switch class="set" v-model:value="settingStore.downloadMeta" :round="false" />
      </n-card>
      <n-card class="set-item">
        <div class="label">
          <n-text class="name">同时下载封面</n-text>
          <n-text class="tip" :depth="3">下载歌曲时同时下载封面</n-text>
        </div>
        <n-switch
          v-model:value="settingStore.downloadCover"
          :disabled="!settingStore.downloadMeta"
          :round="false"
          class="set"
        />
      </n-card>
      <n-card class="set-item">
        <div class="label">
          <n-text class="name">同时下载歌词</n-text>
          <n-text class="tip" :depth="3">下载歌曲时同时下载歌词</n-text>
        </div>
        <n-switch
          v-model:value="settingStore.downloadLyric"
          :disabled="!settingStore.downloadMeta"
          :round="false"
          class="set"
        />
      </n-card>
      <n-card class="set-item">
        <div class="label">
          <n-text class="name">保留元信息文件</n-text>
          <n-text class="tip" :depth="3">是否在下载目录中保留元信息文件</n-text>
        </div>
        <n-switch
          v-model:value="settingStore.saveMetaFile"
          :disabled="!settingStore.downloadMeta"
          :round="false"
          class="set"
        />
      </n-card>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useSettingStore } from "@/stores";
import { changeLocalPath } from "@/utils/helper";

const settingStore = useSettingStore();

// 选择下载路径
const choosePath = async () => {
  const path = await window.electron.ipcRenderer.invoke("choose-path");
  if (path) settingStore.downloadPath = path;
};
</script>

<style lang="scss" scoped>
#local-list-choose {
  .n-flex {
    width: 100%;
  }
  .n-collapse-transition {
    margin-top: 12px;
  }
}
</style>
