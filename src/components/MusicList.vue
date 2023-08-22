<template>
  <a-list
    item-layout="horizontal"
    :pagination="pagination"
    :grid="{ gutter: 16, xs: 1, sm: 2, md: 4, lg: 4, xl: 6, xxl: 3 }"
    :data-source="props.musicList"
  >
    <template #renderItem="{ item }">
      <a-list-item>
        <a :href="item.mp3Url">
          <a-card>
            <template #cover>
              <img :src="item.picUrl" referrerpolicy="no-referrer" />
            </template>
            <a :href="item.mp3Url">
              <a-card-meta :title="'歌名:' + item.name" />
              <a-card-meta :title="'歌手:' + item.author" />
            </a>
          </a-card>
        </a>
      </a-list-item>
    </template>
  </a-list>
</template>

<script setup lang="ts">
import { withDefaults, defineProps, onMounted } from "vue";
import { useRoute, useRouter } from "vue-router";

const route = useRoute();
const router = useRouter();

const pagination = {
  onChange: (page: number) => {
    console.log(page);
    pagination.current = page;
    const query = { ...route.query };
    // 修改query参数中的某一项
    query.pageNum = page.toString();
    // 将修改后的query参数更新到URL中
    router.replace({ query });
  },
  pageSize: 12,
  hideOnSinglePage: false,
  //current: ref(parseInt(route.query.pageNum)),
};

onMounted(() => {
  if (route.query.pageNum != null) {
    pagination.onChange(parseInt(route.query.pageNum));
    //pagination.current = parseInt(route.query.pageNum);
  }
});

interface Props {
  musicList: any[];
}

const props = withDefaults(defineProps<Props>(), {
  musicList: () => [],
});
</script>

<style scoped></style>
