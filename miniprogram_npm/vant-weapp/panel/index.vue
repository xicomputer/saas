<template>
    <view class="van-panel van-hairline--top-bottom custom-class">
        <van-cell v-if="title || desc || status" :title="title" :label="desc" :value="status" custom-class="header-class" value-class="van-panel__header-value" />
        <slot v-else name="header" />

        <view class="van-panel__content">
            <slot />
        </view>

        <view v-if="useFooterSlot" class="van-panel__footer van-hairline--top footer-class">
            <slot name="footer" />
        </view>
    </view>
</template>

<script>
import vanCell from '../cell/index';
import { VantComponent } from '../common/component';
export default {
    data() {
        return {};
    },
    components: {
        vanCell
    },
    classes: ['header-class', 'footer-class'],
    props: {
        desc: String,
        title: String,
        status: String,
        useFooterSlot: Boolean
    }
};
</script>
<style>
@import '../common/index.css';
.van-panel {
    background: #fff;
}
.van-panel__header-value {
    color: #f44;
}
.van-panel__footer {
    padding: 10px 15px;
}
</style>
