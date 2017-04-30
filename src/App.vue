<template>
    <div class="wrapper">
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
    }
    .wrapper {
        width: 100%;
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: column;
    }

</style>
