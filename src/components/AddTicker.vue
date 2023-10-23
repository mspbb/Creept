<template>
  <section>
    <div class="flex">
      <div class="max-w-xs">
        <label for="wallet" class="block text-sm font-medium text-gray-700"
          >Тикер {{ ticker }}</label
        >
        <div class="mt-1 relative rounded-md shadow-md">
          <input
            v-on:keydown.enter="add"
            v-on:input="showPossible"
            v-model="ticker"
            type="text"
            name="wallet"
            id="wallet"
            class="block w-full pr-10 border-gray-300 text-gray-900 focus:outline-none focus:ring-gray-500 focus:border-gray-500 sm:text-sm rounded-md"
            placeholder="Например DOGE"
          />
        </div>
        <div class="flex bg-white shadow-md p-1 rounded-md shadow-md flex-wrap">
          <span
            v-for="creeptElemnt of possibleCreept"
            v-bind:key="creeptElemnt"
            v-on:click="add((this.ticker = creeptElemnt))"
            class="inline-flex items-center px-2 m-1 rounded-md text-xs font-medium bg-gray-300 text-gray-800 cursor-pointer"
          >
            {{ creeptElemnt }}
          </span>
        </div>
        <div v-if="propsTypeOfValid" class="text-sm text-red-600">
          {{ propsTypeOfValid }}
        </div>
      </div>
    </div>
    <add-button
      v-on:click="add"
      :disabled="disabled"
      v-show="buttonActive"
      type="button"
    />
  </section>
</template>

<script>
import AddButton from "./AddButton.vue";
export default {
  components: {
    AddButton,
  },

  props: {
    disabled: {
      type: Boolean,
      required: false,
      default: false,
    },
    propsTypeOfValid: {
      type: String,
      required: false,
      default: "",
    },
  },

  emits: {
    "add-ticker": (value) => typeof value === "string" && value.length > 0,
  },

  data() {
    return {
      ticker: "",
      possibleCreept: [],
      buttonActive: true,
      /* typeOfValid: this.propsTypeOfValid; */
      creeptArr: {},
    };
  },

  created() {
    this.fetchArr();
  },

  methods: {
    add() {
      if (this.ticker.length === 0) {
        return;
      }
      this.$emit("add-ticker", this.ticker);
      this.ticker = "";
    },

    //мое
    showPossible() {
      let arrData = this.creeptArr.Data;
      let testArr = [];

      for (this.key in arrData) {
        if (
          arrData[this.key].Symbol.slice(0, this.ticker.length) ==
          this.ticker.toLocaleUpperCase()
        ) {
          testArr.push(arrData[this.key].Symbol);
        }
      }
      this.possibleCreept = testArr.slice(0, 4);
    },
    async fetchArr() {
      this.creeptArr = await (
        await fetch(
          "https://min-api.cryptocompare.com/data/all/coinlist?summary=true"
        )
      ).json();
    },
  },

  watch: {
    ticker() {
      this.typeOfValid = "";
    },
  },
};
</script>
