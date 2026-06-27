<template>
  <a-modal v-model:visible="visible" title="分享图片" :footer="false" @cancel="closeModal">
    <h4>复制分享链接</h4>
    <a-typography-link copyable>
      {{ link }}
    </a-typography-link>
    <div style="margin-bottom: 16px" />
    <h4>手机扫码查看</h4>
    <a-qrcode :value="link" />
  </a-modal>
</template>

<script setup lang="ts">
import { defineProps, ref, withDefaults} from 'vue'

/**
 * 定义组件属性类型
 */
interface Props {
  title: string
  link: string
}

/**
 * 给组件指定初始值
 */
const props = withDefaults(defineProps<Props>(), {
  title: "分享图片",
  link: 'https://www.codefather.cn',
})

const open = ref<boolean>(false)

// 是否可见
const visible = ref(false)

// 打开模态框
const openModal = () => {
  visible.value = true
}

// 关闭模态框
const closeModal = () => {
  visible.value = false
}

// 暴露给父组件的方法
defineExpose({
  openModal,
})
</script>
