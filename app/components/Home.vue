<template>
  <Page>
    <ActionBar>
      <Label :text="pageTitle"></Label>
    </ActionBar>

    <GridLayout :columns="isTablet ? '*, 2*' : '*'">
      <GridLayout col="0" class="left-column">
        <ListView class="list-group" for="item in types" @itemTap="select">
          <v-template>
            <GridLayout class="list-group-item" rows="*" columns="auto, *">
              <Image row="0" col="0" :src="item.img" class="thumb"></Image>
              <Label row="0" col="1" :text="item.title"></Label>
            </GridLayout>
          </v-template>
        </ListView>
      </GridLayout>
    </GridLayout>
  </Page>
</template>

<script>
const DeviceType = require("tns-core-modules/ui/enums").DeviceType;
const isTablet =
  require("tns-core-modules/platform").device.deviceType == DeviceType.Tablet;
import DetailPage from "./DetailPage";
const types = require("../types").types;

export default {
  data() {
    return {
      isTablet: isTablet,
      types: types,
      selected: {},
      pageTitle: "قائمة الأذكار"
    };
  },
  methods: {
    select: function(event) {
      // Update the selected instance variable for tablet users
      const selected = types[event.index];
      if (isTablet) {
        this.selected = selected;
      } else {
        // And navigate phone users.
        this.$navigateTo(DetailPage, {
          props: {
            selected: selected
          }
        });
      }
    }
  }
};
</script>

<style scoped lang="scss">
@import "~@nativescript/theme/scss/variables/blue";

// Custom styles
.fas {
  @include colorize($color: accent);
}

.info {
  font-size: 20;
  horizontal-align: center;
  vertical-align: center;
}

.home-panel {
  vertical-align: center;
  font-size: 20;
  margin: 15;
}

.description-label {
  margin-bottom: 15;
}
</style>
