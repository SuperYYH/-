<script setup>
  import { ref, onMounted } from 'vue'
  import taskApi from '@/apis/task'

  // 待提货任务列表
  const pickupList = ref([])

  onMounted(() => {
    getPickupList()
  })

  // 获取待提货的列表
  async function getPickupList(page = 1, pageSize = 5) {
    const { code, data } = await taskApi.list(1, page, pageSize)
    if (code !== 200) return uni.utils.toast('获取列表失败!')
    // 数据渲染
    pickupList.value = data.items
  }
</script>

<template>
  <scroll-view scroll-y refresher-enabled class="scroll-view">
    <view class="scroll-view-wrapper">
      <view v-for="pickup in pickupList" :key="pickup.id" class="task-card">
        <navigator
          hover-class="none"
          :url="`/subpkg_task/detail/index?id=${pickup.id}`"
        >
          <view class="header">
            <text class="no">任务编号: {{ pickup.transportTaskId }}</text>
            <!-- <text class="status">已延迟</text> -->
          </view>
          <view class="body">
            <view class="timeline">
              <view class="line">{{ pickup.startAddress }}</view>
              <view class="line">{{ pickup.endAddress }}</view>
            </view>
          </view>
        </navigator>
        <view class="footer">
          <view class="label">提货时间</view>
          <view class="time">{{ pickup.planDepartureTime }}</view>
          <navigator
            v-if="pickup.enablePickUp"
            hover-class="none"
            :url="`/subpkg_task/pickup/index?id=${pickup.id}`"
            class="action"
          >
            提货
          </navigator>
          <text v-else disabled class="action"> 提货 </text>
        </view>
      </view>
      <view v-if="false" class="task-blank">无待提货物</view>
    </view>
  </scroll-view>
</template>

<style lang="scss" scoped>
  @import './styles.scss';
</style>
