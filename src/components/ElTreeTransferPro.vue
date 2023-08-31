<template>
  <div class="tree-transfer-container">
    <el-tree-transfer-select :remove="leftRemove" :data="leftData" :node-key="nodeKey" :pid-key="pidKey" :title="leftTitle" v-model="leftCheckedValue" />
    <div class="transfer-button-container">
      <div>
        <el-button :disabled="toLeftDisabled" :icon="ArrowLeft" type="primary" @click="transferToLeft" />
        <el-button :disabled="toRightDisabled" :icon="ArrowRight" type="primary" @click="transferToRight" />
      </div>
    </div>
    <el-tree-transfer-select :remove="rightRemove" :data="rightData" :node-key="nodeKey" :pid-key="pidKey" :title="rightTitle" v-model="rightCheckedValue" />
  </div>
</template>

<script lang="ts" setup>
import { ArrowLeft, ArrowRight } from '@element-plus/icons-vue'
import { computed, ref, watch } from 'vue'
import ElTreeTransferSelect from './ElTreeTransferSelect.vue'

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
  return props.titles[0] ?? ''
})
const leftData = ref([])
const leftRemove = ref(false)
const leftCheckedValue = ref<any>(null)
const leftCheckedKeys = computed(() => {
  return leftCheckedValue?.value?.keys ?? []
})

const toLeftDisabled = computed(() => {
  return (rightData.value && rightData.value?.length === 0) || (rightCheckedKeys.value && rightCheckedKeys.value.length === 0)
})

const transferToLeft = () => {
  leftRemove.value = false
  rightRemove.value = true
  leftData.value = JSON.parse(JSON.stringify(rightCheckedValue?.value?.nodes))
  setTimeout(() => {
    rightCheckedValue.value = {
      keys: [],
      nodes: []
    }
    rightRemove.value = false
  }, 1)
}

const rightTitle = computed(() => {
  return props.titles[1] ?? ''
})
const rightData = ref([])
const rightRemove = ref(false)
const rightCheckedValue = ref<any>(null)
const rightCheckedKeys = computed(() => {
  return rightCheckedValue?.value?.keys ?? []
})

const toRightDisabled = computed(() => {
  return (leftData.value && leftData.value?.length === 0) || (leftCheckedKeys.value && leftCheckedKeys.value.length === 0)
})
const transferToRight = () => {
  rightRemove.value = false
  leftRemove.value = true
  rightData.value = JSON.parse(JSON.stringify(leftCheckedValue?.value?.nodes))
  setTimeout(() => {
    leftCheckedValue.value = null
    leftRemove.value = false
  }, 1)
}

watch(() => props.dataSource, (newDataSource) => {
  leftData.value = JSON.parse(JSON.stringify(newDataSource))
}, { immediate: true })

</script>

<style lang="scss" scoped>
.tree-transfer-container {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  height: 100%;
  
  .transfer-button-container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100%;
    width: 100%;
  }
}
</style>
