<template>
  <div class="d-flex flex-items-center flex-justify-between header px-3" v-bind="$attrs">
    <slot name="content">
      <div
        class="d-flex flex-items-center c-pointer"
        data-testid="settings-click"
        @click="$emit('header-click')"
      >
        <VAvatar :name="user.name" class="mr-1" />
        <div class="d-flex flex-column ml-2 mt-1">
          <span class="fs-x-big fw-semibold ff-inter" data-testid="username-label">{{
            user.name
          }}</span>
          <div class="mt-2">
            <span class="fs-x-big fw-semibold ff-inter c-secondary">{{
              hasProPlan ? 'PRO' : 'FREE'
            }}</span>
          </div>
        </div>
      </div>
      <VIcon name="settings" color="white" class="c-pointer" width="10px" />
    </slot>
  </div>
</template>

<script>
import { mapState, mapGetters } from 'vuex'

export default {
  name: 'Header',
  computed: {
    ...mapState(['user']),
    ...mapGetters(['hasProPlan'])
  }
}
</script>

<style lang="scss">
.header {
  height: 95px;
  border-bottom: 2px solid $color-black;
}
</style>
