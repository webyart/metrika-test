<template>
  <div>
    <el-row>
      <el-col class="header-box">
        <div class="block">
          <a href="/">
            <img :src="require(`~/assets/img/logo.png`)" width="170px" alt="Метрика недвижимость" />
          </a>
        </div>
      </el-col>
      <el-col class="login-box">
        <el-dropdown trigger="click" v-on:command="handleNavDropdownCommand">
          <span class="el-dropdown-link">Иван Иванов <i class="el-icon-caret-bottom el-icon--right"></i></span>
          <el-dropdown-menu slot="dropdown" class="navbar__dropdown-menu">
            <el-dropdown-item><router-link to="/user/account">Счет</router-link></el-dropdown-item>
            <el-dropdown-item><router-link to="/user/profile">Профиль</router-link></el-dropdown-item>
            <el-dropdown-item command="logout" divided>Выйти</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  data() {
    return {
    }
  },
  methods: {
    handleNavDropdownCommand: function (command) {
      if (command == "logout") {
        this.$confirm("Выйти из системы?", "Выход", {
          confirmButtonText: "Да",
          cancelButtonText: "Нет",
          type: "warning",
        }).then(
          function () {
            this.$message("Вы вышли из системы");
          }.bind(this)
        );
      }
    },
  },
};
</script>

<style>
  .el-row {
    display: flex;
  }
  .login-box {
    text-align: right;
  }
</style>