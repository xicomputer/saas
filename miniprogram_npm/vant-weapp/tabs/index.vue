<template>
    <script module="utils" lang="wxs" src="../wxs/utils.wxs"></script>

    <view :class="'custom-class ' + utils.bem('tabs', [type])">
        <view
            :style="'z-index: ' + zIndex + '; ' + wrapStyle"
            :class="utils.bem('tabs__wrap', { scrollable }) + ' ' + (type === 'line' && border ? 'van-hairline--top-bottom' : '')"
        >
            <slot name="nav-left" />

            <scroll-view :scroll-x="scrollable" scroll-with-animation :scroll-left="scrollLeft" :class="'van-tabs__scroll--' + type" :style="color ? 'border-color: ' + color : ''">
                <view :class="utils.bem('tabs__nav', [type]) + ' nav-class'">
                    <view v-if="type === 'line'" class="van-tabs__line" :style="lineStyle" />
                    <view
                        :data-index="index"
                        :class="
                            'van-ellipsis tab-class ' + (index === active ? 'tab-active-class' : '') + ' ' + utils.bem('tab', { active: index === active, disabled: item.disabled })
                        "
                        :style="
                            (color && index !== active && type === 'card' && !item.disabled ? 'color: ' + color : '') +
                            ' ' +
                            (color && index === active && type === 'card' ? ';background-color:' + color : '') +
                            ' ' +
                            (color ? ';border-color: ' + color : '') +
                            ' ' +
                            (scrollable ? ';flex-basis:' + 88 / swipeThreshold + '%' : '')
                        "
                        @tap="onTap"
                        v-for="(item, index) in tabs"
                        :key="index"
                    >
                        <view :class="'van-ellipsis ' + utils.bem('tab__title', { dot: item.dot })" :style="item.titleStyle">
                            {{ item.title }}
                            <van-info v-if="item.info !== null" :info="item.info" custom-class="van-tab__title__info" />
                        </view>
                    </view>
                </view>
            </scroll-view>

            <slot name="nav-right" />
        </view>
        <view class="van-tabs__content" @touchstart="onTouchStart" @touchmove="onTouchMove" @touchend="onTouchEnd" @touchcancel="onTouchEnd">
            <view class="van-tabs__track" :style="trackStyle">
                <slot />
            </view>
        </view>
    </view>
</template>

