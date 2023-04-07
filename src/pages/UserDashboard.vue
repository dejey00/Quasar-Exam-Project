<template>
  <q-page padding>
    <div class="q-mt-sm">
      <div>
        <p class="text-h5 text-weight-bold q-mb-none">Dashboard</p>
      </div>
      <div class="row q-mt-md">
        <div class="col-xs-12 col-sm-6 col-md-4 q-pr-sm q-pt-sm">
          <q-card class="q-pa-lg rounded-sm shadow-sm">
            <div class="row items-start gap-md">
              <div class="bg-red-6 q-pa-md rounded-md q-mr-md">
                <q-icon name="people" size="32px" class="" color="white" />
              </div>
              <div>
                <p class="text-h2 text-weight-medium">
                  {{ jsonPlaceholder.users.length }}
                </p>
                <p class="q-mb-sm q-nmt-sm">Total Users</p>
              </div>
            </div>
          </q-card>
        </div>
        <div class="col-xs-12 col-sm-6 col-md-4 q-pr-sm q-pt-sm">
          <q-card class="q-pa-lg rounded-sm shadow-sm">
            <div class="row items-start gap-md">
              <div class="bg-blue-5 q-pa-md rounded-md q-mr-md">
                <q-icon name="collections" size="32px" class="" color="white" />
              </div>
              <div>
                <p class="text-h2 text-weight-medium">
                  {{ jsonPlaceholder.albums.length ?? "00" }}
                </p>
                <p class="q-mb-sm q-nmt-sm">Total Albums</p>
              </div>
            </div>
          </q-card>
        </div>
        <div class="col-xs-12 col-sm-6 col-md-4 q-pr-sm q-pt-sm">
          <q-card class="q-pa-lg rounded-sm shadow-sm">
            <div class="row items-start gap-md">
              <div class="bg-yellow-8 q-pa-md rounded-md q-mr-md">
                <q-icon
                  name="crop_original"
                  size="32px"
                  class=""
                  color="white"
                />
              </div>
              <div>
                <p class="text-h2 text-weight-medium">
                  {{ jsonPlaceholder.photos.length ?? "00" }}
                </p>
                <p class="q-mb-sm q-nmt-sm">Total Photos</p>
              </div>
            </div>
          </q-card>
        </div>
      </div>
    </div>
    <div class="q-mt-xl" v-if="!isAlbumTableShown">
      <div class="row justify-between">
        <div>
          <p class="text-h5 text-weight-bold q-mb-none">Users Table</p>
          <p class="">Shown on the table are the users data</p>
        </div>
        <div>
          <q-input debounce="300" v-model="search" label="Search">
            <template v-slot:append>
              <q-icon name="search" />
            </template>
          </q-input>
        </div>
      </div>
      <div class="q-mt-lg">
        <q-table
          :loading="isLoading"
          :rows="rows"
          :columns="userColumns"
          :filter="search"
          row-key="name"
          flat
        >
          <template #body-cell-name="props">
            <q-td key="name" :props="props">
              <div class="row no-wrap items-center">
                <q-avatar size="64px">
                  <q-icon name="person"></q-icon>
                </q-avatar>
                <div>
                  <p>{{ props.row.name }}</p>
                  <p class="text-gray-400 text-lowercase">
                    {{ props.row.email }}
                  </p>
                </div>
              </div>
            </q-td>
          </template>
          <template #body-cell-actions="props">
            <q-td key="actions" :props="props">
              <q-btn
                @click="setAlbumTableData(props.row.albums, props.row.name)"
                flat
                color="primary"
                size="12px"
                >View Albums</q-btn
              >
            </q-td>
          </template>
          <template v-slot:header="props">
            <q-tr :props="props">
              <q-th
                v-for="col in props.cols"
                :key="col.name"
                :props="props"
                class="text-uppercase"
              >
                {{ col.label }}
              </q-th>
            </q-tr>
          </template>
        </q-table>
      </div>
    </div>
    <div class="q-mt-xl" v-else>
      <div class="row justify-between">
        <div>
          <p class="text-h5 text-weight-bold q-mb-none">Albums</p>
          <p class="">
            Shown on the table are the albums of
            <span class="text-primary text-weight-bold">{{ albumUser }}</span>
          </p>
        </div>
        <div>
          <q-input debounce="300" v-model="albumSearch" label="Search">
            <template v-slot:append>
              <q-icon name="search" />
            </template>
          </q-input>
        </div>
      </div>
      <q-btn
        class="q-mt-md"
        unelevated
        size="12px"
        @click="toggleAlbumTable"
        color="primary"
        >Return</q-btn
      >
      <div class="q-mt-lg">
        <q-table
          :rows="albumRows"
          :columns="albumColumns"
          :filter="albumSearch"
          row-key="id"
          flat
        >
          <template #body-cell-album="props">
            <q-td key="album" :props="props">
              <div class="row no-wrap items-center">
                <div>
                  <p class="text-gray-400 text-capitalize">
                    {{ props.row.title }}
                  </p>
                </div>
              </div>
            </q-td>
          </template>
          <template v-slot:header="props">
            <q-tr :props="props">
              <q-th
                v-for="col in props.cols"
                :key="col.name"
                :props="props"
                class="text-uppercase"
              >
                {{ col.label }}
              </q-th>
            </q-tr>
          </template>
          <template #body-cell-actions="props">
            <q-td key="actions" :props="props">
              <q-btn
                :to="`/album/${props.row.id}`"
                flat
                color="primary"
                size="12px"
                >View Photos</q-btn
              >
            </q-td>
          </template>
        </q-table>
      </div>
    </div>
  </q-page>
</template>
<script setup>
import { ref, onMounted } from "vue";
import { useExampleStore } from "../stores/example-store";
import { tableColumns } from "../composable/tables";

const search = ref("");
const albumSearch = ref("");

const isAlbumTableShown = ref(false);
const albumUser = ref("");
const isLoading = ref(false);
const jsonPlaceholder = useExampleStore();
let rows = ref();
let albumRows = ref();

const { userColumns, albumColumns } = tableColumns();

const setAlbumTableData = (album, user) => {
  toggleAlbumTable();
  albumRows.value = album;
  albumUser.value = user;
};

const toggleAlbumTable = () =>
  (isAlbumTableShown.value = !isAlbumTableShown.value);

onMounted(async () => {
  isLoading.value = true;
  await jsonPlaceholder.fetchUsers();
  await jsonPlaceholder.fetchAlbums();
  await jsonPlaceholder.fetchPhotos();

  rows.value = jsonPlaceholder.getUsers;
  isLoading.value = false;
});
</script>
