<template>
  <div class="container">
    <div class="checkbox-container">
      <el-checkbox v-model="checkAll" @change="checkAllClick" :disabled="checkAllDisabled" :indeterminate="isIndeterminate">
        <el-text class="title">{{ title }}</el-text>
        <el-text class="statistic">
          <el-text class="checked">{{ checkedLength }}</el-text>
          <el-text class="delimiter">/</el-text>
          <el-text class="total">{{ treeLength }}</el-text>
        </el-text>
      </el-checkbox>
    </div>
    <div class="tree-container">
      <el-tree ref="elTreeRef" :default-expand-all="defaultExpandAll" :data="treeData" :node-key="nodeKey" :props="defaultProps" show-checkbox
               @check="updateCheckedKeys" />
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ElTree } from 'element-plus'
import { computed, onMounted, ref, watch } from 'vue'

const props = defineProps({
  modelValue: [Object],
  title: {
    type: String,
    required: true
  },
  data: {
    type: Array<any>,
    required: true,
    default: []
  },
  remove: {
    type: Boolean,
    required: true,
    default: false
  },
  defaultProps: {
    type: Object,
    default: () => {
      return {
        children: 'children',
        label: 'label'
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
  defaultExpandAll: {
    type: Boolean,
    default: true
  },
  isIndeterminate: {
    type: Boolean,
    default: false
  }
})

const emits = defineEmits(['update:modelValue'])

const elTreeRef = ref(ElTree)

const treeData = ref<any[]>([])
const treeItems = ref<any[]>([])
const treeLength = computed(() => {
  return treeItems.value.length
})

const checkAll = ref(false)
const checkAllDisabled = computed(() => {
  return treeLength.value === 0
})

const checkedItems = ref([])
const checkedLength = computed(() => {
  return checkedItems.value.length
})

const clearCheckedItems = () => {
  checkedItems.value = []
}

const setCheckAll = (checked: boolean) => {
  checkAll.value = checked
}

const addTreeItem = (item: any) => {
  treeItems.value.push(item)
}

const setTreeItems = (treeArr: any[], clear: boolean = true) => {
  if (clear) {
    treeItems.value = []
  }
  for (const item of treeArr) {
    addTreeItem(item)
    if (item[props.defaultProps?.children]) {
      setTreeItems(item[props.defaultProps?.children], false)
    }
  }
}

const updateCheckedKeys = () => {
  checkedItems.value = elTreeRef.value.getCheckedKeys(false, true)
  emits('update:modelValue', {
    keys: checkedItems.value,
    nodes: elTreeRef.value.getCheckedNodes(false)
  })
}

const checkAllClick = (checked: boolean) => {
  if (checked) {
    elTreeRef.value.setCheckedNodes(props.data)
  } else {
    elTreeRef.value.setCheckedNodes([])
  }
  updateCheckedKeys()
}

watch(checkedLength, (newCheckedLength) => {
  setCheckAll(newCheckedLength !== 0 && treeLength.value === newCheckedLength)
})

watch(() => props.remove, (newRemove) => {
  if (newRemove) {
    elTreeRef.value.getCheckedNodes().forEach((node: any) => {
      elTreeRef.value.remove(node)
    })
    clearCheckedItems()
    setTreeItems(treeData.value, true)
  }
}, { immediate: true })

watch(() => props.data, (newData) => {
  newData?.forEach((node: any) => {
    if (!elTreeRef.value.getNode(node[props.nodeKey])) {
      elTreeRef.value.append(node, node[props.pidKey])
    }
  })
  setTreeItems(treeData.value, true)
})

onMounted(() => {
  treeData.value = props.data
  setTreeItems(props.data)
})
</script>

<style lang="scss" scoped>
.container {
  width: 100%;
}
</style>
