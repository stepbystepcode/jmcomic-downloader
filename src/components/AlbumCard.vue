<script setup lang="ts">
import {Album, commands} from "../bindings.ts";
import {useNotification} from "naive-ui";
import {AlbumInfo} from "../types.ts";

defineProps<{
  albumInfo: AlbumInfo
}>();

const selectedAlbum = defineModel<Album | undefined>("selectedAlbum", {required: true});
const currentTabName = defineModel<"search" | "favorite" | "chapter">("currentTabName", {required: true});

const notification = useNotification();

async function onClickItem(aid: number) {
  const result = await commands.getAlbum(aid);
  if (result.status === "error") {
    notification.error({title: "获取漫画失败", description: result.error});
    return;
  }
  selectedAlbum.value = result.data;
  currentTabName.value = "chapter";
}

async function downloadAlbum(aid: number) {
  const result = await commands.downloadAlbum(aid);
  if (result.status === "error") {
    notification.error({title: "下载漫画失败", description: result.error});
    return;
  }
}

</script>

<template>
  <n-card content-style="padding: 0.25rem;"
          hoverable>
    <div class="flex">
      <img class="w-24 object-cover mr-4 cursor-pointer transition-transform duration-200 hover:scale-106"
           :src="`https://cdn-msp3.18comic.vip/media/albums/${albumInfo.id}_3x4.jpg`"
           alt=""
           referrerpolicy="no-referrer"
           @click="onClickItem(parseInt(albumInfo.id))"/>
      <div class="flex flex-col w-full justify-between">
        <div class="flex flex-col">
          <!--    TODO:调整标题的最大行数，以确保漫画卡片大小一致       -->
          <span class="font-bold text-xl line-clamp-3 cursor-pointer transition-colors duration-200 hover:text-blue-5"
                @click="onClickItem(parseInt(albumInfo.id))">
          {{ albumInfo.name }}
          </span>
          <span class="text-red">作者：{{ albumInfo.author }}</span>
          <span class="text-gray">分类：{{ albumInfo.category.title }} {{ albumInfo.category_sub.title }}</span>
        </div>
        <n-button size="tiny" class="ml-auto" @click="downloadAlbum(parseInt(albumInfo.id))">一键下载所有章节</n-button>
      </div>
    </div>
  </n-card>
</template>