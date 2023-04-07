<template>
  <q-page padding>
    <div class="q-mt-sm">
      <div>
        <p class="text-h5 text-weight-bold q-mb-none">Albums</p>
        <p class="">Shown below are all the albums of the users</p>
      </div>
      <div class="row q-mt-md">
        <UserAlbums
          v-for="(album, i) in mergedData"
          :key="i"
          :title="album.title"
          :user="album.user.name"
          :index="i"
          :img="album.photos[0].thumbnailUrl"
        />
      </div>
    </div>
  </q-page>
</template>

<script setup>
import { onMounted, ref } from "vue";
import { useExampleStore } from "../stores/example-store";
import UserAlbums from "../components/UserAlbums.vue";

const albums = ref();
const photos = ref();
const mergedData = ref();
const jsonPlaceholder = useExampleStore();

onMounted(async () => {
  await jsonPlaceholder.fetchUsers();

  await jsonPlaceholder.fetchAlbums();
  albums.value = jsonPlaceholder.albums;

  await jsonPlaceholder.fetchPhotos();
  photos.value = jsonPlaceholder.photos;

  mergedData.value = jsonPlaceholder.getData;
});
</script>
