<template>
    <script module="utils" lang="wxs" src="../wxs/utils.wxs"></script>

    <view
        :class="utils.bem('nav-bar', { fixed }) + ' custom-class ' + (border ? 'van-hairline--bottom' : '')"
        :style="'z-index: ' + zIndex + '; ' + (safeAreaInsetTop ? 'padding-top: ' + statusBarHeight + 'px;' : '')"
    >
        <view class="van-nav-bar__left" @tap="onClickLeft">
            <block v-if="leftArrow || leftText">
                <van-icon v-if="leftArrow" size="16px" name="arrow-left" custom-class="van-nav-bar__arrow" />
                <view v-if="leftText" class="van-nav-bar__text" hover-class="van-nav-bar__text--hover" hover-stay-time="70">{{ leftText }}</view>
            </block>
            <slot v-else name="left" />
        </view>
        <view class="van-nav-bar__title title-class van-ellipsis">
            <block v-if="title">{{ title }}</block>
            <slot v-else name="title" />
        </view>
        <view class="van-nav-bar__right" @tap="onClickRight">
            <view v-if="rightText" class="van-nav-bar__text" hover-class="van-nav-bar__text--hover" hover-stay-time="70">{{ rightText }}</view>
            <slot v-else name="right" />
        </view>
    </view>
</template>

<script>
import vanIcon from '../icon/index';
import { VantComponent } from '../common/component';
import { safeArea } from '../mixins/safe-area';
export default {
    data() {
        return {
            safeAreaInsetTop: false,
            statusBarHeight: ''
        };
    },
    components: {
        vanIcon
    },
    mixins: [
        safeArea({
            safeAreaInsetTop: true
        })
    ],
    classes: ['title-class'],
    props: {
        title: String,
        fixed: Boolean,
        leftText: String,
        rightText: String,
        leftArrow: Boolean,
        border: {
            type: Boolean,
            default: true
        },
        zIndex: {
            type: Number,
            default: 120
        }
    },
    methods: {
        onClickLeft() {
            this.$emit('click-left');
        },

        onClickRight() {
            this.$emit('click-right');
        }
    }
};
</script>
<style>
@import '../common/index.css';
.van-nav-bar {
    position: relative;
    height: 44px;
    line-height: 44px;
    text-align: center;
    background-color: #fff;
    -webkit-user-select: none;
    user-select: none;
}
.van-nav-bar__text {
    display: inline-block;
    padding: 0 15px;
    margin: 0 -15px;
    color: #1989fa;
    vertical-align: middle;
}
.van-nav-bar__text--hover {
    background-color: #f2f3f5;
}
.van-nav-bar__arrow {
    color: #1989fa;
    vertical-align: middle;
}
.van-nav-bar__arrow + .van-nav-bar__text {
    padding-left: 25px;
    margin-left: -20px;
}
.van-nav-bar--fixed {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
}
.van-nav-bar__title {
    max-width: 60%;
    margin: 0 auto;
    font-size: 16px;
    font-weight: 500;
}
.van-nav-bar__left,
.van-nav-bar__right {
    position: absolute;
    bottom: 0;
    font-size: 14px;
}
.van-nav-bar__left {
    left: 15px;
}
.van-nav-bar__right {
    right: 15px;
}
</style>
