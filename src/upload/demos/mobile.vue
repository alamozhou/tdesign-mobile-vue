<template>
  <div class="tdesign-mobile-demo">
    <h1 class="title">Upload上传</h1>
    <p class="summary">上传图片（单张上传、多张上传</p>
    <tdesign-demo-block title="01 类型" summary="上传图片">
      <div class="upload-demo">
        <div class="upload-title">单选上传图片</div>
        <t-upload :multiple="false" :action="action"> </t-upload>
        <div class="upload-title">多选上传图片</div>
        <t-upload :multiple="true" :action="action"> </t-upload>
        <div class="upload-title">限制图片上传的大小为不超过3KB</div>
        <t-upload :action="action" :size-limit="sizeLimit" :before-upload="onBeforeUpload"> </t-upload>
        <!-- <div class="upload-title">限制只能上传3张图片</div>
        <t-upload :action="action" :multiple="true" :max="3" :before-upload="onBeforeUploadLimitNum"> </t-upload> -->
        <div class="upload-title">图片上传，一行展示3列</div>
        <t-upload :action="action" :multiple="true" :grid-config="gridConfig"> </t-upload>
        <div class="upload-title">图片上传，触发各类事件</div>
        <t-upload
          :action="action"
          :multiple="true"
          :on-fail="onFail"
          :on-progress="onProgress"
          :on-change="onChange"
          :on-preview="onPreview"
          :on-success="onSuccess"
          :on-remove="onRemove"
        >
        </t-upload>
      </div>
    </tdesign-demo-block>
  </div>
</template>
<script lang="ts">
import { defineComponent, h } from 'vue';
import { AddIcon } from 'tdesign-icons-vue-next';
import { UploadChangeContext, UploadFile, UploadRemoveContext, SuccessContext, ProgressContext } from '../type';
import { isOverSizeLimit } from '../util';
import { Toast } from '@/components';

export default defineComponent({
  components: {},
  setup() {
    const addIcon = () => h(AddIcon);
    const action = 'https://service-bv448zsw-1257786608.gz.apigw.tencentcs.com/api/upload-demo';
    // const action = 'www';
    const max = 10;
    const sizeLimit = {
      size: 3,
      unit: 'KB',
      message: '您上传的图片超过大小限制',
    };
    const gridConfig = {
      column: 3,
    };
    const onBeforeUpload = (file: UploadFile) => {
      console.log('====onBeforeUpload', file);
      const isOverSize = isOverSizeLimit(file.size, sizeLimit.size, sizeLimit.unit);
      if (isOverSize) {
        Toast(sizeLimit.message);
      }
      return !isOverSize;
    };
    const onFail = ({ e, file }) => {
      console.log('---onFail', file, e);
      return null;
    };
    const onProgress = ({ file, percent, type, e }: ProgressContext) => {
      console.log('---onProgress:', file, percent, type, e);
    };
    const onChange = (files: Array<UploadFile>, { e, response, trigger, index, file }: UploadChangeContext) => {
      console.log('====onChange', files, e, response, trigger, index, file);
    };
    const onPreview = ({ file, e }) => {
      console.log('====onPreview', file, e);
    };
    const onSuccess = ({ file, fileList, response, e }: SuccessContext) => {
      console.log('====onSuccess', file, fileList, e, response);
    };
    const onRemove = ({ index, file, e }: UploadRemoveContext) => {
      console.log('====onRemove', index, file, e);
    };
    const onBeforeUploadLimitNum = (file: UploadFile) => {
      // TODO 超过限制这个信息暂时未传到组件外，所以拿不到，是不是需要再加一个onExceed的函数?
      // console.log('====onBeforeUpload', file);
      // const isOverLimit = this.files > this.max;
      // if (isOverLimit) {
      //   Toast(`您上传的图片超过${this.max}`);
      // }
      // return !isOverLimit;
    };

    return {
      action,
      max,
      addIcon,
      onFail,
      onChange,
      onPreview,
      onSuccess,
      onRemove,
      onProgress,
      onBeforeUpload,
      onBeforeUploadLimitNum,
      gridConfig,
      sizeLimit,
    };
  },
});
</script>
<style scoped lang="less">
.upload-demo {
  background: #fff;
  .upload-title {
    padding: 12px 0 12px 16px;
    color: rgba(0, 0, 0, 0.9);
    font-size: 16px;
    font-weight: 400;
    font-family: PingFang SC;
    text-align: left;
    line-height: 24px;
  }
}
</style>
