<template>
  <div class="auth-code_container">
    <div
      class="auth-code_input-box"
      v-for="(item, index) in codeArray"
      :key="index"
      :style="{
        'margin-left':
          (index === 0 ? 0 : codeLength / 2 === index ? 18 : 6) + 'px',
      }"
    >
      <input
        type="number"
        class="auth-code_input"
        @keydown="handleNavigation($event, index)"
        @input="handleValue($event, index)"
        ref="authcode_input"
      />
    </div>
  </div>
</template>
<script>
const numericKey = Array.from({ length: 10 }, (v, i) => i + "");
export default {
  name: "VueAuthCodeInput",
  props: {
    codeLength: {
      type: Number,
      default: 6,
    },
  },
  computed: {
    codeArray() {
      return Array.from({ length: this.codeLength }, () => "");
    },
  },
  methods: {
    handleValue(e, index) {
      if (numericKey.indexOf(e.target.value) > -1) {
        this.updateCodeArray(index, e.target.value);
        this.switchFocusAndSelect(index, 1);
      }
    },
    handleNavigation(e, index) {
      let content = this.$refs["authcode_input"][index].value;
      if (["ArrowRight", "Tab"].indexOf(e.key) > -1) {
        this.switchFocusAndSelect(index, 1);
      } else if (["ArrowLeft"].indexOf(e.key) > -1) {
        this.switchFocusAndSelect(index, -1);
      } else if (["Backspace"].indexOf(e.key) > -1) {
        if (content) {
          this.updateCodeArray(index, "");
        } else {
          this.switchFocusAndClear(index, -1);
        }
      }
    },
    updateCodeArray(index, value) {
      this.$refs["authcode_input"][index].value = value;
      this.codeArray.splice(index, 1, value);
      this.$emit("inputChange", this.codeArray, index);
      if (this.codeArray.every(Boolean)) {
        this.$emit("inputComplete", this.codeArray, index);
      }
    },
    switchFocusAndSelect(index, direction = 1) {
      if (this.$refs["authcode_input"][index + direction]) {
        this.$refs["authcode_input"][index + direction].select();
      }
    },
    switchFocusAndClear(index, direction = 1) {
      if (this.$refs["authcode_input"][index + direction]) {
        this.$refs["authcode_input"][index + direction].value = "";
        this.codeArray.splice(index + direction, 1, "");
        this.$refs["authcode_input"][index + direction].focus();
      }
    },
  },
};
</script>
<style scoped>
.auth-code_container {
  display: flex;
  justify-content: center;
}
.auth-code_input-box {
  width: 49px;
  height: 49px;
}
.auth-code_input {
  width: 45px;
  height: 45px;
  line-height: 45px;
  font-size: 24px;
  padding: 0px;
  text-align: center;
  color: #494949;
  font-weight: 400;
  border: 1px solid #d6d6d6;
  border-radius: 4px;
  background: #fff;
  box-sizing: border-box;
  -webkit-appearance: none;
}
.auth-code_input:focus {
  outline: none;
  border: 1px solid #97cee5;
  box-shadow: 0 0 0 3px rgba(131, 192, 253, 0.5);
}

input[type="number"] {
  -moz-appearance: textfield;
}

input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
</style>
