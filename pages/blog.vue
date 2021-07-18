<template>
  <div class="container">
    <nav class="main-nav">
      <div class="logo">
        Blog Pain
      </div>
      <Burger></Burger>
    </nav>

    <div>
      <Logo />

      <Sidebar>
        <ul class="sidebar-panel-nav">
          <li><a href="/">Home</a></li>
          <li><a href="#about">About</a></li>
          <li><a href="#contact">Contact</a></li>
        </ul>
      </Sidebar>
    </div>
  </div>
</template>
<script>
import Burger from "../components/Burger.vue";
import Sidebar from "../components/Sidebar.vue";

export default {
  name: "index",
  components: {
    Burger,
    Sidebar
  },
  async asyncData({ $content, params }) {
    const pages = await $content("pages")
      .only(["title", "description", "slug"])
      .sortBy("createdAt", "asc")
      .fetch();

    return {
      pages
    };
  }
};
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
}

.burger {
  align-content: right;
  justify-content: right;
}
</style>
