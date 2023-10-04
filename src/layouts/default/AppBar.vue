<template>
  <v-app-bar color="primary" :elevation="2">
    <v-app-bar-nav-icon v-on:click="drawer = !drawer" />

    <v-app-bar-title>
      <v-icon icon="mdi-code-braces" />
      Vue Sandbox
    </v-app-bar-title>

    <template v-slot:append>
      <v-btn
        v-if="dark"
        icon="mdi-lightbulb-on"
        title="Toggle theme"
        v-on:click="toggleTheme"
      ></v-btn>
      <v-btn
        v-else
        icon="mdi-lightbulb-on-outline"
        title="Toggle theme"
        v-on:click="toggleTheme"
      ></v-btn>
    </template>
  </v-app-bar>

  <v-navigation-drawer v-model="drawer" location="left" temporary>
    <v-list nav>
      <v-list-item
        v-for="item in items"
        :key="item.title"
        router
        :to="item.to"
        :prepend-icon="item.icon"
        :title="item.title"
      >
      </v-list-item>
    </v-list>
  </v-navigation-drawer>
</template>

<script lang="ts" setup>
import { ref } from "vue";
import { useTheme } from "vuetify";

const theme = useTheme();

const drawer = ref(false);
const dark = ref(theme.global.current.value.dark);

const items = [
  {
    title: "Home",
    icon: "mdi-home",
    to: "/",
  },
  {
    title: "Calculator",
    icon: "mdi-calculator",
    to: "/calculator",
  },
];

function toggleTheme() {
  theme.global.name.value = theme.global.current.value.dark ? "light" : "dark";
}
</script>
