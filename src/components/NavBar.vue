<template>
  <div>
    <nav class="navbar navbar-dark sticky-top bg-dark flex-md-nowrap p-0">
      <a class="navbar-brand col-sm-3 col-md-2 mr-0" href="#">COMB JELLY</a>
      <!-- <input
        class="form-control form-control-dark w-100"
        type="text"
        placeholder="Search"
        aria-label="Search"
      /> -->
      <ul class="navbar-nav px-3">
        <li class="nav-item text-nowrap">
          <a class="nav-link" href="#" @click.prevent="signout">登出</a>
        </li>
      </ul>
    </nav>
  </div>
</template>

<script>
export default {
  methods: {
    signout() {
      const api = `${process.env.VUE_APP_API}logout`;
      const vm = this;
      this.$http.post(api).then(res => {
        console.log(res.data);
        // 若成功登出，跳轉到首頁
        if (res.data.success) {
          vm.$router.push("/login");
        }
      });
    }
  }
};
</script>
