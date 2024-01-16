<template>
  <q-layout class="layout" view="lHh Lpr lFf">
    <q-header>
      <q-toolbar style="align-items: center">
        <div
          class="large-screen-only"
          style="display: flex; align-items: center"
        >
          <div
            class="large-screen-only text-h6 text-weight-medium q-ml-md q-my-auto"
          >
            Bodyweight Tracker App by Vino
          </div>
          <a
            href="https://www.github.com/Noxxider"
            target="_blank"
            class="no-decoration"
          >
            <q-btn
              class="large-screen-only q-px-xl q-ml-xl"
              no-caps
              text-color="white"
              label="GitHub"
              style="padding: 8px 12px"
              unelevated
            />
          </a>

          <a href="mailto:ravino.juwono@gmail.com" class="no-decoration">
            <q-btn
              class="large-screen-only q-px-xl q-ml-xl"
              no-caps
              text-color="white"
              label="Email"
              style="padding: 8px 12px"
              unelevated
            />
          </a>
        </div>

        <div class="small-screen-only q-mx-auto full-width">
          <div class="flex justify-between text-h6">
            <div>Bodyweight Tracker App by Vino</div>

            <q-btn
              flat
              no-caps
              dense
              icon="menu"
              style="color: #262626"
              aria-label="Menu"
              @click="toggleLeftDrawer"
              class="small-screen-only q-my-auto"
            />
          </div>
        </div>
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" :width="200" bordered behavior="mobile">
      <q-list>
        <div class="text-h6 q-pa-md" header>Bodyweight Tracker App by Vino</div>
        <q-separator />
        <EssentialLink
          class="q-mt-sm"
          v-for="link in essentialLinks"
          :key="link.title"
          v-bind="link"
        />
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from "vue";
import EssentialLink from "components/EssentialLink.vue";

const linksList = [
  {
    title: "App",
    icon: "person",
    link: "/",
  },
  {
    title: "Email",
    icon: "mail",
    link: "mailto:ravino.juwono@gmail.com",
  },
  {
    title: "GitHub",
    icon: "widgets",
    link: "https://www.github.com/Noxxider",
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

<style scoped>
.no-decoration {
  text-decoration: none; /* Removes underline */
  color: inherit; /* Keeps the text color consistent with your button */
}
</style>