<template>
    <view>
        <script module="utils" lang="wxs" src="../wxs/utils.wxs"></script>

        <view v-if="fixed && placeholder" :style="'height: ' + height + 'px;'" />

        <view :class="utils.bem('nav-bar', { fixed }) + ' custom-class ' + (border ? 'van-hairline--bottom' : '')" :style="baseStyle + '; ' + customStyle">
            <view class="van-nav-bar__content">
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
        </view>
    </view>
</template>

<script>
'use strict';
import vanIcon from '../icon/index';

Object.defineProperty(exports, '__esModule', {
    value: true
});

var component_1 = require('../common/component');

var utils_1 = require('../common/utils');

component_1.VantComponent({
    classes: ['title-class'],
    props: {
        title: String,
        fixed: {
            type: Boolean,
            observer: 'setHeight'
        },
        placeholder: {
            type: Boolean,
            observer: 'setHeight'
        },
        leftText: String,
        rightText: String,
        customStyle: String,
        leftArrow: Boolean,
        border: {
            type: Boolean,
            value: true
        },
        zIndex: {
            type: Number,
            value: 1
        },
        safeAreaInsetTop: {
            type: Boolean,
            value: true
        }
    },
    data: {
        statusBarHeight: 0,
        height: 44,
        baseStyle: ''
    },
    created: function () {
        var statusBarHeight = utils_1.getSystemInfoSync().statusBarHeight;
        var _a = this.data;
        var safeAreaInsetTop = _a.safeAreaInsetTop;
        var zIndex = _a.zIndex;
        var paddingTop = safeAreaInsetTop ? statusBarHeight : 0;
        var baseStyle = 'z-index: ' + zIndex + ';padding-top: ' + paddingTop + 'px;';
        this.setData({
            statusBarHeight: statusBarHeight,
            height: 44 + statusBarHeight,
            baseStyle: baseStyle
        });
    },
    mounted: function () {
        this.setHeight();
    },
    methods: {
        onClickLeft: function () {
            this.$emit('click-left');
        },
        onClickRight: function () {
            this.$emit('click-right');
        },
        setHeight: function () {
            var that = this;

            if (!this.data.fixed || !this.data.placeholder) {
                return;
            }

            uni.nextTick(function () {
                that.getRect('.van-nav-bar').then(function (res) {
                    that.setData({
                        height: res.height
                    });
                });
            });
        }
    }
});
</script>
<style>
@import '../common/index.css';
.van-nav-bar {
    text-align: center;
    -webkit-user-select: none;
    user-select: none;
    height: 44px;
    height: var(--nav-bar-height, 44px);
    line-height: 44px;
    line-height: var(--nav-bar-height, 44px);
    background-color: #fff;
    background-color: var(--nav-bar-background-color, #fff);
}
.van-nav-bar,
.van-nav-bar__content {
    position: relative;
}
.van-nav-bar__text {
    display: inline-block;
    vertical-align: middle;
    margin: 0 -16px;
    margin: 0 -var(--padding-md, 16px);
    padding: 0 16px;
    padding: 0 var(--padding-md, 16px);
    color: #1989fa;
    color: var(--nav-bar-text-color, #1989fa);
}
.van-nav-bar__text--hover {
    background-color: #f2f3f5;
    background-color: var(--active-color, #f2f3f5);
}
.van-nav-bar__arrow {
    vertical-align: middle;
    font-size: 16px;
    font-size: var(--nav-bar-arrow-size, 16px);
    color: #1989fa;
    color: var(--nav-bar-text-color, #1989fa);
}
.van-nav-bar__arrow + .van-nav-bar__text {
    margin-left: -20px;
    padding-left: 25px;
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
    color: #323233;
    color: var(--nav-bar-title-text-color, #323233);
    font-weight: 500;
    font-weight: var(--font-weight-bold, 500);
    font-size: 16px;
    font-size: var(--nav-bar-title-font-size, 16px);
}
.van-nav-bar__left,
.van-nav-bar__right {
    position: absolute;
    top: 0;
    bottom: 0;
    display: -webkit-flex;
    display: flex;
    -webkit-align-items: center;
    align-items: center;
    font-size: 14px;
    font-size: var(--font-size-md, 14px);
}
.van-nav-bar__left {
    left: 16px;
    left: var(--padding-md, 16px);
}
.van-nav-bar__right {
    right: 16px;
    right: var(--padding-md, 16px);
}
</style>