<script>
import vanInfo from '../info/index';
import { VantComponent } from '../common/component';
import { touch } from '../mixins/touch';
import { nextTick } from '../common/utils';
export default {
    components: {
        vanInfo
    },
    data() {
        return {
            tabs: [],
            lineStyle: '',
            scrollLeft: 0,
            scrollable: false,
            trackStyle: '',
            wrapStyle: '',
            position: ''
        };
    },
    mixins: [touch],
    classes: ['nav-class', 'tab-class', 'tab-active-class', 'line-class'],
    relation: {
        name: 'tab',
        type: 'descendant',

        linked(child) {
            this.child.push(child);
            this.updateTabs(this.tabs.concat(child.data));
        },

        unlinked(child) {
            const index = this.child.indexOf(child);
            const { tabs } = this;
            tabs.splice(index, 1);
            this.child.splice(index, 1);
            this.updateTabs(tabs);
        }
    },
    props: {
        color: String,
        sticky: Boolean,
        animated: Boolean,
        swipeable: Boolean,
        lineWidth: {
            type: Number,
            default: -1
        },
        lineHeight: {
            type: Number,
            default: -1
        },
        active: {
            type: Number,
            default: 0
        },
        type: {
            type: String,
            default: 'line'
        },
        border: {
            type: Boolean,
            default: true
        },
        duration: {
            type: Number,
            default: 0.3
        },
        zIndex: {
            type: Number,
            default: 1
        },
        swipeThreshold: {
            type: Number,
            default: 4
        },
        offsetTop: {
            type: Number,
            default: 0
        }
    },
    watch: {
        swipeThreshold: {
            handler: function () {
                this.set({
                    scrollable: this.child.length > this.swipeThreshold
                });
            },

            immediate: true,
            deep: true
        },

        color: {
            handler: 'setLine',
            immediate: true,
            deep: true
        },

        lineWidth: {
            handler: 'setLine',
            immediate: true,
            deep: true
        },

        lineHeight: {
            handler: 'setLine',
            immediate: true,
            deep: true
        },

        active: {
            handler: 'setActiveTab',
            immediate: true,
            deep: true
        },

        animated: {
            handler: 'setTrack',
            immediate: true,
            deep: true
        },

        offsetTop: {
            handler: 'setWrapStyle',
            immediate: true,
            deep: true
        }
    },
    beforeCreate() {
        this.child = [];
    },
    mounted() {
        this.setLine(true);
        this.setTrack();
        this.scrollIntoView();
        this.getRect('.van-tabs__wrap').then((rect) => {
            this.navHeight = rect.height;
            this.observerContentScroll();
        });
    },
    destroyed() {
        // @ts-ignore
        this.createIntersectionObserver().disconnect();
    },
    methods: {
        updateTabs(tabs) {
            tabs = tabs || this.tabs;
            this.set({
                tabs,
                scrollable: tabs.length > this.swipeThreshold
            });
            this.setActiveTab();
        },

        trigger(eventName, index) {
            this.$emit(eventName, {
                index,
                title: this.tabs[index].title
            });
        },

        onTap(event) {
            const { index } = event.currentTarget.dataset;

            if (this.tabs[index].disabled) {
                this.trigger('disabled', index);
            } else {
                this.trigger('click', index);
                this.setActive(index);
            }
        },

        setActive(active) {
            if (active !== this.active) {
                this.trigger('change', active);
                this.set({
                    active
                });
                this.setActiveTab();
            }
        },

        setLine(skipTransition) {
            if (this.type !== 'line') {
                return;
            }

            const { color, active, duration, lineWidth, lineHeight } = this;
            this.getRect('.van-tab', true).then((rects) => {
                const rect = rects[active];
                const width = lineWidth !== -1 ? lineWidth : rect.width / 2;
                const height = lineHeight !== -1 ? `height: ${lineHeight}px;` : '';
                let left = rects.slice(0, active).reduce((prev, curr) => prev + curr.width, 0);
                left += (rect.width - width) / 2;
                const transition = skipTransition ? '' : `transition-duration: ${duration}s; -webkit-transition-duration: ${duration}s;`;
                this.set({
                    lineStyle: `
          ${height}
          width: ${width}px;
          background-color: ${color};
          -webkit-transform: translateX(${left}px);
          transform: translateX(${left}px);
          ${transition}
        `
                });
            });
        },

        setTrack() {
            const { animated, active, duration } = this;

            if (!animated) {
                return '';
            }

            this.getRect('.van-tabs__content').then((rect) => {
                const { width } = rect;
                this.set({
                    trackStyle: `
          width: ${width * this.child.length}px;
          left: ${-1 * active * width}px;
          transition: left ${duration}s;
          display: -webkit-box;
          display: flex;
        `
                });
                const props = {
                    width,
                    animated
                };
                this.child.forEach((item) => {
                    item.set(props);
                });
            });
        },

        setActiveTab() {
            this.child.forEach((item, index) => {
                const data = {
                    active: index === this.active
                };

                if (data.active) {
                    data.inited = true;
                }

                if (data.active !== item.data.active) {
                    item.set(data);
                }
            });
            nextTick(() => {
                this.setLine();
                this.setTrack();
                this.scrollIntoView();
            });
        },

        // scroll active tab into view
        scrollIntoView() {
            const { active, scrollable } = this;

            if (!scrollable) {
                return;
            }

            Promise.all([this.getRect('.van-tab', true), this.getRect('.van-tabs__nav')]).then(([tabRects, navRect]) => {
                const tabRect = tabRects[active];
                const offsetLeft = tabRects.slice(0, active).reduce((prev, curr) => prev + curr.width, 0);
                this.set({
                    scrollLeft: offsetLeft - (navRect.width - tabRect.width) / 2
                });
            });
        },

        onTouchStart(event) {
            if (!this.swipeable) {
                return;
            }

            this.touchStart(event);
        },

        onTouchMove(event) {
            if (!this.swipeable) {
                return;
            }

            this.touchMove(event);
        },

        // watch swipe touch end
        onTouchEnd() {
            if (!this.swipeable) {
                return;
            }

            const { active, tabs } = this;
            const { direction, deltaX, offsetX } = this;
            const minSwipeDistance = 50;

            if (direction === 'horizontal' && offsetX >= minSwipeDistance) {
                if (deltaX > 0 && active !== 0) {
                    this.setActive(active - 1);
                } else if (deltaX < 0 && active !== tabs.length - 1) {
                    this.setActive(active + 1);
                }
            }
        },

        setWrapStyle() {
            const { offsetTop, position } = this;
            let wrapStyle;

            switch (position) {
                case 'top':
                    wrapStyle = `
          top: ${offsetTop}px;
          position: fixed;
        `;
                    break;

                case 'bottom':
                    wrapStyle = `
          top: auto;
          bottom: 0;
        `;
                    break;

                default:
                    wrapStyle = '';
            } // cut down `set`

            if (wrapStyle === this.wrapStyle) {
                return;
            }

            this.set({
                wrapStyle
            });
        },

        observerContentScroll() {
            if (!this.sticky) {
                return;
            }

            const { offsetTop } = this;
            const { windowHeight } = uni.getSystemInfoSync(); // @ts-ignore

            this.createIntersectionObserver().disconnect(); // @ts-ignore

            this.createIntersectionObserver()
                .relativeToViewport({
                    top: -(this.navHeight + offsetTop)
                })
                .observe('.van-tabs', (res) => {
                    const { top } = res.boundingClientRect;

                    if (top > offsetTop) {
                        return;
                    }

                    const position = res.intersectionRatio > 0 ? 'top' : 'bottom';
                    this.$emit('scroll', {
                        scrollTop: top + offsetTop,
                        isFixed: position === 'top'
                    });
                    this.setPosition(position);
                }); // @ts-ignore

            this.createIntersectionObserver()
                .relativeToViewport({
                    bottom: -(windowHeight - 1 - offsetTop)
                })
                .observe('.van-tabs', (res) => {
                    const { top, bottom } = res.boundingClientRect;

                    if (bottom < this.navHeight) {
                        return;
                    }

                    const position = res.intersectionRatio > 0 ? 'top' : '';
                    this.$emit('scroll', {
                        scrollTop: top + offsetTop,
                        isFixed: position === 'top'
                    });
                    this.setPosition(position);
                });
        },

        setPosition(position) {
            if (position !== this.position) {
                this.set({
                    position
                }).then(() => {
                    this.setWrapStyle();
                });
            }
        }
    }
};
</script>
<style>
@import '../common/index.css';
.van-tabs {
    position: relative;
    -webkit-tap-highlight-color: transparent;
}
.van-tabs__wrap {
    position: absolute;
    top: 0;
    right: 0;
    left: 0;
    display: -webkit-flex;
    display: flex;
    background-color: #fff;
}
.van-tabs__wrap--page-top {
    position: fixed;
}
.van-tabs__wrap--content-bottom {
    top: auto;
    bottom: 0;
}
.van-tabs__wrap--scrollable .van-tab {
    -webkit-flex: 0 0 22%;
    flex: 0 0 22%;
}
.van-tabs__scroll--card {
    border: 1px solid #f44;
    border-radius: 2px;
}
.van-tabs__nav {
    position: relative;
    display: -webkit-flex;
    display: flex;
    -webkit-user-select: none;
    user-select: none;
}
.van-tabs__nav--line {
    height: 100%;
}
.van-tabs__nav--card {
    height: 30px;
}
.van-tabs__nav--card .van-tab {
    line-height: 30px;
    color: #f44;
    border-right: 1px solid #f44;
}
.van-tabs__nav--card .van-tab:last-child {
    border-right: none;
}
.van-tabs__nav--card .van-tab.van-tab--active {
    color: #fff;
    background-color: #f44;
}
.van-tabs__line {
    position: absolute;
    bottom: 0;
    left: 0;
    z-index: 1;
    height: 3px;
    background-color: #f44;
    border-radius: 3px;
}
.van-tabs--line {
    padding-top: 44px;
}
.van-tabs--line .van-tabs__wrap {
    height: 44px;
}
.van-tabs--card {
    padding-top: 30px;
    margin: 0 15px;
}
.van-tabs--card .van-tabs__wrap {
    height: 30px;
}
.van-tabs__content {
    overflow: hidden;
}
.van-tab,
.van-tabs__track {
    position: relative;
}
.van-tab {
    min-width: 0;
    padding: 0 5px;
    font-size: 14px;
    line-height: 44px;
    color: #7d7e80;
    text-align: center;
    cursor: pointer;
    box-sizing: border-box;
    -webkit-flex: 1;
    flex: 1;
}
.van-tab--active {
    font-weight: 500;
    color: #333;
}
.van-tab--disabled {
    color: #c9c9c9;
}
.van-tab__title--dot:after {
    display: inline-block;
    width: 8px;
    height: 8px;
    vertical-align: middle;
    background-color: #f44;
    border-radius: 100%;
    content: '';
}
.van-tab__title__info {
    position: relative !important;
    top: -1px !important;
    display: inline-block;
    -webkit-transform: translateX(0) !important;
    transform: translateX(0) !important;
}
</style>
