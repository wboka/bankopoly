<template>
  <div>
    <h1 class="text-center">Bankopoly</h1>

    <div v-show="!isLoading">
      <button type="button" @click="stopScan">Stop</button>
      <video id="scan-preview" poster="data:image/gif,AAAA" ref="scanner"></video>
    </div>

    <button type="button" @click="startScan" v-show="isLoading">Scan</button>

    <div class="banking-unit">
      <div class="flex flex-wrap">
        <div
          class="w-full text-right text-4xl border-2 border-slate-700 rounded-full"
        >
          ${{ amount }}{{ unit }}
        </div>
        <p class="w-full text-center text-gray-500">
          {{ cardNumber }}
        </p>
      </div>
      <div class="flex flex-wrap">
        <div class="w-1/3">
          <button type="button" class="bg-red-700 text-white" @click="setUnit">
            M
          </button>
        </div>
        <div class="w-1/3">
          <button type="button" class="bg-green-700 text-white" @click="passGo">
            &larr;
          </button>
        </div>
        <div class="w-1/3">
          <button type="button" class="bg-blue-700 text-white" @click="setUnit">
            K
          </button>
        </div>
      </div>
      <div class="flex flex-wrap">
        <div class="w-1/3">
          <button type="button" @click="addNumber">7</button>
        </div>
        <div class="w-1/3">
          <button type="button" @click="addNumber">8</button>
        </div>
        <div class="w-1/3">
          <button type="button" @click="addNumber">9</button>
        </div>
      </div>
      <div class="flex flex-wrap">
        <div class="w-1/3">
          <button type="button" @click="addNumber">4</button>
        </div>
        <div class="w-1/3">
          <button type="button" @click="addNumber">5</button>
        </div>
        <div class="w-1/3">
          <button type="button" @click="addNumber">6</button>
        </div>
      </div>
      <div class="flex flex-wrap">
        <div class="w-1/3">
          <button type="button" @click="addNumber">1</button>
        </div>
        <div class="w-1/3">
          <button type="button" @click="addNumber">2</button>
        </div>
        <div class="w-1/3">
          <button type="button" @click="addNumber">3</button>
        </div>
      </div>
      <div class="flex flex-wrap">
        <div class="w-1/3">
          <button type="button" @click="clear">C</button>
        </div>
        <div class="w-1/3">
          <button type="button" @click="addNumber">0</button>
        </div>
        <div class="w-1/3">
          <button type="button">.</button>
        </div>
      </div>
      <div class="flex flex-wrap">
        <div class="w-1/3">
          <button type="button">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="1.5"
              stroke="currentColor"
              class="icon"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M19 7.5v3m0 0v3m0-3h3m-3 0h-3m-2.25-4.125a3.375 3.375 0 11-6.75 0 3.375 3.375 0 016.75 0zM4 19.235v-.11a6.375 6.375 0 0112.75 0v.109A12.318 12.318 0 0110.374 21c-2.331 0-4.512-.645-6.374-1.766z"
              />
            </svg>
          </button>
        </div>
        <div class="w-1/3">
          <button type="button">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="1.5"
              stroke="currentColor"
              class="icon"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M18 18.72a9.094 9.094 0 003.741-.479 3 3 0 00-4.682-2.72m.94 3.198l.001.031c0 .225-.012.447-.037.666A11.944 11.944 0 0112 21c-2.17 0-4.207-.576-5.963-1.584A6.062 6.062 0 016 18.719m12 0a5.971 5.971 0 00-.941-3.197m0 0A5.995 5.995 0 0012 12.75a5.995 5.995 0 00-5.058 2.772m0 0a3 3 0 00-4.681 2.72 8.986 8.986 0 003.74.477m.94-3.197a5.971 5.971 0 00-.94 3.197M15 6.75a3 3 0 11-6 0 3 3 0 016 0zm6 3a2.25 2.25 0 11-4.5 0 2.25 2.25 0 014.5 0zm-13.5 0a2.25 2.25 0 11-4.5 0 2.25 2.25 0 014.5 0z"
              />
            </svg>
          </button>
        </div>
        <div class="w-1/3">
          <button type="button">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="1.5"
              stroke="currentColor"
              class="icon"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M22 10.5h-6m-2.25-4.125a3.375 3.375 0 11-6.75 0 3.375 3.375 0 016.75 0zM4 19.235v-.11a6.375 6.375 0 0112.75 0v.109A12.318 12.318 0 0110.374 21c-2.331 0-4.512-.645-6.374-1.766z"
              />
            </svg>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { BrowserMultiFormatReader, Exception } from "@zxing/library";

export default {
  name: "BankingUnit",
  data() {
    return {
      amount: "",
      unit: "",
      isLoading: true,
      codeReader: new BrowserMultiFormatReader(),
      isMediaStreamAPISupported:
        navigator &&
        navigator.mediaDevices &&
        "enumerateDevices" in navigator.mediaDevices,
      cardNumber: "-",
    };
  },
  mounted() {
    if (!this.isMediaStreamAPISupported) {
      throw new Exception("Media Stream API is not supported");
      return;
    }
  },
  beforeUnmount() {
    this.stopScan();
  },
  methods: {
    addNumber(e) {
      if (!/\d+/.test(this.amount)) {
        this.amount = "";
      }

      this.amount += "" + e.target.innerText;
    },
    clear() {
      this.cardNumber = "-";
      this.amount = "-";
      this.unit = "";
    },
    passGo() {
      this.amount = "2";
      this.unit = "M";
    },
    setUnit(e) {
      this.unit = e.target.innerText;
    },
    startScan() {
      this.init();

      this.$refs.scanner.oncanplay = (event) => {
        this.isLoading = false;
      };
    },
    stopScan() {
      this.isLoading = true;
      this.codeReader.reset();
    },
    init() {
      this.codeReader.decodeFromVideoDevice(
        undefined,
        this.$refs.scanner,
        (result, err) => {
          if (result) {
            this.cardNumber = result.text;

            this.stopScan();
          }
        }
      );
    },
  },
};
</script>

<style scoped>
#scan-preview {
  @apply w-full mx-auto h-full;
}
.scanner-container {
  position: relative;
}
</style>
