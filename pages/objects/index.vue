<template>
  <section>
    <h2>Список недвижимости</h2>
    <el-pagination background layout="prev, pager, next" :total="1000">
    </el-pagination>
    <ObjectCard
      v-for="object of objects"
      :key="object.id"
      v-bind:object="object"
    />
  </section>
</template>

<script>
const objectsData = () =>
  import("~/assets/data.json").then((res) => res.default || res);

import ObjectCard from "@/components/ObjectCard";

export default {
  components: {
    ObjectCard,
  },
  async asyncData() {
    const objects = await objectsData();

    return { objects };
  },
  data: () => ({
    objects: [],
  }),
};
</script>