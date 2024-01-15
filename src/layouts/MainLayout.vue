<template>
  <q-layout class="layout" view="lHh Lpr lFf">
    <q-header>
      <q-toolbar style="align-items: center">
        <div class="small-screen-only q-mx-auto full-width">
          <div class="flex justify-between">
            <q-btn to="/" flat>
              <img
                src="~assets/header.svg"
                class="q-my-auto"
                style="height: 24px; padding: 0px"
              />
            </q-btn>

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

        <div
          class="large-screen-only"
          style="display: flex; align-items: center"
        >
          <q-btn to="/" class="large-screen-only q-ml-md" flat>
            <img
              src="~src\assets\header.svg"
              class="q-my-auto"
              style="height: 24px; padding: 0px"
            />
          </q-btn>
          <q-btn
            class="large-screen-only q-px-xl q-mx-xl"
            no-caps
            to="/"
            text-color="dark"
            label="Home"
            style="padding: 8px 12px"
            unelevated
          />
          <q-btn
            class="large-screen-only q-px-xl"
            no-caps
            to="/contact"
            text-color="dark"
            label="Contact"
            style="padding: 8px 12px"
            unelevated
          />
          <q-btn
            class="large-screen-only q-ml-xl"
            no-caps
            to="/resume"
            color="accent"
            label="Resume"
            style="padding: 8px 16px"
            unelevated
          />
        </div>
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" :width="200" bordered behavior="mobile">
      <q-list>
        <div class="text-h6 q-pa-md" header>Pages</div>
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
    title: "Docs",
    caption: "quasar.dev",
    icon: "school",
    link: "https://quasar.dev",
  },
  {
    title: "Github",
    caption: "github.com/quasarframework",
    icon: "code",
    link: "https://github.com/quasarframework",
  },
  {
    title: "Discord Chat Channel",
    caption: "chat.quasar.dev",
    icon: "chat",
    link: "https://chat.quasar.dev",
  },
  {
    title: "Forum",
    caption: "forum.quasar.dev",
    icon: "record_voice_over",
    link: "https://forum.quasar.dev",
  },
  {
    title: "Twitter",
    caption: "@quasarframework",
    icon: "rss_feed",
    link: "https://twitter.quasar.dev",
  },
  {
    title: "Facebook",
    caption: "@QuasarFramework",
    icon: "public",
    link: "https://facebook.quasar.dev",
  },
  {
    title: "Quasar Awesome",
    caption: "Community Quasar projects",
    icon: "favorite",
    link: "https://awesome.quasar.dev",
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
