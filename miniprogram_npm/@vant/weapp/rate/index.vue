<template>
    <script module="utils" lang="wxs" src="../wxs/utils.wxs"></script>

    <view class="van-rate custom-class" @touchmove="onTouchMove">
        <view class="van-rate__item" :style="'padding-right: ' + (index !== count - 1 ? utils.addUnit(gutter) : '')" v-for="(item, index) in innerCountArray" :key="index">
            <van-icon
                :name="index + 1 <= innerValue ? icon : voidIcon"
                class="van-rate__icon"
                :style="'font-size: ' + utils.addUnit(size)"
                custom-class="icon-class"
                :data-score="index"
                :color="disabled ? disabledColor : index + 1 <= innerValue ? color : voidColor"
                @click="onSelect($event, { score: index })"
            />

            <van-icon
                v-if="allowHalf"
                :name="index + 0.5 <= innerValue ? icon : voidIcon"
                :class="utils.bem('rate__icon', ['half'])"
                :style="'font-size: ' + utils.addUnit(size)"
                custom-class="icon-class"
                :data-score="index - 0.5"
                :color="disabled ? disabledColor : index + 0.5 <= innerValue ? color : voidColor"
                @click="onSelect($event, { score: index - 0.5 })"
            />
        </view>
    </view>
</template>

<script>
'use strict';
import vanIcon from '../icon/index';

var __assign =
    (this && this.__assign) ||
    function () {
        __assign =
            Object.assign ||
            function (t) {
                for (var s, i = 1, n = arguments.length; i < n; i++) {
                    s = arguments[i];

                    for (var p in s) {
                        if (Object.prototype.hasOwnProperty.call(s, p)) {
                            t[p] = s[p];
                        }
                    }
                }

                return t;
            };

        return __assign.apply(this, arguments);
    };

Object.defineProperty(exports, '__esModule', {
    value: true
});

var component_1 = require('../common/component');

var version_1 = require('../common/version');

component_1.VantComponent({
    field: true,
    classes: ['icon-class'],
    props: {
        value: {
            type: Number,
            observer: function (value) {
                if (value !== this.data.innerValue) {
                    this.setData({
                        innerValue: value
                    });
                }
            }
        },
        readonly: Boolean,
        disabled: Boolean,
        allowHalf: Boolean,
        size: null,
        icon: {
            type: String,
            value: 'star'
        },
        voidIcon: {
            type: String,
            value: 'star-o'
        },
        color: {
            type: String,
            value: '#ffd21e'
        },
        voidColor: {
            type: String,
            value: '#c7c7c7'
        },
        disabledColor: {
            type: String,
            value: '#bdbdbd'
        },
        count: {
            type: Number,
            value: 5,
            observer: function (value) {
                this.setData({
                    innerCountArray: Array.from({
                        length: value
                    })
                });
            }
        },
        gutter: null,
        touchable: {
            type: Boolean,
            value: true
        }
    },
    data: {
        innerValue: 0,
        innerCountArray: Array.from({
            length: 5
        })
    },
    methods: {
        onSelect: function (event) {
            var that = this;
            var data = this.data;
            var score = event.currentTarget.dataset.score;

            if (!data.disabled && !data.readonly) {
                this.setData({
                    innerValue: score + 1
                });

                if (version_1.canIUseModel()) {
                    this.setData({
                        value: score + 1
                    });
                }

                uni.nextTick(function () {
                    that.$emit('input', score + 1);
                    that.$emit('change', score + 1);
                });
            }
        },
        onTouchMove: function (event) {
            var that = this;
            var touchable = this.data.touchable;

            if (!touchable) {
                return;
            }

            var clientX = event.touches[0].clientX;
            this.getRect('.van-rate__icon', true).then(function (list) {
                var target = list
                    .sort(function (item) {
                        return item.right - item.left;
                    })
                    .find(function (item) {
                        return clientX >= item.left && clientX <= item.right;
                    });

                if (target != null) {
                    that.onSelect(
                        __assign(__assign({}, event), {
                            currentTarget: target
                        })
                    );
                }
            });
        }
    }
});
</script>
<style>
@import '../common/index.css';
.van-rate {
    display: -webkit-inline-flex;
    display: inline-flex;
    -webkit-user-select: none;
    user-select: none;
}
.van-rate__item {
    position: relative;
    padding: 0 2px;
    padding: 0 var(--rate-horizontal-padding, 2px);
}
.van-rate__icon {
    display: block;
    height: 1em;
    font-size: 20px;
    font-size: var(--rate-icon-size, 20px);
}
.van-rate__icon--half {
    position: absolute;
    top: 0;
    width: 0.5em;
    overflow: hidden;
    left: 2px;
    left: var(--rate-horizontal-padding, 2px);
}
</style>
