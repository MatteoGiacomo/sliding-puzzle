<template>
    <div :class="$style.container">
        <div :class="[$style.overlay, this.finish ? $style.active : '' ]">
            <span :class="$style.game">FINISH</span>
            <div
                :class="$style.btn_reset"
                @click.stop="newGame()">
                    NEW GAME
            </div>
        </div>
        <transition-group>
            <template v-for="(row,indexRow) in gridBoard">
                <Block
                    v-for="(box,indexBox) in row"
                    v-if="!(box.index == 8 )"
                    :dataBlock="box"
                    key="indexBox"
                    @click.native="setPosition(indexBox, indexRow)">
                </Block>
            </template>
        </transition-group>
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

    const RANDOM_NUMBERS = [
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
                finish: false
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

                _.each (this.gridBoard, (row) => {
                    let numbersRow = numbers.splice (0, row.length)
                    _.each (row, (box, index) => {
                        if (numbersRow[index] === box.index) {
                            counter ++
                        }
                    })
                })

                if (counter === numbersLength) {
                    this.finish = true
                }
                return
            },

            /*
            *   invertPosition: switcha la posizione del box vuoto con il box cliccato e aggiorna l'indice del box vuoto
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

                // update indexEmptyBlock
                this.indexEmptyBlock = _.extend({}, { row: indexRow, box: indexBox })
                this.gridBoard = _.extend ({}, virtualBoard)
            },

            /*
            *   isUp, isDown, isRight, isLeft sono delle funzioni che verificano se il box vuoto è limitrofo al box cliccato
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
            *   setPosition: identifica la posizione del box cliccato e in base alla posizione
            *   chiama le funzioni di verifica del box vuoto
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
            *   mixBoard: initialized the matrix board with random numbers and images
            */
            mixBoard () {
                let numbers = RANDOM_NUMBERS
                let currentIndex = numbers.length -1
                let randomIndex
                let temporaryValue

                // randomize indexs
                while (0 !== currentIndex) {
                    randomIndex = Math.floor(Math.random() * currentIndex)
                    currentIndex -= 1

                    temporaryValue = numbers[currentIndex]
                    numbers[currentIndex] = numbers[randomIndex]
                    numbers[randomIndex] = temporaryValue
                }

                // populate the Board with random numbers
                _.each(this.gridBoard, (row) => {
                    let randomNumbersRow = numbers.splice (0, row.length)
                    _.each(row, (box, index) => {
                        _.extend (box, randomNumbersRow[index])
                    })
                })
            },
            /*
            *   reset:
            */
            newGame () {
                this.finish = false
                this.mixBoard ()
                this.indexEmptyBlock = _.extend({}, { row: 2, box: 2 })
            }
        },
        created () {
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
