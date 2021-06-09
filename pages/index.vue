<template>
  <div class="container">
    <Burger></Burger>
    <Sidebar>
      <ul class="sidebar-panel-nav">
        <li><a href="./myposts">Blog</a></li>
        <li><a href="#about">About</a></li>
        <li><a href="#contact">Contact</a></li>
      </ul>
    </Sidebar>
    <nav class="main-nav">
      <div class="logo">
        my.company
      </div>
    </nav>

    <div class="container2">
      <Logo />
      <h1 class="title">
        Hiii
      </h1>

      <div class="links">
        <a
          href="./myposts"
          target="_blank"
          rel="noopener noreferrer"
          class="button--green"
        >
          My Posts
        </a>
        <a
          href="https://github.com/nuxt/nuxt.js"
          target="_blank"
          rel="noopener noreferrer"
          class="button--grey"
        >
          GitHub
        </a>
      </div>
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
  }
};
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
}
.container2 {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
}
.logo {
  align-self: center;
  color: rgb(248, 7, 7);
  font-weight: bold;
  font-family: "Lato";
}

.main-nav {
  display: flex;
  justify-content: space-between;
  padding: 0.5rem 0.8rem;
}
.sidebar-panel-nav {
  list-style-type: none;
}

.sidebar-panel-nav li a {
  color: rgb(19, 180, 14);
  text-decoration: none;
  list-style-type: ;
  font-size: 1.5rem;
  display: block;
  padding-bottom: 0.5em;
  color: rgb(248, 7, 7);
  font-weight: bold;
  font-family: "Lato";
}

.title {
  font-family: "Quicksand", "Source Sans Pro", -apple-system, BlinkMacSystemFont,
    "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #26643b;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
