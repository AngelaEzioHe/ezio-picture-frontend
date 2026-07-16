<template>
  <a-modal
    class="image-cropper"
    v-model:visible="visible"
    title="编辑图片"
    :footer="false"
    @cancel="closeModal"
  >
    <!-- 图片裁切组件 -->
    <vue-cropper
      ref="cropperRef"
      :img="imageUrl"
      output-type="png"
      :info="true"
      :can-move="false"
      :can-move-box="true"
      :fixed-box="false"
      :auto-crop="true"
      :center-box="true"
    />
    <div style="margin-bottom: 16px" />
    <!-- 图片操作 -->
    <div class="image-cropper-actions">
      <a-space>
        <a-button @click="rotateLeft">向左旋转</a-button>
        <a-button @click="rotateRight">向右旋转</a-button>
        <a-button @click="changeScale(1)">放大</a-button>
        <a-button @click="changeScale(-1)">缩小</a-button>
        <a-button type="primary" :loading="loading" @click="handleConfirm">确定</a-button>
      </a-space>
    </div>
  </a-modal>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import { uploadPictureUsingPost } from '@/api/pictureController.ts'
import { message } from 'ant-design-vue'

interface Props {
  imageUrl?: string
  picture?: API.PictureVO
  spaceId?: number
  onSuccess?: (newPicture: API.PictureVO) => void
}

const props = defineProps<Props>()

//获取图片裁切器的引用
const cropperRef = ref()

//缩放比例
const changeScale = (num) => {
  cropperRef.value?.changeScale(num)
}

//向左旋转
const rotateLeft = () => {
  cropperRef.value?.rotateLeft()
}

//向右旋转
const rotateRight = () => {
  cropperRef.value?.rotateRight()
}

const handleConfirm = () => {
  cropperRef.value.getCropBlob((blob: Blob) => {
    //blob 为已经裁切好的文件
    const  fileName = (props.picture?.name || 'image')+'.png'
    const file = new File([blob], fileName, { type: blob.type })
    //上传图片
    handleUpload({ file })
  })
}

const loading = ref(false)

const handleUpload = async ({ file }: any) => {
  loading.value = true
  try {
    const params: API.PictureUploadRequest = props.picture ? { id: props.picture.id } : {}
    params.spaceId = props.spaceId
    const res = await uploadPictureUsingPost(params, {}, file)
    if (res.data.code === 0 && res.data.data) {
      message.success('图片上传成功')
      props.onSuccess?.(res.data.data)
      closeModal()
    } else {
      message.error('图片上传失败,' + res.data.message)
    }
  } catch (error) {
    console.error('图片上传失败', error)
    message.error('图片上传失败,' + error.message)
  }
  loading.value = false
}

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

<style>
.image-cropper {
  text-align: center;
}
.image-cropper .vue-cropper {
  height: 400px !important;
}
</style>
