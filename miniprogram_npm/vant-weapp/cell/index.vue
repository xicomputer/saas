<template>
    <script module="utils" lang="wxs" src="../wxs/utils.wxs"></script>

    <view
        :class="'custom-class ' + utils.bem('cell', [size, { center, required, borderless: !border, clickable: isLink || clickable }])"
        hover-class="van-cell--hover hover-class"
        hover-stay-time="70"
        :style="customStyle"
        @tap="onClick"
    >
        <van-icon v-if="icon" :name="icon" class="van-cell__left-icon-wrap" custom-class="van-cell__left-icon" />
        <slot v-else name="icon" />

        <view :style="titleWidth ? 'max-width:' + titleWidth + ';min-width:' + titleWidth : ''" class="van-cell__title title-class">
            <block v-if="title">{{ title }}</block>
            <slot v-else name="title" />

            <view v-if="label || useLabelSlot" class="van-cell__label label-class">
                <slot v-if="useLabelSlot" name="label" />
                <block v-else-if="label">{{ label }}</block>
            </view>
        </view>

        <view class="van-cell__value value-class">
            <block v-if="value || value === 0">{{ value }}</block>
            <slot v-else />
        </view>

        <van-icon
            v-if="isLink"
            :name="arrowDirection ? 'arrow' + '-' + arrowDirection : 'arrow'"
            class="van-cell__right-icon-wrap right-icon-class"
            custom-class="van-cell__right-icon"
        />
        <slot v-else name="right-icon" />

        <slot name="extra" />
    </view>
</template>

<script>
import vanIcon from '../icon/index';
import { link } from '../mixins/link';
import { VantComponent } from '../common/component';
export default {
    data() {
        return {};
    },
    components: {
        vanIcon
    },
    classes: ['title-class', 'label-class', 'value-class', 'right-icon-class', 'hover-class'],
    mixins: [link],
    props: {
        title: null,
        value: null,
        icon: String,
        size: String,
        label: String,
        center: Boolean,
        isLink: Boolean,
        required: Boolean,
        clickable: Boolean,
        titleWidth: String,
        customStyle: String,
        arrowDirection: String,
        useLabelSlot: Boolean,
        border: {
            type: Boolean,
            default: true
        }
    },
    methods: {
        onClick(event) {
            this.$emit('click', event.detail);
            this.jumpLink();
        }
    }
};
</script>
<style>
@import '../common/index.css';
.van-cell {
    position: relative;
    display: -webkit-flex;
    display: flex;
    width: 100%;
    padding: 10px 15px;
    font-size: 14px;
    line-height: 24px;
    color: #333;
    background-color: #fff;
    box-sizing: border-box;
}
.van-cell:after {
    content: ' ';
    position: absolute;
    pointer-events: none;
    box-sizing: border-box;
    -webkit-transform-origin: center;
    transform-origin: center;
    top: auto;
    left: 15px;
    right: 0;
    bottom: 0;
    -webkit-transform: scaleY(0.5);
    transform: scaleY(0.5);
    border-bottom: 1px solid #eee;
}
.van-cell--borderless:after {
    display: none;
}
.van-cell-group {
    background-color: #fff;
}
.van-cell__label {
    margin-top: 3px;
    font-size: 12px;
    line-height: 18px;
    color: #999;
}
.van-cell__value {
    overflow: hidden;
    color: #999;
    text-align: right;
    vertical-align: middle;
}
.van-cell__title,
.van-cell__value {
    -webkit-flex: 1;
    flex: 1;
}
.van-cell__title:empty,
.van-cell__value:empty {
    display: none;
}
.van-cell__left-icon-wrap,
.van-cell__right-icon-wrap {
    display: -webkit-flex;
    display: flex;
    height: 24px;
    font-size: 16px;
    -webkit-align-items: center;
    align-items: center;
}
.van-cell__left-icon-wrap {
    margin-right: 5px;
}
.van-cell__right-icon-wrap {
    margin-left: 5px;
    color: #999;
}
.van-cell__left-icon {
    line-height: 24px;
    vertical-align: middle;
}
.van-cell__right-icon {
    line-height: 24px;
}
.van-cell--clickable.van-cell--hover {
    background-color: #f2f3f5;
}
.van-cell--required {
    overflow: visible;
}
.van-cell--required:before {
    position: absolute;
    left: 7px;
    font-size: 14px;
    color: #f44;
    content: '*';
}
.van-cell--center {
    -webkit-align-items: center;
    align-items: center;
}
.van-cell--large {
    padding-top: 12px;
    padding-bottom: 12px;
}
.van-cell--large .van-cell__title {
    font-size: 16px;
}
.van-cell--large .van-cell__label {
    font-size: 14px;
}
</style>
