<template>
  <div id="app"
       class="container col-12">
    <!--
        <div id="nav">
            <router-link to="/">Home</router-link>
            |
            <router-link to="/login">Login</router-link>
            |
            <router-link to="/pannel">Pannel</router-link>
            |
            <router-link to="/test">Test</router-link>
            |
            <router-link to="/subject/1278">Content</router-link>
        </div>
        -->
    <div class="row d-flex justify-content-between p-1">
      <!--主面板-->
      <router-view />
      <!--子面板-->
      <router-view name="sub" />
    </div>
    <footer>
      <div class="card shadow">
        <div class="card-body p-0">
          &copy;<a target="_blank"
             href="https://github.com/mrthanlon/kase-be">Kase</a>
          {{year}}
        </div>
      </div>
    </footer>
  </div>
</template>
<script>
export default {
  name: 'app',
  data: function () {
    return {
      year: new Date().getFullYear()
    }
  },
  async created () {
  }
}
</script>
<style>
</style>
