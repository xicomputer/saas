<template>
    <script module="utils" lang="wxs" src="../wxs/utils.wxs"></script>

    <view
        :class="'custom-class ' + utils.bem('switch', { on: value === activeValue, disabled })"
        :style="'font-size: ' + size + '; ' + ((checked ? activeColor : inactiveColor) ? 'background-color: ' + (checked ? activeColor : inactiveColor) : '')"
        @tap="onClick"
    >
        <view class="van-switch__node node-class">
            <van-loading v-if="loading" :color="loadingColor" custom-class="van-switch__loading" />
        </view>
    </view>
</template>

<script>
'use strict';
import vanLoading from '../loading/index';

Object.defineProperty(exports, '__esModule', {
    value: true
});

var component_1 = require('../common/component');

var color_1 = require('../common/color');

component_1.VantComponent({
    field: true,
    classes: ['node-class'],
    props: {
        checked: {
            type: null,
            observer: function (value) {
                var loadingColor = this.getLoadingColor(value);
                this.setData({
                    value: value,
                    loadingColor: loadingColor
                });
            }
        },
        loading: Boolean,
        disabled: Boolean,
        activeColor: String,
        inactiveColor: String,
        size: {
            type: String,
            value: '30px'
        },
        activeValue: {
            type: null,
            value: true
        },
        inactiveValue: {
            type: null,
            value: false
        }
    },
    created: function () {
        var value = this.data.checked;
        var loadingColor = this.getLoadingColor(value);
        this.setData({
            value: value,
            loadingColor: loadingColor
        });
    },
    methods: {
        getLoadingColor: function (checked) {
            var _a = this.data;
            var activeColor = _a.activeColor;
            var inactiveColor = _a.inactiveColor;
            return checked ? activeColor || color_1.BLUE : inactiveColor || color_1.GRAY_DARK;
        },
        onClick: function () {
            var _a = this.data;
            var activeValue = _a.activeValue;
            var inactiveValue = _a.inactiveValue;
            if (!this.data.disabled && !this.data.loading) {
                var checked = this.data.checked === activeValue;
                var value = checked ? inactiveValue : activeValue;
                this.$emit('input', value);
                this.$emit('change', value);
            }
        }
    }
});
</script>
<style>
@import '../common/index.css';
.van-switch {
    position: relative;
    display: inline-block;
    box-sizing: initial;
    width: 2em;
    width: var(--switch-width, 2em);
    height: 1em;
    height: var(--switch-height, 1em);
    background-color: #fff;
    background-color: var(--switch-background-color, #fff);
    border: 1px solid rgba(0, 0, 0, 0.1);
    border: var(--switch-border, 1px solid rgba(0, 0, 0, 0.1));
    border-radius: 1em;
    border-radius: var(--switch-node-size, 1em);
    transition: background-color 0.3s;
    transition: background-color var(--switch-transition-duration, 0.3s);
}
.van-switch__node {
    position: absolute;
    top: 0;
    left: 0;
    border-radius: 100%;
    z-index: 1;
    z-index: var(--switch-node-z-index, 1);
    width: 1em;
    width: var(--switch-node-size, 1em);
    height: 1em;
    height: var(--switch-node-size, 1em);
    background-color: #fff;
    background-color: var(--switch-node-background-color, #fff);
    box-shadow: 0 3px 1px 0 rgba(0, 0, 0, 0.05), 0 2px 2px 0 rgba(0, 0, 0, 0.1), 0 3px 3px 0 rgba(0, 0, 0, 0.05);
    box-shadow: var(--switch-node-box-shadow, 0 3px 1px 0 rgba(0, 0, 0, 0.05), 0 2px 2px 0 rgba(0, 0, 0, 0.1), 0 3px 3px 0 rgba(0, 0, 0, 0.05));
    transition: -webkit-transform 0.3s cubic-bezier(0.3, 1.05, 0.4, 1.05);
    transition: transform 0.3s cubic-bezier(0.3, 1.05, 0.4, 1.05);
    transition: transform 0.3s cubic-bezier(0.3, 1.05, 0.4, 1.05), -webkit-transform 0.3s cubic-bezier(0.3, 1.05, 0.4, 1.05);
    transition: -webkit-transform var(--switch-transition-duration, 0.3s) cubic-bezier(0.3, 1.05, 0.4, 1.05);
    transition: transform var(--switch-transition-duration, 0.3s) cubic-bezier(0.3, 1.05, 0.4, 1.05);
    transition: transform var(--switch-transition-duration, 0.3s) cubic-bezier(0.3, 1.05, 0.4, 1.05),
        -webkit-transform var(--switch-transition-duration, 0.3s) cubic-bezier(0.3, 1.05, 0.4, 1.05);
}
.van-switch__loading {
    position: absolute !important;
    top: 25%;
    left: 25%;
    width: 50%;
    height: 50%;
}
.van-switch--on {
    background-color: #1989fa;
    background-color: var(--switch-on-background-color, #1989fa);
}
.van-switch--on .van-switch__node {
    -webkit-transform: translateX(1em);
    transform: translateX(1em);
    -webkit-transform: translateX(calc(var(--switch-width, 2em) - var(--switch-node-size, 1em)));
    transform: translateX(calc(var(--switch-width, 2em) - var(--switch-node-size, 1em)));
}
.van-switch--disabled {
    opacity: 0.4;
    opacity: var(--switch-disabled-opacity, 0.4);
}
</style>
