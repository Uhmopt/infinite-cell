<template>
  <tr
    :style="`color: ${textColor}; position: relative; cursor: pointer;`"
    @click="handleClick"
  >
    <td style="vertical-align: top; width: 2rem">
      <div
        :style="`
        padding: 0.5rem;
        background: ${backGround};
        font-size: 1rem; 
        font-weight: bold; ${
          Boolean(openChild)
            ? 'border: 1px solid black'
            : `border: 1px solid ${backGround};`
        }`"
      >
        {{ cellData.reference }}
      </div>
    </td>

    <td :style="`vertical-align: top; padding-bottom: 1rem; width: 400px;`">
      <div style="width: 100%; display: flex; align-items: strech">
        <div
          :style="`display: flex; flex-grow: 1; ${
            Boolean(openChild)
              ? `border: 1px solid black`
              : `border: 1px solid ${backGround};`
          }`"
        >
          <div
            :style="`padding: 1rem; background: ${backGround}; font-size: 1rem; flex-grow: 1; width: 400px;`"
          >
            {{ cellData.content }}
          </div>
        </div>
      </div>
    </td>
    <td
      v-if="(Boolean(openChild) || !Boolean(isRoot)) && Boolean(targetArray[0])"
      style="vertical-align: top"
    >
      <div style="position: absolute; padding-left: 1rem">
        <keep-alive>
          <CellGrid
            :data="remappedData"
            :cellFormat="cellFormat"
            :toShow="targetArray[0]"
          />
        </keep-alive>
      </div>
    </td>
  </tr>
</template>

<script>
export default {
  name: "CellCard",
  components: {
    CellGrid() {
      return import("@/components/CellGrid.vue");
    },
  },
  props: {
    data: Array,
    cellData: Object,
    cellFormat: {
      type: Object,
      default() {
        return {
          rootCellBackground: "#90EE90",
          rootCellTextColor: "#000",
          childCellBackground: "#ADD8E6",
          childCellTextColor: "#000",
        };
      },
    },
    isRoot: Boolean,
    onClick: {
      type: Function,
      default() {
        return () => {};
      },
    },
    openChild: Boolean,
  },
  data() {
    return {
      targetArray: this.cellData?.["in-content reference"] ?? [],
      remappedData: [
        ...((this.data ?? []).reduce((ret, cur) => {
          const ref = cur?.reference ?? "";
          const index = (
            (this.cellData?.["in-content reference"] ?? [])?.[0] ?? []
          ).findIndex((item) => String(item) === String(ref));
          if (index >= 0) {
            ret.push({
              ...(cur ?? {}),
              "in-content reference": [
                (this.cellData?.["in-content reference"] ?? [])?.[index + 1] ??
                  [],
              ],
            });
          } else {
            ret.push(cur);
          }
          return ret;
        }, []) ?? []),
      ],
      backGround:
        (this.isRoot
          ? this.cellFormat?.rootCellBackground
          : this.cellFormat?.childCellBackground) ?? "",
      textColor:
        (this.isRoot
          ? this.cellFormat?.rootCellTextColor
          : this.cellFormat?.childCellTextColor) ?? "",
    };
  },
  methods: {
    handleClick() {
      this.onClick();
    },
  },
};
</script>

<style></style>
