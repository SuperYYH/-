<script setup>
  import { ref, onMounted } from 'vue'
  import taskApi from '@/apis/task'

  // 定义数据
  const deliveryList = ref([])

  // 请求数据
  onMounted(() => {
    getDeliveryList()
  })

  // 获取在途列表数据
  async function getDeliveryList() {
    const { code, data } = await taskApi.list(2)
    // 检测接口是否调用成功
    if (code !== 200) return uni.utils.toast('在途列表获取失败!')
    // 数据渲染
    deliveryList.value = data.items
  }
</script>
<template>
  <scroll-view scroll-y refresher-enabled class="scroll-view">
    <view class="scroll-view-wrapper">
      <view
        v-for="delivery in deliveryList"
        :key="delivery.id"
        class="task-card"
      >
        <navigator
          hover-class="none"
          :url="`/subpkg_task/detail/index?id=${delivery.id}`"
        >
          <view class="header">
            <text class="no">任务编号: {{ delivery.transportTaskId }}</text>
          </view>
          <view class="body">
            <view class="timeline">
              <view class="line">{{ delivery.startAddress }}</view>
              <view class="line">{{ delivery.endAddress }}</view>
            </view>
          </view>
        </navigator>
        <view class="footer">
          <view class="label">提货时间</view>
          <view class="time">{{ delivery.created }}</view>
          <!-- status 为 2 交付 -->
          <navigator
            v-if="delivery.status === 2"
            hover-class="none"
            :url="`/subpkg_task/delivery/index?id=${delivery.id}`"
            class="action"
          >
            交付
          </navigator>
          <!-- status 为 4 时为回车登记 -->
          <navigator
            v-if="delivery.status === 4"
            hover-class="none"
            :url="`/subpkg_task/record/index?transportTaskId=${delivery.transportTaskId}&actualDepartureTime=${delivery.actualDepartureTime}`"
            class="action"
          >
            回车登记
          </navigator>
        </view>
      </view>
      <view v-if="false" class="task-blank">无在途货物</view>
    </view>
  </scroll-view>
</template>

<style lang="scss" scoped>
  @import './styles.scss';
</style>
