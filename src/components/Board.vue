<template>

    <div
        :class="$style.container"
        :style="boardSizes">

            <div :class="[$style.overlay, {[$style.overlay_active]: isFinish}]">

                <span :class="$style.overlay_text">FINISH</span>
                <div :class="$style.overlay_btn" @click="mixGridBoard">NEW GAME</div>

            </div>

            <Block
                v-for="(block, index) in gridBoard"
                :key="index"
                :styleBlock="block"
                :index="index"
                :style="{ width: `${blockWidth}px`, height: `${blockWidth}px` }"
                @click.native="handlerClick(index)">
            </Block>

    </div>

</template>

<script>

    import Block from './Block'

    const DEFAULT_EMPTY_BLOCK = 8

    // keep this for future implementations for dynamic sizes
    const BOARD_SIZE = 500
    const BLOCK_IN_ROW = 3

    const BLOCK_WIDTH = (BOARD_SIZE / BLOCK_IN_ROW)

    /**
     * create an array of objects with background-poisition, order and opacity of n blocks.
     * @return {Array}
     */
    function initBoard () {
        let startBoard = []

        // assign background position
        for (let row = 0; row < BLOCK_IN_ROW; row++ ) {
            for (let block = 0; block < BLOCK_IN_ROW; block++ ) {
                startBoard.push({
                    backgroundPosition: `-${BLOCK_WIDTH * block}px -${BLOCK_WIDTH * row}px`
                })
            }
        }

        // assign order
        return startBoard.map((o, i) => ({
            ...o,
            opacity: (i === DEFAULT_EMPTY_BLOCK) ? 0 : 1,
            order: i
        }))
    }

    export default {
    	name: 'Board',
        data () {
            return {
                gameOver: false,
                gridBoard: [],
                isFinish: false
            }
        },
    	components: {
    		Block
    	},
        methods: {
            /*
            *   mixBoard: initialized the board with random indexs and images
            */
            mixGridBoard () {
                let cards = [...initBoard()]

                let rawId = cards.length -1

                // randomize indexs
                while (0 !== rawId) {
                    let randId = Math.floor(Math.random() * rawId)
                    rawId -= 1

                    let tempValue = cards[rawId].order
                    cards[rawId].order = cards[randId].order
                    cards[randId].order = tempValue
                }
                this.gridBoard = cards
            },
            /**
             * check conditions to switch blocks,
             * calc x,y position of clicked and empty blocks
             * @param  {Number} index index clicked block
             */
            handlerClick (index) {
                // coordinates clicked block
                const clickColumn = this.gridBoard[index].order % BLOCK_IN_ROW
                const clickRow = Math.floor(this.gridBoard[index].order / BLOCK_IN_ROW)

                // coordinates empty block
                const emptyColumn = this.gridBoard[DEFAULT_EMPTY_BLOCK].order % BLOCK_IN_ROW
                const emptyRow = Math.floor(this.gridBoard[DEFAULT_EMPTY_BLOCK].order / BLOCK_IN_ROW)

                // valid conditions to switch
                // same row and different columns
                const SAME_ROW = ((clickRow === emptyRow) && ((emptyColumn + 1 === clickColumn) || (emptyColumn -1 === clickColumn)))
                // same column different and row +- 1
                const SAME_COLUMN = (((clickRow === emptyRow + 1 ) || (clickRow === emptyRow - 1 )) && (emptyColumn === clickColumn))

                if (SAME_ROW || SAME_COLUMN) {
                    // switch blocks
                    this.invertPosition(index)
                }
            },
            /*
            *   invertPosition: switch index and background position of the empty box with clicked box
            */
            invertPosition (index) {

                let board = [...this.gridBoard]
                let temOrder = board[index].order

                // switch orders
                board[index].order = board[DEFAULT_EMPTY_BLOCK].order

                // update empty block order
                board[DEFAULT_EMPTY_BLOCK].order = temOrder

                this.gridBoard = [...board]
            }
        },
        watch: {
            gridBoard: {
                deep: true,
                handler: function () {
                    // check if the game is over
                    this.isFinish = !(this.gridBoard.find((obj, index) => (obj.order !== index)))
                }
            }
        },
        created () {
            // keep this for future implementations for dynamic sizes
            this.boardSizes = `width: ${BOARD_SIZE}px; height: ${BOARD_SIZE}px;`

            // initialized const to use it in template
            this.blockWidth = BLOCK_WIDTH

            // initiliazed and mix grid board
            this.mixGridBoard()
        }
    }
</script>

<style lan="scss" module>

    .container {
        display: flex;
        flex-wrap: wrap;
        position: relative;
        border: 1px solid #666;
        background-color: lightgray;
    }
    .overlay {
        opacity: 0;
        display: none;
        position: absolute;
        z-index: 1;
        width: 100%;
        height: 100%;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        background-color: black;
        transition: opacity .5s;
        font-family: Arial;
    }
    .overlay_active {
        opacity: 1;
        display: flex;
    }

    .overlay_text {
        font-size: 30px;
        color: white;
        margin-bottom: 20px;
    }
    .overlay_btn {
        color: white;
        padding: 15px 30px;
        background-color: #333333;
        cursor: pointer;
    }

</style>
