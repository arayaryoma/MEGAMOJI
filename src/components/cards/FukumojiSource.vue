<script lang="ts">
import { defineComponent } from "vue";
import { NCard, NTabs, NTabPane } from "naive-ui";
import PartSelect from "../formblocks/PartSelect.vue";
import { EMOJI_SIZE } from "../../constants/emoji";
import { mergeImages, urlToImg } from "../../utils/canvas";
import * as parts from "../../constants/parts";
import empty from "../../parts/void.svg";

export default defineComponent({
  components: {
    PartSelect, NCard, NTabs, NTabPane,
  },
  props: {
    show: { type: Boolean, required: true },
  },
  emits: [
    "render",
  ],
  data() {
    return {
      parts,
      tab: "ベース",
      conf: {
        base: empty,
        textures: empty,
        eyes: empty,
        mouths: empty,
        others: empty,
      },
    };
  },
  watch: {
    conf: {
      handler(): void {
        this.render();
      },
      deep: true,
    },
  },
  methods: {
    render(): void {
      mergeImages(EMOJI_SIZE, EMOJI_SIZE, [
        this.conf.base,
        this.conf.textures,
        this.conf.mouths,
        this.conf.eyes,
        this.conf.others,
      ], (blobUrl) => {
        urlToImg(blobUrl, (img) => this.$emit("render", img));
      });
    },
  },
});
</script>

<template>
  <NCard v-if="show">
    <NTabs v-model:value="tab" type="card">
      <NTabPane
          v-for="category in parts.categories"
          :key="category.label"
          :name="category.label">
        <PartSelect
            :key="category.value"
            v-model="conf[category.value]"
            :parts="parts[category.value]" />
      </NTabPane>
    </NTabs>
  </NCard>
</template>
