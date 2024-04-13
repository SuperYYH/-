<!-- 点击详情页面-xx状态跳转的 页面-->
<script setup>
  import { ref, computed } from 'vue'
  import { onLoad } from '@dcloudio/uni-app'
  import taskApi from '@/apis/task'
  // 原定提货时间
  const planDepartureTime = ref('')
  // 任务的ID
  const id = ref('')

  // 延迟时间
  const delayTime = ref('')
  // 验证延迟时间不能超过两小时
  const delayTimeValid = computed(() => {
    // 验证数据是否合法（不能超过2小时即 7200 * 1000）
    const start = new Date(planDepartureTime.value)
    const end = new Date(delayTime.value)
    return end - start > 0 && end - start <= 7200 * 1000
  })

  // 延迟原因
  const delayReason = ref('')
  // 统计字数
  const wordsCount = computed(() => {
    return delayReason.value.length
  })
  // 验证延迟原因
  const delayReasonValid = computed(() => {
    return wordsCount.value <= 50 && wordsCount.value > 0
  })
  // 计算属性data prop computed $route vuex数据等响应数据改变
  // 是否允许表单提交 两个条件都满足了才可以提交
  const enableSubmit = computed(() => {
    return !delayReasonValid.value || !delayTimeValid.value
  })

  onLoad((query) => {
    // 获取地址中参数
    planDepartureTime.value = query.planDepartureTime
    id.value = query.id
  })

  // 获取用户选择的时间
  function onPickerChange(ev) {
    // 延迟时间必定与原定时间处于同一天
    const [date] = planDepartureTime.value.split(' ')
    delayTime.value = `${date} ${ev.detail.value}`
  }

  // 提交延迟提货数据
  async function onFormSubmit() {
    // 提交的数据
    const formData = {
      id: id.value,
      delayTime: delayTime.value,
      delayReason: delayReason.value,
    }

    const { code } = await taskApi.delay(formData)
    // 检测接口是否调用成功
    if (code !== 200) return uni.utils.toast('延迟申请失败!')
    // 跳转到任务列表 清空所有返回栈
    uni.reLaunch({ url: '/pages/task/index' })
  }
</script>

<template>
  <view class="page-container">
    <uni-list :border="false">
      <uni-list-item
        title="原定时间 "
        showArrow
        :right-text="planDepartureTime"
      />
      <uni-list-item title="延迟时间" showArrow>
        <template v-slot:footer>
          <picker @change="onPickerChange" class="time-picker" mode="time">
            <text v-if="!delayTime">不可超过2个小时</text>
            <!-- 变红色的样式 -->
            <text :class="{ error: !delayTimeValid }" v-else>
              {{ delayTime }}:00
            </text>
          </picker>
        </template>
      </uni-list-item>
      <uni-list-item direction="column">
        <template v-slot:body>
          <view class="textarea-wrapper">
            <textarea
              v-model="delayReason"
              class="textarea"
              placeholder-style="color: #818181"
              placeholder="请输入延迟提货原因"
            ></textarea>
            <!-- 变红色的样式 -->
            <text :class="{ error: wordsCount > 50 }" class="words-count">
              {{ wordsCount }}/50
            </text>
          </view>
        </template>
      </uni-list-item>
      <uni-list-item :border="false">
        <template v-slot:body>
          <button @click="onFormSubmit" :disabled="enableSubmit" class="button">
            提交
          </button>
        </template>
      </uni-list-item>
    </uni-list>
  </view>
</template>

<style lang="scss" scoped>
  @import './index.scss';
</style>
