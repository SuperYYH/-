<script setup>
  import { ref } from 'vue'

  // 获取表单数据
  const realName = ref('')
  const idCard = ref('')

  function onFormSubmit() {
    console.log(realName)
    try {
      // 获取设备信息
      const metaInfo = uni.getFacialRecognitionMetaInfo()
      // 调用云函数
      uniCloud.callFunction({
        name: 'uni-verify',
        data: { metaInfo, realName: realName.value, idCard: idCard.value },
        success({ result }) {
          // 客户端调起sdk刷脸认证
          uni.startFacialRecognitionVerify({
            certifyId: result.certifyId,
            success() {
              uni.utils.toast('认证成功!')
            },
            fail() {
              uni.utils.toast('认证失败!')
            },
          })
        },
      })
    } catch (err) {
      console.log(err)
    }
  }
</script>

<template>
  <uni-forms class="verify-form">
    <uni-forms-item name="account">
      <input
        type="text"
        v-model="realName"
        placeholder="请输入姓名"
        class="uni-input-input"
        placeholder-style="color: #818181"
      />
    </uni-forms-item>
    <uni-forms-item name="password">
      <input
        type="text"
        v-model="idCard"
        placeholder="请输入身份证号"
        class="uni-input-input"
        placeholder-style="color: #818181"
      />
    </uni-forms-item>
    <button @click="onFormSubmit" class="submit-button">认证</button>
  </uni-forms>
</template>

<style lang="scss" scoped>
  @import './index.scss';
</style>
