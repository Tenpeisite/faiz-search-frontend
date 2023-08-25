<template>
  <div class="index-page">
    <a-input-search
      v-model:value="searchText"
      placeholder="请输入搜索关键词"
      enter-button="搜索"
      size="large"
      @search="onSearch"
    />
    <MyDivider />
    <a-tabs
      v-model:activeKey="activeKey"
      @change="onTabChange"
      style="lighting-color: #eea6b7"
    >
      <a-tab-pane key="post" tab="文章">
        <PostList :post-list="postList" />
      </a-tab-pane>
      <a-tab-pane key="video" tab="视频">
        <VideoList :video-list="videoList"></VideoList>
      </a-tab-pane>
      <a-tab-pane key="music" tab="音乐">
        <MusicList :music-list="musicList"></MusicList>
      </a-tab-pane>
      <a-tab-pane key="picture" tab="图片">
        <PictureList :picture-list="pictureList" />
      </a-tab-pane>
      <a-tab-pane key="user" tab="用户">
        <UserList :user-list="userList" />
      </a-tab-pane>
    </a-tabs>
  </div>
</template>

<script setup lang="ts">
import { ref, watchEffect } from "vue";
import PostList from "@/components/PostList.vue";
import PictureList from "@/components/PictureList.vue";
import UserList from "@/components/UserList.vue";
import VideoList from "@/components/VideoList.vue";
import MusicList from "@/components/MusicList.vue";
import MyDivider from "@/components/MyDivider.vue";
import { useRoute, useRouter } from "vue-router";
import myAxios from "@/plugins/myAxios";
import { message } from "ant-design-vue";
import { onMounted } from "vue";

const postList = ref([]);

const userList = ref([]);

const pictureList = ref([]);

const videoList = ref([]);

const musicList = ref([]);

const route = useRoute();
const router = useRouter();
const activeKey = route.params.category;

const initSearchParams = {
  type: activeKey,
  text: "",
  pageSize: 50,
  pageNum: 1,
};

const searchText = ref(route.query.text || "");

/**
 * 加载单类数据
 * @param params
 */
const loadData = (params: any) => {
  const { type = "post" } = params;
  if (!type) {
    message.error("类别为空");
    return;
  }
  const query = {
    ...params,
    searchText: params.text,
  };
  myAxios.post("search/all", query).then((res: any) => {
    if (type === "post") {
      postList.value = res.dataList;
    } else if (type === "user") {
      userList.value = res.dataList;
    } else if (type === "picture") {
      pictureList.value = res.dataList;
    } else if (type === "video") {
      videoList.value = res.dataList;
    } else if (type === "music") {
      musicList.value = res.dataList;
    }
  });
};

const searchParams = ref(initSearchParams);

// watchEffect(() => {
//   searchParams.value = {
//     ...initSearchParams,
//     text: route.query.text,
//     type: route.params.category,
//   } as any;
//   loadData(searchParams.value);
// });
watchEffect(() => {
  // 判断是否是首次加载或者刷新页面
  const isFirstLoad = !searchParams.value.text && !searchParams.value.type;
  if (isFirstLoad) {
    searchParams.value = {
      ...initSearchParams,
      text: route.query.text,
      type: "post",
    } as any;
  } else {
    searchParams.value = {
      ...initSearchParams,
      text: route.query.text,
      type: route.params.category || "post",
    } as any;
  }
  loadData(searchParams.value);
});

const onSearch = (value: string) => {
  router.push({
    query: {
      ...searchParams.value,
      text: value,
    },
  });
};

const onTabChange = (key: string) => {
  router.push({
    path: `/${key}`,
    // query: searchParams.value,
    query: {
      ...initSearchParams,
      text: route.query.text,
      type: `${key}`,
    },
  });
};
</script>

<style>
:where(.css-ykw4qh).ant-float-btn
  .ant-float-btn-body
  .ant-float-btn-content
  .ant-float-btn-icon {
  text-align: center;
  margin: auto;
  width: 25px;
  font-size: 18px;
  line-height: 1;
}

:where(.css-ykw4qh).ant-float-btn-default
  .ant-float-btn-body
  .ant-float-btn-content
  .ant-float-btn-description {
  display: flex;
  align-items: center;
  line-height: 16px;
  color: #eea6b7;
  font-size: 12px;
}

#spin {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.container {
  text-align: justify;
}

/*pre {*/
/*  word-wrap: break-word;*/
/*  overflow-wrap: break-word;*/
/*}*/
.text {
  white-space: pre-wrap;
  word-break: break-word;
  line-height: 20px;
}

.display {
  width: 100%;
  height: auto;
}
</style>
