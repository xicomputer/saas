<template>
    <script module="utils" lang="wxs" src="../wxs/utils.wxs"></script>
    <script module="getters" lang="wxs" src="./index.wxs"></script>

    <view :class="'custom-class ' + utils.bem('tabs', [type])">
        <van-sticky :disabled="!sticky" :z-index="zIndex" :offset-top="offsetTop" :container="container" @scroll="onTouchScroll">
            <view :class="utils.bem('tabs__wrap', { scrollable }) + ' ' + (type === 'line' && border ? 'van-hairline--top-bottom' : '')">
                <slot name="nav-left" />

                <scroll-view
                    :scroll-x="scrollable"
                    scroll-with-animation
                    :scroll-left="scrollLeft"
                    :class="utils.bem('tabs__scroll', [type])"
                    :style="color ? 'border-color: ' + color : ''"
                >
                    <view :class="utils.bem('tabs__nav', [type, { complete: !ellipsis }]) + ' nav-class'" :style="getters.tabCardTypeBorderStyle(color, type)">
                        <view v-if="type === 'line'" class="van-tabs__line" :style="lineStyle" />
                        <view
                            :data-index="index"
                            :class="
                                getters.tabClass(index === currentIndex, ellipsis) +
                                ' ' +
                                utils.bem('tab', { active: index === currentIndex, disabled: item.disabled, complete: !ellipsis })
                            "
                            :style="
                                getters.tabStyle(index === currentIndex, ellipsis, color, type, item.disabled, titleActiveColor, titleInactiveColor, swipeThreshold, scrollable)
                            "
                            @tap="onTap"
                            v-for="(item, index) in tabs"
                            :key="index"
                        >
                            <view :class="ellipsis ? 'van-ellipsis' : ''" :style="item.titleStyle">
                                {{ item.title }}
                                <van-info v-if="item.info !== null || item.dot" :info="item.info" :dot="item.dot" custom-class="van-tab__title__info" />
                            </view>
                        </view>
                    </view>
                </scroll-view>

                <slot name="nav-right" />
            </view>
        </van-sticky>

        <view class="van-tabs__content" @touchstart="onTouchStart" @touchmove="onTouchMove" @touchend="onTouchEnd" @touchcancel="onTouchEnd">
            <view :class="utils.bem('tabs__track', [{ animated }]) + ' van-tabs__track'" :style="getters.trackStyle({ duration, currentIndex, animated })">
                <slot />
            </view>
        </view>
    </view>
</template>

<script>
'use strict';
import vanInfo from '../info/index';
import vanSticky from '../sticky/index';

Object.defineProperty(exports, '__esModule', {
    value: true
});

var component_1 = require('../common/component');

var touch_1 = require('../mixins/touch');

var utils_1 = require('../common/utils');

