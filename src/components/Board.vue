<template>
    <div :class="$style.container">
        <div :class="[$style.overlay, this.gameOver ? $style.active : '' ]">
            <span :class="$style.game">FINISH</span>
            <div
                :class="$style.btn_reset"
                @click.stop="newGame()">
                    NEW GAME
            </div>
        </div>
        <template v-for="(row,indexRow) in gridBoard">
            <Block
                v-for="(box,indexBox) in row"
                v-if="!(box.index == 8 )"
                :dataBlock="box"
                key="indexBox"
                @click.native="setPosition(indexBox, indexRow)">
            </Block>
        </template>
    </div>
</template>

<script>
    import Block from './Block'
    import _ from 'lodash'

    const BOARD = [
        [{
            boxPosition: {
                top: '0px',
                left: '0px'
            }
        },
        {
            boxPosition: {
                top: '0px',
                left: '166.6px'
            }
        },
        {
            boxPosition: {
                top: '0px',
                left: '333.2px'
            }
        }],
        [{
            boxPosition: {
                top: '166.6px',
                left: '0px'
            }
        },
        {
            boxPosition: {
                top: '166.6px',
                left: '166.6px'
            }
        },
        {
            boxPosition: {
                top: '166.6px',
                left: '333.2px'
            }
        }],
        [{
            boxPosition: {
                top: '333.2px',
                left: '0px'
            }
        },
        {
            boxPosition: {
                top: '333.2px',
                left: '166.6px'
            }
        },
        {
            boxPosition: {
                top: '333.2px',
                left: '333.2px'
            }
        }]
    ]

    const CARDS = [
        {
            index: 0,
            bgPosition: {
                backgroundPosition: '0px 0px'
            }
        },
        {
            index: 1,
            bgPosition: {
                backgroundPosition: '-166.6px 0px'
            }
        },
        {
            index: 2,
            bgPosition: {
                backgroundPosition: '-333.2px 0px'
            }
        },
        {
            index: 3,
            bgPosition: {
                backgroundPosition: '0px -166.6px'
            }
        },
        {
            index: 4,
            bgPosition: {
                backgroundPosition: '-166.6px -166.6px'
            }
        },
        {
            index: 5,
            bgPosition: {
                backgroundPosition: '-333.2px -166.6px'
            }
        },
        {
            index: 6,
            bgPosition: {
                backgroundPosition: '0px -333.2px'
            }
        },
        {
            index: 7,
            bgPosition: {
                backgroundPosition: '-166.6px -333.2px'
            }
        },
        {
            index: 8,
            bgPosition: {
                backgroundPosition: '-333.2px -333.2px'
            }
        }
    ]

    export default {
    	name: 'Board',
        data () {
            return {
                indexEmptyBlock: {
                    row: 2,
                    box: 2
                },
                gridBoard: BOARD,
                gameOver: false
            }
        },
    	components: {
    		Block
    	},
        methods: {

            /*
            *   isFinish: check if the game is over
            */
            isFinish () {
                let numbers = [0,1,2,3,4,5,6,7,8]
                let numbersLength = numbers.length
                let counter = 0

                for (let row in this.gridBoard) {
                    let numbersRow = numbers.splice (0, this.gridBoard[row].length)
                    for (let box in this.gridBoard[row]) {
                        if (numbersRow[box] === this.gridBoard[row][box].index){
                            counter ++
                        }
                    }
                }

                if (counter === numbersLength) {
                    this.gameOver = true
                }
                return
            },

            /*
            *   invertPosition: switch index and background position of the empty box with clicked box
            */
            invertPosition (indexBox, indexRow) {

                let virtualBoard = this.gridBoard
                let emptyBox = virtualBoard[this.indexEmptyBlock.row][this.indexEmptyBlock.box]
                let clickedBox = virtualBoard[indexRow][indexBox]

                let indexSwitched = clickedBox.index
                clickedBox.index = emptyBox.index
                emptyBox.index = indexSwitched

                let bgPositionSwitched = clickedBox.bgPosition
                clickedBox.bgPosition = emptyBox.bgPosition
                emptyBox.bgPosition = bgPositionSwitched

                //check if the game is finished
                this.isFinish()

                // update indexEmptyBlock and gridBoard to render
                this.indexEmptyBlock = { ...{ row: indexRow, box: indexBox } }
                this.gridBoard = { ...virtualBoard }
            },

            /*
            *   isUp, isDown, isRight, isLeft: check if empty box is close to clicked box
            */
            isUp (indexBox, indexRow) {
                if (indexBox == this.indexEmptyBlock.box && indexRow - 1 == this.indexEmptyBlock.row) {
                   return true
               } else {
                   return false
               }
            },
            isDown (indexBox, indexRow) {
                if (indexBox == this.indexEmptyBlock.box && indexRow + 1 == this.indexEmptyBlock.row) {
                   return true
               } else {
                   return false
               }
            },
            isLeft (indexBox, indexRow) {
                if (indexBox - 1 == this.indexEmptyBlock.box && indexRow == this.indexEmptyBlock.row) {
                    return true
                } else {
                    return false
                }
            },
            isRight (indexBox, indexRow) {
                if (indexBox + 1 == this.indexEmptyBlock.box && indexRow == this.indexEmptyBlock.row) {
                    return true
                } else {
                    return false
                }
            },

            /*
            *   setPosition: identify the position of clicked box and checks if empty box is close to clicked box
            *   @param indexBox and IndexRow: numbers or strings which define clicked box position
            */
            setPosition(indexBox, indexRow) {

                if ( (typeof indexRow) == 'string' || (typeof indexBox) == 'string') {
                    indexRow = parseFloat(indexRow)
                    indexBox = parseFloat(indexBox)
                }

                let maxLengthRow =  this.gridBoard[indexRow].length -1

                // first row
                if (indexRow == 0 ) {
                    if (indexBox == 0) {
                        if (this.isDown (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                        else if (this.isRight (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                    }

                    else if (indexBox == maxLengthRow ) {
                        if (this.isDown (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                        else if (this.isLeft (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                    }

                    else {
                        if (this.isDown (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                        else if (this.isLeft (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                        else if (this.isRight (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                    }
                }

                // last row
                else if (indexRow == maxLengthRow) {
                    if (indexBox == 0) {
                        if (this.isUp (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                        else if (this.isRight (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                    }

                    else if (indexBox == maxLengthRow ) {
                        if (this.isUp (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                        else if (this.isLeft (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                    }

                    else {
                        if (this.isUp (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                        else if (this.isLeft (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                        else if (this.isRight (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                    }
                }

                // others rows
                else {
                    if (indexBox == 0) {
                        if (this.isUp (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                        else if (this.isDown (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                        else if (this.isRight (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                    }

                    else if (indexBox == maxLengthRow ) {
                        if (this.isUp (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                        else if (this.isDown (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                        else if (this.isLeft (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                    }
                    else {

                        if (this.isUp (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                        else if (this.isDown (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                        else if (this.isLeft (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }
                        else if (this.isRight (indexBox, indexRow)) {
                            this.invertPosition (indexBox, indexRow)
                        }

                    }
                }
            },

            /*
            *   mixBoard: initialized the board with random indexs and images
            */
            mixBoard () {
                let cards = CARDS
                let currentIndex = cards.length -1
                let randomIndex
                let temporaryValue

                // randomize indexs
                while (0 !== currentIndex) {
                    randomIndex = Math.floor(Math.random() * currentIndex)
                    currentIndex -= 1

                    temporaryValue = cards[currentIndex]
                    cards[currentIndex] = cards[randomIndex]
                    cards[randomIndex] = temporaryValue
                }

                // populate the Board with random cards
                for (let row in this.gridBoard) {
                    let randomNumbersRow = cards.splice(0, this.gridBoard[row].length)
                    for (let box in this.gridBoard[row]) {
                        Object.assign(this.gridBoard[row][box], randomNumbersRow[box])
                    }
                }

            },

            /*
            *  newGame: reset the board for a new match
            */
            newGame () {
                this.gameOver = false
            },

            /*
            *   initBoard: generate the board with numb column and numb row. It permits to choose the numbers of cards.
            *   @param {Number} numb: number of columns and rows
            */
            initBoard (numb) {
                let boxSize = 500 / numb
                let heightBox = 0
                let widthBox = 0
                let temporaryBoard = []

                _.times (numb, (n) => {
                    _.times (numb, (n) => {
                        temporaryBoard.push({
                            boxPosition: {
                                top: heightBox + 'px',
                                left: widthBox + 'px'
                            }
                        })
                        widthBox = widthBox + boxSize
                    })

                    heightBox = heightBox + boxSize
                    widthBox = 0
                })
                this.gridBoard = temporaryBoard
            }
        },
        created () {
            // initBoard doesn't work properly yet
            // this.initBoard ()
            //     this.mixBoard ()
            // }
            this.mixBoard ()
        }
    }
</script>

<style lan="scss" module>

    .wrapper {
        width: 100%;
        display: flex;
        justify-content: center;
        align-items: center;
    }
    .container {
        width: 500px;
        height: 500px;
        border: 1px solid #666;
        display: flex;
        position: relative;
    }
    .overlay {
        position: absolute;
        z-index: 1;
        width: 100%;
        height: 100%;
        background-color: black;
        transition: opacity .5s;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        opacity: 0;
        display: none;
        font-family: Arial;
    }
    .game {
        font-size: 30px;
        color: white;
        margin-bottom: 20px;
    }
    .btn_reset {
        color: white;
        padding: 15px 30px;
        background-color: #333333;
        cursor: pointer;
    }
    .active {
        opacity: 1;
        display: flex;
    }

</style>
