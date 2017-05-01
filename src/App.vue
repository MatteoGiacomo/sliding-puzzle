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
        background-color: #282828;
        display: flex;
        align-items: center;
        flex-direction: column;
        padding-top: 100px;
    }

</style>
