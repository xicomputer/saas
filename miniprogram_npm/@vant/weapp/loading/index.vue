<template>
    <script module="utils" lang="wxs" src="../wxs/utils.wxs"></script>

    <view :class="'custom-class van-loading ' + (vertical ? 'van-loading--vertical' : '')">
        <view :class="'van-loading__spinner van-loading__spinner--' + type" :style="'color: ' + color + '; width: ' + utils.addUnit(size) + '; height: ' + utils.addUnit(size)">
            <view v-if="type === 'spinner'" class="van-loading__dot" v-for="(item, index) in array12" :key="index"></view>
        </view>
        <view class="van-loading__text" :style="'font-size: ' + utils.addUnit(textSize) + ';'">
            <slot />
        </view>
    </view>
</template>

<script>
'use strict';

Object.defineProperty(exports, '__esModule', {
    value: true
});

var component_1 = require('../common/component');

component_1.VantComponent({
    props: {
        color: String,
        vertical: Boolean,
        type: {
            type: String,
            value: 'circular'
        },
        size: String,
        textSize: String
    },
    data: {
        array12: Array.from({
            length: 12
        })
    }
});
</script>
<style>
@import '../common/index.css';
:host {
    font-size: 0;
    line-height: 1;
}
.van-loading {
    display: -webkit-inline-flex;
    display: inline-flex;
    -webkit-align-items: center;
    align-items: center;
    -webkit-justify-content: center;
    justify-content: center;
    color: #c8c9cc;
    color: var(--loading-spinner-color, #c8c9cc);
}
.van-loading__spinner {
    position: relative;
    box-sizing: border-box;
    width: 30px;
    width: var(--loading-spinner-size, 30px);
    max-width: 100%;
    max-height: 100%;
    height: 30px;
    height: var(--loading-spinner-size, 30px);
    -webkit-animation: van-rotate 0.8s linear infinite;
    animation: van-rotate 0.8s linear infinite;
    -webkit-animation: van-rotate var(--loading-spinner-animation-duration, 0.8s) linear infinite;
    animation: van-rotate var(--loading-spinner-animation-duration, 0.8s) linear infinite;
}
.van-loading__spinner--spinner {
    -webkit-animation-timing-function: steps(12);
    animation-timing-function: steps(12);
}
.van-loading__spinner--circular {
    border: 1px solid transparent;
    border-top-color: initial;
    border-radius: 100%;
}
.van-loading__text {
    margin-left: 8px;
    margin-left: var(--padding-xs, 8px);
    color: #969799;
    color: var(--loading-text-color, #969799);
    font-size: 14px;
    font-size: var(--loading-text-font-size, 14px);
    line-height: 20px;
    line-height: var(--loading-text-line-height, 20px);
}
.van-loading__text:empty {
    display: none;
}
.van-loading--vertical {
    -webkit-flex-direction: column;
    flex-direction: column;
}
.van-loading--vertical .van-loading__text {
    margin: 8px 0 0;
    margin: var(--padding-xs, 8px) 0 0;
}
.van-loading__dot {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
}
.van-loading__dot:before {
    display: block;
    width: 2px;
    height: 25%;
    margin: 0 auto;
    background-color: currentColor;
    border-radius: 40%;
    content: ' ';
}
.van-loading__dot:first-of-type {
    -webkit-transform: rotate(30deg);
    transform: rotate(30deg);
    opacity: 1;
}
.van-loading__dot:nth-of-type(2) {
    -webkit-transform: rotate(60deg);
    transform: rotate(60deg);
    opacity: 0.9375;
}
.van-loading__dot:nth-of-type(3) {
    -webkit-transform: rotate(90deg);
    transform: rotate(90deg);
    opacity: 0.875;
}
.van-loading__dot:nth-of-type(4) {
    -webkit-transform: rotate(120deg);
    transform: rotate(120deg);
    opacity: 0.8125;
}
.van-loading__dot:nth-of-type(5) {
    -webkit-transform: rotate(150deg);
    transform: rotate(150deg);
    opacity: 0.75;
}
.van-loading__dot:nth-of-type(6) {
    -webkit-transform: rotate(180deg);
    transform: rotate(180deg);
    opacity: 0.6875;
}
.van-loading__dot:nth-of-type(7) {
    -webkit-transform: rotate(210deg);
    transform: rotate(210deg);
    opacity: 0.625;
}
.van-loading__dot:nth-of-type(8) {
    -webkit-transform: rotate(240deg);
    transform: rotate(240deg);
    opacity: 0.5625;
}
.van-loading__dot:nth-of-type(9) {
    -webkit-transform: rotate(270deg);
    transform: rotate(270deg);
    opacity: 0.5;
}
.van-loading__dot:nth-of-type(10) {
    -webkit-transform: rotate(300deg);
    transform: rotate(300deg);
    opacity: 0.4375;
}
.van-loading__dot:nth-of-type(11) {
    -webkit-transform: rotate(330deg);
    transform: rotate(330deg);
    opacity: 0.375;
}
.van-loading__dot:nth-of-type(12) {
    -webkit-transform: rotate(1turn);
    transform: rotate(1turn);
    opacity: 0.3125;
}
@-webkit-keyframes van-rotate {
    0% {
        -webkit-transform: rotate(0deg);
        transform: rotate(0deg);
    }
    to {
        -webkit-transform: rotate(1turn);
        transform: rotate(1turn);
    }
}
@keyframes van-rotate {
    0% {
        -webkit-transform: rotate(0deg);
        transform: rotate(0deg);
    }
    to {
        -webkit-transform: rotate(1turn);
        transform: rotate(1turn);
    }
}
</style>
