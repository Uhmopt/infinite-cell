<template>
  <div>
    <table
      cellspacing="0"
      cellpadding="0"
      style="border-collapse: collapse; border: none"
    >
      <tbody>
        <CellCard
          v-for="(cell, cellIndex) in showData"
          :id="`cell_${cellIndex}`"
          :key="cellIndex"
          :data="data"
          :cellFormat="cellFormat"
          :cellData="cell"
          :isRoot="Boolean(isRoot)"
          :openChild="Boolean(selectedCell.reference === cell.reference)"
          :onClick="() => handleCardClick(cell, cellIndex)"
        />
      </tbody>
    </table>
  </div>
</template>

<script>
import CellCard from "@/components/CellCard.vue";

export default {
  name: "CellGrid",
  components: {
    CellCard,
  },
  props: {
    data: Array,
    cellFormat: {
      type: Object,
      default: () => ({
        rootCellBackground: "#90EE90",
        rootCellTextColor: "#000",
        childCellBackground: "#ADD8E6",
        childCellTextColor: "#000",
      }),
    },
    toShow: Array,
    isRoot: Boolean,
  },
  data() {
    return {
      showData: this.isRoot
        ? this.data ?? []
        : (this.data ?? []).filter(
            (item) =>
              (this.toShow ?? []).findIndex(
                (i) => String(i) === String(item?.reference)
              ) >= 0
          ),
      selectedCell: this.isRoot ? { ...(this.data?.[0] ?? {}), index: 0 } : {},
    };
  },
  methods: {
    getCellElement(cellIndex) {
      return this.$el.querySelector(`#cell_${cellIndex}`);
    },
    isInScroll(cellIndex) {
      const element = this.getCellElement(cellIndex);
      return window.innerHeight - 100 < element.getBoundingClientRect().top;
    },
    getPageDown(cellIndex, length) {
      let sumHeight = 0;
      for (let index = cellIndex; index < length; index++) {
        const element = this.getCellElement(index);
        if (element) {
          sumHeight += Number(element.clientHeight);
          if (sumHeight > window.innerHeight) {
            return index;
          }
        }
      }
      return length - 1;
    },
    getPageUp(cellIndex) {
      let sumHeight = 0;
      for (let index = cellIndex; index >= 0; index--) {
        const element = this.getCellElement(index);
        if (element) {
          sumHeight += Number(element.clientHeight);
          if (sumHeight > window.innerHeight) {
            return index;
          }
        }
      }
      return 0;
    },
    scrollToCell(cellIndex) {
      const element = this.getCellElement(cellIndex);
      if (element) {
        element.scrollIntoView({
          // behavior: "smooth",
          block: "nearest",
        });
      }
    },
    handleCardClick(cell, cellIndex) {
      if (!this.isRoot) {
        return;
      }
      this.selectedCell = { ...(cell ?? {}), index: cellIndex };
      this.scrollToCell(cellIndex);
      return;
    },
    handleKeyDown(e) {
      const length = Number(this.data?.length ?? 0);
      let index = this.selectedCell?.index ?? -1;
      if (e?.key === "ArrowDown") {
        e.preventDefault();
        index = index + 1;
      } else if (e?.key === "ArrowUp") {
        e.preventDefault();
        index = index - 1;
      } else if (e?.key === "PageDown") {
        e.preventDefault();
        index = this.getPageDown(index, length);
      } else if (e?.key === "PageUp") {
        e.preventDefault();
        index = this.getPageUp(index);
      } else if (e?.key === "Home") {
        e.preventDefault();
        index = 0;
      } else if (e?.key === "End") {
        e.preventDefault();
        index = length - 1;
      } else {
        return;
      }
      index = (index + length) % length;
      this.handleCardClick(this.data?.[index], index);
    },
  },
  created() {
    if (this.isRoot) {
      window.document.addEventListener("keydown", this.handleKeyDown);
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
