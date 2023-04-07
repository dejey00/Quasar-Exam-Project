<template>
  <q-layout view="lHh Lpr lFf">
    <q-header class="">
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />

        <p class="q-ml-md text-h6">Welcome</p>
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" show-if-above bordered :width="250">
      <div class="row justify-center items-center q-mt-md">
        <div class="bg-blue-5 q-mt-lg q-pa-md rounded-md q-mr-md">
          <q-icon name="collections" size="20px" class="" color="white" />
        </div>
        <p class="text-h4 text-weight-bold q-mt-lg q-mb-none">GALLERY</p>
      </div>
      <br />
      <q-list>
        <q-item-label header> Main Menu</q-item-label>

        <EssentialLink
          v-for="link in essentialLinks"
          :key="link.title"
          v-bind="link"
          :exact="link.exact"
        />
      </q-list>
    </q-drawer>

    <q-page-container class q-mx-lg>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from "vue";
import EssentialLink from "components/EssentialLink.vue";

const linksList = [
  {
    title: "Dashboard",
    icon: "dashboard",
    link: "/",
    exact: true,
  },
  {
    title: "Albums",
    icon: "art_track",
    link: "/albums",
    exact: false,
  },
];

export default defineComponent({
  name: "MainLayout",

  components: {
    EssentialLink,
  },

  setup() {
    const leftDrawerOpen = ref(false);

    return {
      essentialLinks: linksList,
      leftDrawerOpen,
      toggleLeftDrawer() {
        leftDrawerOpen.value = !leftDrawerOpen.value;
      },
    };
  },
});
</script>
