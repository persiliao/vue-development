<template>
  <div class="tree-transfer-container">
    <el-tree-transfer-select :data="leftData" :node-key="nodeKey" :pid-key="pidKey" :title="leftTitle" v-model="leftCheckedValue" />
    <div class="transfer-button">
      <el-button :disabled="toLeftDisabled" :icon="ArrowLeft" type="primary" @click="transferToLeft"></el-button>
      <el-button :disabled="toRightDisabled" :icon="ArrowRight" type="primary" @click="transferToRight"></el-button>
    </div>
    <el-tree-transfer-select :data="rightData" :node-key="nodeKey" :pid-key="pidKey" :title="rightTitle" v-model="rightCheckedValue" />
  </div>
</template>

<script lang="ts" setup>
import { ArrowLeft, ArrowRight } from '@element-plus/icons-vue'
import { computed, ref, watch } from 'vue'
import ElTreeTransferSelect from './ElTreeTransferSelect.vue'

interface CheckedValue {
  keys: Array<Number>,
  nodes: Array<any>
}

const props = defineProps({
  dataSource: {
    type: Array<any>,
    required: true,
    default: []
  },
  defaultProps: {
    type: Object,
    default: () => {
      return {
        children: 'children',
        label: 'name'
      }
    }
  },
  nodeKey: {
    type: String,
    default: 'id'
  },
  pidKey: {
    type: String,
    default: 'pid'
  },
  titles: {
    type: Array<string>,
    default: ['待选', '已选']
  }
})

const emits = defineEmits(['update:modelValue'])

const leftTitle = computed(() => {
  return props.titles[0]
})
const leftData = ref([])
const leftCheckedValue = ref<CheckedValue>()
const leftCheckKeys = computed(() => {
  return leftCheckedValue.value?.keys ?? []
})

const toLeftDisabled = computed(() => {
  return (rightData.value && rightData.value?.length === 0) || (rightCheckKeys.value && rightCheckKeys.value.length === 0)
})

const transferToLeft = () => {
  leftData.value = JSON.parse(JSON.stringify(rightCheckedValue?.value?.nodes))
}

const rightTitle = computed(() => {
  return props.titles[1]
})
const rightData = ref([])
const rightCheckedValue = ref<CheckedValue>()
const rightCheckKeys = computed(() => {
  return rightCheckedValue.value?.keys ?? []
})

const toRightDisabled = computed(() => {
  return (leftData.value && leftData.value?.length === 0) || (leftCheckKeys.value && leftCheckKeys.value.length === 0)
})
const transferToRight = () => {
  console.debug('transferToRight', leftCheckedValue?.value?.nodes)
  rightData.value = JSON.parse(JSON.stringify(leftCheckedValue?.value?.nodes))
}

watch(() => props.dataSource, (newDataSource) => {
  leftData.value = JSON.parse(JSON.stringify(newDataSource))
}, { immediate: true })

</script>

<style lang="scss" scoped>
.tree-transfer-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
