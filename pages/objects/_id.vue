<template>
  <section>
    <el-page-header 
      @back="goBack" 
      title="Назад" 
      :content="objectTitle()">
    </el-page-header>

    Это объект {{$route.params}}
    Address: {{object.address}}
  </section>
</template>

<script>
const objectsData = () => import('~/assets/data.json').then(res => res.default || res);

export default {
  validate({params}) {
    return /^\d+$/.test(params.id)
  },
  async asyncData({params: {id}}) {
    const objects = await objectsData();
    const object = objects.find(obj => {
      return obj.id === parseInt(id)
    })

    console.log(object);

    return {object}
  },
  data() {
    return {
      objectTitle: function () {
        console.warn(this.$route.params);
        return `Объект ${this.$route.params.id}`;
      }
    }
  },
  methods: {
    goBack() {
      this.$router.push(`/objects`);
      console.log("go back");
    }
  },
};
</script>