component_1.VantComponent({
    mixins: [touch_1.touch],
    classes: ['nav-class', 'tab-class', 'tab-active-class', 'line-class'],
    relation: {
        name: 'tab',
        type: 'descendant',
        current: 'tabs',
        linked: function (target) {
            target.index = this.children.length - 1;
            this.updateTabs();
        },
        unlinked: function () {
            this.children = this.children.map(function (child, index) {
                child.index = index;
                return child;
            });
            this.updateTabs();
        }
    },
    props: {
        sticky: Boolean,
        border: Boolean,
        swipeable: Boolean,
        titleActiveColor: String,
        titleInactiveColor: String,
        color: {
            type: String,
            observer: 'setLine'
        },
        animated: {
            type: Boolean,
            observer: function () {
                var that = this;
                this.children.forEach(function (child, index) {
                    return child.updateRender(index === that.data.currentIndex, that);
                });
            }
        },
        lineWidth: {
            type: [String, Number],
            value: 40,
            observer: 'setLine'
        },
        lineHeight: {
            type: [String, Number],
            value: -1,
            observer: 'setLine'
        },
        active: {
            type: [String, Number],
            value: 0,
            observer: function (name) {
                if (name !== this.getCurrentName()) {
                    this.setCurrentIndexByName(name);
                }
            }
        },
        type: {
            type: String,
            value: 'line'
        },
        ellipsis: {
            type: Boolean,
            value: true
        },
        duration: {
            type: Number,
            value: 0.3
        },
        zIndex: {
            type: Number,
            value: 1
        },
        swipeThreshold: {
            type: Number,
            value: 5,
            observer: function (value) {
                this.setData({
                    scrollable: this.children.length > value || !this.data.ellipsis
                });
            }
        },
        offsetTop: {
            type: Number,
            value: 0
        },
        lazyRender: {
            type: Boolean,
            value: true
        }
    },
    data: {
        tabs: [],
        lineStyle: '',
        scrollLeft: 0,
        scrollable: false,
        trackStyle: '',
        currentIndex: null,
        container: null
    },
    mounted: function () {
        var that = this;
        uni.nextTick(function () {
            that.setLine(true);
            that.scrollIntoView();
        });
    },
    methods: {
        updateContainer: function () {
            var that = this;
            this.setData({
                container: function () {
                    return that.createSelectorQuery().select('.van-tabs');
                }
            });
        },
        updateTabs: function () {
            var that = this;
            var _b = that.children;
            var children = _b === void 0 ? [] : _b;
            var data = that.data;
            this.setData({
                tabs: children.map(function (child) {
                    return child.data;
                }),
                scrollable: this.children.length > data.swipeThreshold || !data.ellipsis
            });
            this.setCurrentIndexByName(this.getCurrentName() || data.active);
        },
        trigger: function (eventName, child) {
            var currentIndex = this.data.currentIndex;
            var currentChild = child || this.children[currentIndex];

            if (!utils_1.isDef(currentChild)) {
                return;
            }

            this.$emit(eventName, {
                index: currentChild.index,
                name: currentChild.getComputedName(),
                title: currentChild.data.title
            });
        },
        onTap: function (event) {
            var that = this;
            var index = event.currentTarget.dataset.index;
            var child = this.children[index];

            if (child.data.disabled) {
                this.trigger('disabled', child);
            } else {
                this.setCurrentIndex(index);
                uni.nextTick(function () {
                    that.trigger('click');
                });
            }
        },
        // correct the index of active tab
        setCurrentIndexByName: function (name) {
            var _a = this.children;
            var children = _a === void 0 ? [] : _a;
            var matched = children.filter(function (child) {
                return child.getComputedName() === name;
            });

            if (matched.length) {
                this.setCurrentIndex(matched[0].index);
            }
        },
        setCurrentIndex: function (currentIndex) {
            var that = this;

            var _a = this;

            var data = _a.data;
            var _b = _a.children;
            var children = _b === void 0 ? [] : _b;
            if (!utils_1.isDef(currentIndex) || currentIndex >= children.length || currentIndex < 0) {
                return;
            }

            children.forEach(function (item, index) {
                var active = index === currentIndex;

                if (active !== item.data.active || !item.inited) {
                    item.updateRender(active, that);
                }
            });

            if (currentIndex === data.currentIndex) {
                return;
            }

            var shouldEmitChange = data.currentIndex !== null;
            this.setData({
                currentIndex: currentIndex
            });
            uni.nextTick(function () {
                that.setLine();
                that.scrollIntoView();
                that.updateContainer();
                that.trigger('input');

                if (shouldEmitChange) {
                    that.trigger('change');
                }
            });
        },
        getCurrentName: function () {
            var activeTab = this.children[this.data.currentIndex];

            if (activeTab) {
                return activeTab.getComputedName();
            }
        },
        setLine: function (skipTransition) {
            var that = this;

            if (this.data.type !== 'line') {
                return;
            }

            var _a = this.data;
            var color = _a.color;
            var duration = _a.duration;
            var currentIndex = _a.currentIndex;
            var lineWidth = _a.lineWidth;
            var lineHeight = _a.lineHeight;
            this.getRect('.van-tab', true).then(function (rects) {
                if (rects === void 0) {
                    rects = [];
                }

                var rect = rects[currentIndex];

                if (rect == null) {
                    return;
                }

                var height = lineHeight !== -1 ? 'height: ' + utils_1.addUnit(lineHeight) + '; border-radius: ' + utils_1.addUnit(lineHeight) + ';' : '';
                var left = rects.slice(0, currentIndex).reduce(function (prev, curr) {
                    return prev + curr.width;
                }, 0);
                left += (rect.width - lineWidth) / 2;
                var transition = skipTransition ? '' : 'transition-duration: ' + duration + 's; -webkit-transition-duration: ' + duration + 's;';
                that.setData({
                    lineStyle:
                        '\n            ' +
                        height +
                        '\n            width: ' +
                        utils_1.addUnit(lineWidth) +
                        ';\n            background-color: ' +
                        color +
                        ';\n            -webkit-transform: translateX(' +
                        left +
                        'px);\n            transform: translateX(' +
                        left +
                        'px);\n            ' +
                        transition +
                        '\n          '
                });
            });
        },
        // scroll active tab into view
        scrollIntoView: function () {
            var that = this;
            var _a = this.data;
            var currentIndex = _a.currentIndex;
            var scrollable = _a.scrollable;
            if (!scrollable) {
                return;
            }

            Promise.all([this.getRect('.van-tab', true), this.getRect('.van-tabs__nav')]).then(function (_a) {
                var tabRects = _a[0];
                var navRect = _a[1];
                var tabRect = tabRects[currentIndex];
                var offsetLeft = tabRects.slice(0, currentIndex).reduce(function (prev, curr) {
                    return prev + curr.width;
                }, 0);
                that.setData({
                    scrollLeft: offsetLeft - (navRect.width - tabRect.width) / 2
                });
            });
        },
        onTouchScroll: function (event) {
            this.$emit('scroll', event.detail);
        },
        onTouchStart: function (event) {
            if (!this.data.swipeable) {
                return;
            }

            this.touchStart(event);
        },
        onTouchMove: function (event) {
            if (!this.data.swipeable) {
                return;
            }

            this.touchMove(event);
        },
        // watch swipe touch end
        onTouchEnd: function () {
            if (!this.data.swipeable) {
                return;
            }

            var that = this;
            var direction = that.direction;
            var deltaX = that.deltaX;
            var offsetX = that.offsetX;
            var minSwipeDistance = 50;

            if (direction === 'horizontal' && offsetX >= minSwipeDistance) {
                var index = this.getAvaiableTab(deltaX);

                if (index !== -1) {
                    this.setCurrentIndex(index);
                }
            }
        },
        getAvaiableTab: function (direction) {
            var _a = this.data;
            var tabs = _a.tabs;
            var currentIndex = _a.currentIndex;
            var step = direction > 0 ? -1 : 1;

            for (var i = step; currentIndex + i < tabs.length && currentIndex + i >= 0; i += step) {
                var index = currentIndex + i;

                if (index >= 0 && index < tabs.length && tabs[index] && !tabs[index].disabled) {
                    return index;
                }
            }

            return -1;
        }
    }
});
</script>
<style>
@import '../common/index.css';
.van-tabs {
    position: relative;
    -webkit-tap-highlight-color: transparent;
}
.van-tabs__wrap {
    display: -webkit-flex;
    display: flex;
    overflow: hidden;
}
.van-tabs__wrap--scrollable .van-tab {
    -webkit-flex: 0 0 22%;
    flex: 0 0 22%;
}
.van-tabs__wrap--scrollable .van-tab--complete {
    -webkit-flex: 1 0 auto !important;
    flex: 1 0 auto !important;
    padding: 0 12px;
}
.van-tabs__wrap--scrollable .van-tabs__nav--complete {
    padding-right: 8px;
    padding-left: 8px;
}
.van-tabs__scroll {
    background-color: #fff;
    background-color: var(--tabs-nav-background-color, #fff);
}
.van-tabs__scroll--line {
    box-sizing: initial;
    height: calc(100% + 15px);
}
.van-tabs__scroll--card {
    margin: 0 16px;
    margin: 0 var(--padding-md, 16px);
}
.van-tabs__scroll::-webkit-scrollbar {
    display: none;
}
.van-tabs__nav {
    position: relative;
    display: -webkit-flex;
    display: flex;
    -webkit-user-select: none;
    user-select: none;
}
.van-tabs__nav--card {
    box-sizing: border-box;
    height: 30px;
    height: var(--tabs-card-height, 30px);
    border: 1px solid #ee0a24;
    border: var(--border-width-base, 1px) solid var(--tabs-default-color, #ee0a24);
    border-radius: 2px;
    border-radius: var(--border-radius-sm, 2px);
}
.van-tabs__nav--card .van-tab {
    color: #ee0a24;
    color: var(--tabs-default-color, #ee0a24);
    line-height: 28px;
    line-height: calc(var(--tabs-card-height, 30px) - 2 * var(--border-width-base, 1px));
    border-right: 1px solid #ee0a24;
    border-right: var(--border-width-base, 1px) solid var(--tabs-default-color, #ee0a24);
}
.van-tabs__nav--card .van-tab:last-child {
    border-right: none;
}
.van-tabs__nav--card .van-tab.van-tab--active {
    color: #fff;
    color: var(--white, #fff);
    background-color: #ee0a24;
    background-color: var(--tabs-default-color, #ee0a24);
}
.van-tabs__nav--card .van-tab--disabled {
    color: #c8c9cc;
    color: var(--tab-disabled-text-color, #c8c9cc);
}
.van-tabs__line {
    position: absolute;
    bottom: 0;
    left: 0;
    z-index: 1;
    height: 3px;
    height: var(--tabs-bottom-bar-height, 3px);
    border-radius: 3px;
    border-radius: var(--tabs-bottom-bar-height, 3px);
    background-color: #ee0a24;
    background-color: var(--tabs-bottom-bar-color, #ee0a24);
}
.van-tabs__track {
    position: relative;
    width: 100%;
    height: 100%;
}
.van-tabs__track--animated {
    display: -webkit-flex;
    display: flex;
    transition-property: -webkit-transform;
    transition-property: transform;
    transition-property: transform, -webkit-transform;
}
.van-tabs__content {
    overflow: hidden;
}
.van-tabs--line .van-tabs__wrap {
    height: 44px;
    height: var(--tabs-line-height, 44px);
}
.van-tabs--card .van-tabs__wrap {
    height: 30px;
    height: var(--tabs-card-height, 30px);
}
.van-tab {
    position: relative;
    -webkit-flex: 1;
    flex: 1;
    box-sizing: border-box;
    min-width: 0;
    padding: 0 5px;
    text-align: center;
    cursor: pointer;
    color: #646566;
    color: var(--tab-text-color, #646566);
    font-size: 14px;
    font-size: var(--tab-font-size, 14px);
    line-height: 44px;
    line-height: var(--tabs-line-height, 44px);
}
.van-tab--active {
    font-weight: 500;
    font-weight: var(--font-weight-bold, 500);
    color: #323233;
    color: var(--tab-active-text-color, #323233);
}
.van-tab--disabled {
    color: #c8c9cc;
    color: var(--tab-disabled-text-color, #c8c9cc);
}
.van-tab__title__info {
    position: relative !important;
    top: -1px !important;
    display: inline-block;
    -webkit-transform: translateX(0) !important;
    transform: translateX(0) !important;
}
</style>
