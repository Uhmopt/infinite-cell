<template>
  <div class="home-container">
    <div class="home">
      <CellGrid
        :data="data.sections"
        :cellFormat="{
          rootCellBackground:
            data['sections format']['reference background color hex'],
          rootCellTextColor:
            data['sections format']['reference text color hex'],
          childCellBackground:
            data['sections format'][
              'in-content reference background color hex'
            ],
          childCellTextColor:
            data['sections format']['in-content reference text color hex'],
        }"
        :isRoot="true"
      />
    </div>
  </div>
</template>

<style>
.home-container {
  height: calc(100vh - 20px);
}

.home {
  overflow: auto;
  height: 100%;
}
</style>
<script>
// @ is an alias to /src
import CellGrid from "@/components/CellGrid.vue";
import data from "@/data/cellData.json";

export default {
  name: "Home",
  components: {
    CellGrid,
  },
  data: function () {
    return {
      data: data ?? {},
    };
  },
};
</script>
