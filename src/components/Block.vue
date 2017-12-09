<template>
    <div
        :style="setClasses"
        :class="$style.box">
        <span :class="$style.indexBox">0</span>{{ dataBlock.index }}
    </div>
</template>

<script>

    export default {
        name: 'Block',
        props: {
            dataBlock: {
                type: Object,
                required: true
            }
        },
        computed: {
            setClasses () {
                return  {...this.dataBlock.boxPosition, ...this.dataBlock.bgPosition}
            }
        }
    }
</script>

<style lan="scss" module>

    .box {
        width: 166.6px;
        height: 166.6px;
        box-sizing: border-box;
        border: 1px solid #666;
        position: absolute;
        background-image: url('../assets/monks.jpg');
        background-repeat: no-repeat;
        color: white;
        font-family: Arial;
        padding: 3px;
        font-size: 15px;
    }
    .indexBox {
        border-bottom: 1px solid white;
        padding: 2px;
    }

</style>
