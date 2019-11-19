<template>
  <Page class="page">
    <ActionBar :title="selected.title"></ActionBar>
    <StackLayout col="1" class="p-10">
      <ActivityIndicator v-if="isBusy" :busy="isBusy" />
      <Label text="قائمة الأذكار" v-else style="text-align: center; font-size: 20;" />
      <ListView
        style="background-color: #4F617D; color: white;"
        v-if="azkar.length"
        for="item in azkar"
        @itemTap="onItemTap"
      >
        <v-template>
          <WrapLayout class="zikr-item">
            <Label
              :text="item.zekr"
              textWrap="true"
              width="100%"
              :class="[item.repeat == 0 ? 'off' : 'on']"
            />

            <Label
              v-if="item.bless"
              :text="item.bless"
              textWrap="true"
              width="100%"
              :class="[item.repeat == 0 ? 'off' : 'bless-on']"
            />

            <StackLayout class="btns" orientation="horizontal">
              <Button
                text="إعادة"
                class="btn-counter"
                horizontalAlignment="right"
                width="50%"
                height="70"
                @tap="resetCount(item)"
              />
              <Button
                class="btn-repeat"
                :text="repeatTimeText(item.repeat)"
                horizontalAlignment="left"
                width="50%"
                height="70"
                @tap="decreaseCount(item)"
                :isEnabled="item.repeat > 0"
              />
            </StackLayout>

            <StackLayout orientation="vertical" width="100%" height="20" backgroundColor="#f2f3ff"></StackLayout>
          </WrapLayout>
        </v-template>
      </ListView>
    </StackLayout>
  </Page>
</template>
<script>
const httpModule = require("tns-core-modules/http");

export default {
  props: ["selected"],
  data() {
    return {
      isBusy: true,
      azkar: [],
      azkarCount: []
    };
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    fetchData() {
      httpModule
        .getJSON({
          url: this.selected.url,
          method: "GET"
        })
        .then(
          response => {
            this.isBusy = !this.isBusy;
            this.azkar = response.content;
          },
          e => {}
        );
    },
    decreaseCount(itemName) {
      const selected = this.azkar.find(item => {
        return item.zekr === itemName.zekr;
      });

      const found = this.azkarCount.some(el => el.name === selected.zekr);
      if (!found) {
        this.azkarCount.push({
          name: selected.zekr,
          count: selected.repeat
        });
      }
      selected.repeat--;
    },
    resetCount(itemName) {
      const selected = this.azkar.find(item => {
        return item.zekr === itemName.zekr;
      });
      const found = this.azkarCount.find(el => el.name === selected.zekr);
      if (!found) {
        return;
      }
      selected.repeat = found.count;
    },
    repeatTimeText(repeat) {
      switch (parseInt(repeat)) {
        case 1:
          return "  مرة واحدة";
          break;
        case 2:
          return repeat + " مرتين ";
          break;
        case 0:
          return "تقبل الله";
          break;
        case 100:
          return repeat + " مرة ";
          break;
        default:
          return repeat + " مرات ";
      }
    }
  }
};
</script>

<style>
label {
  font-size: 20;
  text-align: left;
  padding: 10;
  font-weight: bolder;
}

.bless-on {
  background-color: #0059b3;
  color: white;
}

.off {
  background-color: slategray;
  color: black;
}

.btns {
  border-radius: 5;
}

button {
  border-radius: 2px;
}

.btn-repeat {
  color: #fff;
  background-color: #1bf2d6;
}

.btn-counter {
  color: #fff;
  background-color: #f21b4a;
}
</style>