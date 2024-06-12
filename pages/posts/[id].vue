<script setup lang="ts">
import {API_BASE_URL} from "~/utils/constants";

interface Post {
  id: number;
  user: User;
  category: Category;
  title: string;
  published_at: string;
  content_raw: string;
}
interface User {
  name: string;
  email: string;
}

interface Category {
  title: string;
  description: string;
}
const route = useRoute();
const postId = ref<number | null>(null);
const post = ref<Post| null>(null);

const getPost = async (id: number) => {
  try {
    const response = await $fetch<Post>(API_BASE_URL + `/api/blog/posts/${id}`);
    post.value = response;
  } catch (error) {
    console.error('Error fetching post:', error);
  }
};

onMounted(() => {
  const id = parseInt(route.params.id as string, 10);
  if (!isNaN(id)) {
    postId.value = id;
    getPost(id);
  } else {
    console.error('Invalid post ID');
  }
});
</script>

<template>
  <div class="flex justify-center mx-5 gap-5">


  <UCard class="col-auto">
    <template #header>
      <h3 class="text-center"><strong>Detailed info about post</strong> </h3>
    </template>
    <template class="row flex ">
      <div class="col-auto flex flex-col gap-5">
        <p><strong>Id</strong>: {{post?.id}}</p>
        <p><strong>Title:</strong> {{post?.title}}</p>
        <p><strong>Category:</strong> {{post?.category?.title}}</p>
        <p><strong>Published at:</strong> {{post?.published_at}}</p>
        <p><strong>Content:</strong> {{post?.content_raw}}</p>
      </div>
    </template>

  </UCard>
    <div class="row flex-col flex justify-between">


  <UCard class="col-auto">
    <template #header>
        <h3 class="text-center"><strong>User Info</strong></h3>
    </template>
      <template class="row flex justify-center">
        <div class="col-auto">
          <p><strong>Name:</strong> {{post?.user?.name}}</p>
          <p><strong>Email:</strong> {{post?.user?.email}}</p>
        </div>
      </template>
  </UCard>
  <UCard class="col-auto">
    <template #header>
      <h4 class="text-center"><strong>Category Info</strong></h4>
    </template>
    <template class="row flex justify-center">
      <div class="col-auto">
        <p><strong>Title:</strong> {{post?.category?.title}}</p>
        <p><strong>Description:</strong> {{post?.category?.description}}</p>
      </div>
    </template>
  </UCard>
    </div>
  </div>
</template>

<style scoped>

</style>