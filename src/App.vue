<template>
    <div>
        <Board></Board>
    </div>
</template>

<script>
    import Board from './components/Board'

    export default {
    	name: 'app',
    	components: {
    		Board
    	}
    }
</script>

<style lan="scss">

    body {
        position: fixed;
        left: 0;
        right: 0;
        bottom: 0;
        top: 0;
        background-color: lightblue;
        display: flex;
        align-items: center;
        justify-content: center;
        flex-direction: column;
        user-select: none;
    }

</style>
