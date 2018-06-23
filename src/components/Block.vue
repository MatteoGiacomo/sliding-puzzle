<template>

    <div
        :style="styleBlock"
        :class="$style.box">
            <span :class="$style.indexBox">{{ index }}</span>
    </div>

</template>

<script>

    export default {
        name: 'Block',
        props: {
            styleBlock: {
                type: Object,
                required: true
            },
            index: {
                type: Number,
                default: undefined
            }
        }
    }

</script>

<style lan="scss" module>

    .box {
        box-sizing: border-box;
        border: 1px solid #666;
        background-image: url('../assets/slide_image.jpg');
        background-repeat: no-repeat;
        color: white;
        font-family: Arial;
        padding: 3px;
        font-size: 15px;
    }
    .indexBox {
        color: white;
        mix-blend-mode: difference;
        border-bottom: 1px solid white;
        padding: 2px;
    }

</style>
