<template>
    <view class="custom-class van-row" :style="viewStyle">
        <slot />
    </view>
</template>

<script>
'use strict';

Object.defineProperty(exports, '__esModule', {
    value: true
});

var component_1 = require('../common/component');

component_1.VantComponent({
    relation: {
        name: 'col',
        type: 'descendant',
        current: 'row',
        linked: function (target) {
            if (this.data.gutter) {
                target.setGutter(this.data.gutter);
            }
        }
    },
    props: {
        gutter: {
            type: Number,
            observer: 'setGutter'
        }
    },
    data: {
        viewStyle: ''
    },
    mounted: function () {
        if (this.data.gutter) {
            this.setGutter();
        }
    },
    methods: {
        setGutter: function () {
            var that = this;
            var gutter = this.data.gutter;
            var margin = '-' + Number(gutter) / 2 + 'px';
            var viewStyle = gutter ? 'margin-right: ' + margin + '; margin-left: ' + margin + ';' : '';
            this.setData({
                viewStyle: viewStyle
            });
            this.getRelationNodes('../col/index').forEach(function (col) {
                col.setGutter(that.data.gutter);
            });
        }
    }
});
</script>
<style>
@import '../common/index.css';
.van-row:after {
    display: table;
    clear: both;
    content: '';
}
</style>
