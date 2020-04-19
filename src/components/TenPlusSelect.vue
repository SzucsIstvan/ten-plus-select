<template>
  <div :class="mainClasses">
    <div
      :class="selectContainerClassesCalc"
      v-if="state === 0"
    >
      <select
        :class="selectClassesCalc"
        v-model="selectValue"
      >
        <option
          v-for="option in options"
          :key="option._id"
          :value="Number(option.value)"
        >{{option.text}}</option>
        <option
          :key="plusItem._id"
          :value="plusItem.value"
        >{{plusItem.text}}</option>
      </select>
    </div>
    <div
      class="input-container"
      v-if="state === 1"
    >
      <input
        :class="inputClassesCalc"
        type="number"
        @keydown.enter="onClick"
        ref="input"
        v-model.number="inputValue"
      >
      <button
        :class="buttonClassesCalc"
        @click="onClick"
        v-show="inputValue !== value"
        :disabled="inputValue === value"
      >{{refreshButtonTitle}}</button>
    </div>
  </div>
</template>

<script>
import defaultOptions from "../common/default-options";
export default {
  name: "TenPlusSelect",
  props: {
    options: {
      type: Array,
      require: true,
      default: () => defaultOptions
    },
    value: {
      type: Number,
      require: true,
      default: 1
    },
    plusItem: {
      type: Object,
      require: true,
      default: () => {
        return { _id: 11, value: 11, text: "10+" };
      }
    },
    refreshButtonTitle: {
      type: String,
      require: false,
      default: "Update"
    },
    classes: {
      type: Array,
      require: false,
      default: () => []
    },
    selectContainerClasses: {
      type: Array,
      require: false,
      default: () => []
    },
    inputContainerClasses: {
      type: Array,
      require: false,
      default: () => []
    },
    inputClasses: {
      type: Array,
      require: false,
      default: () => []
    },
    buttonClasses: {
      type: Array,
      require: false,
      default: () => []
    },
    selectClasses: {
      type: Array,
      require: false,
      default: () => []
    }
  },
  data() {
    return {
      selectValue: this.value,
      inputValue: this.value,
      state: 0,
      max: 0,
      min: 0
    };
  },
  watch: {
    value(newValue) {
      if (this.state === 0 && newValue <= this.max) {
        this.selectValue = newValue;
      }
      if (this.state === 1 && newValue <= this.max) {
        this.selectValue = newValue;
        this.state = 0;
      }
      if (this.state === 0 && newValue > this.max) {
        this.state = 1;
        this.setFocusToInput();
      }
      if (this.state === 1) {
        this.inputValue = newValue;
      }
    },
    selectValue(newValue) {
      if (newValue < this.min) {
        this.selectValue = this.min;
      }
      // if (newValue === -1) {
      //   this.state = 1;
      //   this.setFocusToInput();
      //   this.inputValue = this.max + 1;
      // }
      if (newValue > this.max) {
        this.state = 1;
        this.setFocusToInput();
        this.inputValue = newValue;
      } else {
        this.emitValue();
      }
    },
    options(newValues) {
      this.max = this.calcMax(newValues);
      this.min = this.calcMin(newValues);
    }
  },
  computed: {
    mainClasses() {
      const defaultClasses = ["ten-plus-select"];
      return [...defaultClasses, ...this.classes];
    },
    selectContainerClassesCalc() {
      const defaultClasses = ["select-container"];
      return [...defaultClasses, this.selectContainerClasses];
    },
    inputContainerClassesCalc() {
      const defaultClasses = ["input-container"];
      return [...defaultClasses, this.inputContainerClasses];
    },
    inputClassesCalc() {
      const defaultClasses = ["input"];
      return [...defaultClasses, this.inputClasses];
    },
    buttonClassesCalc() {
      const defaultClasses = ["btn"];
      return [...defaultClasses, ...this.buttonClasses];
    },
    selectClassesCalc() {
      const defaultClasses = ["select"];
      return [...defaultClasses, ...this.selectClasses];
    }
  },
  methods: {
    emitValue() {
      if (this.state === 0) {
        this.$emit("input", Number(this.selectValue));
      }
      if (this.state === 1) {
        this.$emit("input", Number(this.inputValue));
      }
    },
    onClick() {
      this.emitValue();
      if (this.inputValue <= this.max) {
        this.state = 0;
        this.selectValue = this.inputValue;
        this.inputValue = null;
      }
    },
    calcMax(items) {
      const values = items.map(item => item.value);
      return Math.max(...values);
    },
    calcMin(items) {
      const values = items.map(item => item.value);
      return Math.min(...values);
    },
    setFocusToInput() {
      this.$nextTick(() => {
        if (this.$refs.input) this.$refs.input.focus();
      });
    }
  },
  mounted() {
    this.max = this.calcMax(this.options);
    this.min = this.calcMin(this.options);

    if (this.inputValue <= this.max) {
      this.state = 0;
    }
    if (this.inputValue >= this.max) {
      this.state = 1;
    }
  }
};
</script>