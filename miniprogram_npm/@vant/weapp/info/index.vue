<template>
    <script module="utils" lang="wxs" src="../wxs/utils.wxs"></script>

    <view v-if="(info !== null && info !== '') || dot" :class="'custom-class van-info ' + utils.bem('info', { dot })" :style="customStyle">{{ dot ? '' : info }}</view>
</template>

<script>
'use strict';

Object.defineProperty(exports, '__esModule', {
    value: true
});

var component_1 = require('../common/component');

component_1.VantComponent({
    props: {
        dot: Boolean,
        info: null,
        customStyle: String
    }
});
</script>
<style>
@import '../common/index.css';
.van-info {
    position: absolute;
    top: 0;
    right: 0;
    box-sizing: border-box;
    line-height: 1.2;
    white-space: nowrap;
    text-align: center;
    -webkit-transform: translate(50%, -50%);
    transform: translate(50%, -50%);
    -webkit-transform-origin: 100%;
    transform-origin: 100%;
    min-width: 16px;
    min-width: var(--info-size, 16px);
    padding: 0 3px;
    padding: var(--info-padding, 0 3px);
    color: #fff;
    color: var(--info-color, #fff);
    font-weight: 500;
    font-weight: var(--info-font-weight, 500);
    font-size: 12px;
    font-size: var(--info-font-size, 12px);
    font-family: -apple-system-font, Helvetica Neue, Arial, sans-serif;
    font-family: var(--info-font-family, -apple-system-font, Helvetica Neue, Arial, sans-serif);
    background-color: #ee0a24;
    background-color: var(--info-background-color, #ee0a24);
    border: 1px solid #fff;
    border: var(--info-border-width, 1px) solid var(--white, #fff);
    border-radius: 16px;
    border-radius: var(--info-size, 16px);
}
.van-info--dot {
    min-width: 0;
    border-radius: 100%;
    width: 8px;
    width: var(--info-dot-size, 8px);
    height: 8px;
    height: var(--info-dot-size, 8px);
    background-color: #ee0a24;
    background-color: var(--info-dot-color, #ee0a24);
}
</style>
