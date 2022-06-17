<template>
  <div
    ref="lzmvirtual"
    class="container"
    tabindex="1"
  >
    <input
      v-model="inputValue"
      type="text"
      class="select-input"
      :style="inputStyle()"
      @click.stop="selFocus"
    >
    <transition name="podopen">
      <div v-show="showOptions" ref="lzmcontainer" class="options">
        <div class="content" :style="contentStyle()" tabindex="2">
          <div
            v-for="(item, index) in items"
            :key="index"
            class="virtual-item"
            @click.stop="selBlur(item)"
          >
            {{ item.label }}
          </div>
        </div>
      </div>
    </transition>

  </div>
</template>

<script>

export default {
  name: 'LzmVirtual',
  props: {
    options: {
      type: Array,
      default: () => []
    },
    value: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      inputValue: this.value,
      scrollAdd: 0,
      showOptions: false,
      virtualWidth: 100
    }
  },
  computed: {
    findItems() {
      if (this.inputValue) {
        return this.options.filter(val => {
          return String(val.label).includes(this.inputValue)
        })
      } else {
        return this.options
      }
    },
    items() {
      const scrollPosition = this.scrollAdd
      return this.findItems.slice(scrollPosition, scrollPosition + 10)
    }
  },
  mounted() {
    window.addEventListener('click', () => {
      this.showOptions = false
    })
    this.$refs.lzmcontainer.addEventListener('scroll', () => {
      const scrollPosition = Math.floor(this.$refs.lzmcontainer.scrollTop / 34)
      this.scrollAdd = scrollPosition
    })
    this.initStyle()
  },
  methods: {
    initStyle() {
      this.virtualWidth = this.$refs.lzmvirtual.clientWidth
    },
    inputStyle() {
      return `width: ${this.virtualWidth + 6}px`
    },
    contentStyle() {
      const virtulHeight = this.findItems.length * 34
      const paddingTop = this.scrollAdd * 34
      return `width: ${this.virtualWidth}px;height: ${virtulHeight}px; padding-top: ${paddingTop}px`
    },
    selFocus() {
      this.showOptions = true
    },
    selBlur(item) {
      this.inputValue = item.label
      this.$emit('input', String(item.value))
      this.showOptions = false
    }
  }
}
</script>

<style scoped>
.container{
  min-width: 200px;
}
.select-input{
  position: sticky;
  height: 40px;
  top: 0px;
  border: 1px solid #dcdfe6;
  border-radius: 3px;
  padding: 0px 15px;
  color: #606266;
  font-size: inherit;
}
.select-input:hover{
  outline: 1px solid #409eff;
}
.select-input:focus-visible{
  outline: 2px solid #409eff;
}
.options{
  height: 200px;
  min-height: 100px;
  min-width: 100px;
  background-color: white;
  overflow-y: scroll;
  overflow-x: hidden;
  position: absolute;
  margin-top: 5px;
  color: #606266;
  font-size: inherit;
}
.options::-webkit-scrollbar {
  width : 6px;
  height: 10px;
}
.options::-webkit-scrollbar-thumb {
  border-radius   : 3px;
  background-color: #ccc;
}
.options::-webkit-scrollbar-track {
  background   : none;
  border-radius: 8px;
}
.virtual-item{
  height: 34px;
  padding: 0px 10px;
  line-height: 34px;
}
.virtual-item:hover{
  background-color: #f5f7fa;
  color: #409eff;
  cursor: pointer;
}
.podopen-enter-active {
  animation: bounce-in .2s;
}
.podopen-leave-active {
  animation: bounce-out .2s;
}
@keyframes bounce-in {
  0% {
    transform-origin: top;
    transform: scaleY(0);
  }
  100% {
    transform-origin: top;
    transform: scaleY(1);
  }
}
@keyframes bounce-out {
  0% {
    transform-origin: top;
    transform: scaleY(1);
  }
  100% {
    transform-origin: top;
    transform: scaleY(0);
  }
}
</style>