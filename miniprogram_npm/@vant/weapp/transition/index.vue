<template>
    <view
        v-if="inited"
        :class="'van-transition custom-class ' + classes"
        :style="'-webkit-transition-duration:' + currentDuration + 'ms; transition-duration:' + currentDuration + 'ms; ' + (display ? '' : 'display: none;') + ' ' + customStyle"
        @transitionend="onTransitionEnd"
    >
        <slot />
    </view>
</template>

<script>
'use strict';

Object.defineProperty(exports, '__esModule', {
    value: true
});

var component_1 = require('../common/component');

var transition_1 = require('../mixins/transition');

component_1.VantComponent({
    classes: ['enter-class', 'enter-active-class', 'enter-to-class', 'leave-class', 'leave-active-class', 'leave-to-class'],
    mixins: [transition_1.transition(true)]
});
</script>
<style>
@import '../common/index.css';
.van-transition {
    transition-timing-function: ease;
}
.van-fade-enter-active,
.van-fade-leave-active {
    transition-property: opacity;
}
.van-fade-enter,
.van-fade-leave-to {
    opacity: 0;
}
.van-fade-down-enter-active,
.van-fade-down-leave-active,
.van-fade-left-enter-active,
.van-fade-left-leave-active,
.van-fade-right-enter-active,
.van-fade-right-leave-active,
.van-fade-up-enter-active,
.van-fade-up-leave-active {
    transition-property: opacity, -webkit-transform;
    transition-property: opacity, transform;
    transition-property: opacity, transform, -webkit-transform;
}
.van-fade-up-enter,
.van-fade-up-leave-to {
    -webkit-transform: translate3d(0, 100%, 0);
    transform: translate3d(0, 100%, 0);
    opacity: 0;
}
.van-fade-down-enter,
.van-fade-down-leave-to {
    -webkit-transform: translate3d(0, -100%, 0);
    transform: translate3d(0, -100%, 0);
    opacity: 0;
}
.van-fade-left-enter,
.van-fade-left-leave-to {
    -webkit-transform: translate3d(-100%, 0, 0);
    transform: translate3d(-100%, 0, 0);
    opacity: 0;
}
.van-fade-right-enter,
.van-fade-right-leave-to {
    -webkit-transform: translate3d(100%, 0, 0);
    transform: translate3d(100%, 0, 0);
    opacity: 0;
}
.van-slide-down-enter-active,
.van-slide-down-leave-active,
.van-slide-left-enter-active,
.van-slide-left-leave-active,
.van-slide-right-enter-active,
.van-slide-right-leave-active,
.van-slide-up-enter-active,
.van-slide-up-leave-active {
    transition-property: -webkit-transform;
    transition-property: transform;
    transition-property: transform, -webkit-transform;
}
.van-slide-up-enter,
.van-slide-up-leave-to {
    -webkit-transform: translate3d(0, 100%, 0);
    transform: translate3d(0, 100%, 0);
}
.van-slide-down-enter,
.van-slide-down-leave-to {
    -webkit-transform: translate3d(0, -100%, 0);
    transform: translate3d(0, -100%, 0);
}
.van-slide-left-enter,
.van-slide-left-leave-to {
    -webkit-transform: translate3d(-100%, 0, 0);
    transform: translate3d(-100%, 0, 0);
}
.van-slide-right-enter,
.van-slide-right-leave-to {
    -webkit-transform: translate3d(100%, 0, 0);
    transform: translate3d(100%, 0, 0);
}
</style>
