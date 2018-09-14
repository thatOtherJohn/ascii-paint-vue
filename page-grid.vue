<template lang="pug">
main(class="animation-grid")
    div(v-if="!loaded" id="test") X
    canvas(else id="canvas")
</template>

<script>
const forward = [ ' ', '·', '-', '+', '¤', '¥', 'O', 'X', 'M', '¶' ]
const backward = [ '¶', 'M', 'X', 'O', '¥', '¤', '+', '-', '·', ' ' ]

const fps = 20
let  currentFrame = 0

export default {
    data () {
        return {
            loaded: false,
            grid: {
                rows: 0,
                columns: 0
            },
            ctx: null,
            letterDims: 0
        }
    },
    methods: {
        pickCharacter (x, y) {
            return '0'
        },
        setupCanvas () {
            const canvas = document.querySelector('#canvas')
            canvas.width = window.innerWidth
            canvas.height = window.innerHeight - 5
            this.ctx = canvas.getContext('2d')
            this.ctx.fillStyle = 'grey'
            this.ctx.font = '15px monospace'
            this.ctx.textBaseline = 'top'
            this.letterDims = this.ctx.measureText('X')
        },
        measureFrameSize() {
            let testLetter = document.querySelector('#test')
            this.grid.columns = Math.ceil(window.innerWidth / testLetter.offsetWidth)
            this.grid.rows = Math.ceil(window.innerHeight / testLetter.offsetHeight)
        },
        addFrameData () {
            let fColumnPositions = []
            for( let i = 0; i < this.grid.columns; i++) {
                if ( i % 2 == 0 ) {
                    fColumnPositions[i] = 2
                } else if ( i % 3 == 0 ) {
                    fColumnPositions[i] = 1
                } else {
                    fColumnPositions[i] = 0
                }
            }
            let fRowPositions = []
            for( let j = 0; j < this.grid.rows; j++) {
                fRowPositions[j] = fColumnPositions
            }
            this.frame = fRowPositions
        },
        paintToCanvas (character) {
            for (let row = 0; row < this.frame.length; row++) {
                for (let column = 0; column < this.frame[row].length; column++) {
                    this.ctx.fillText( character, this.letterDims.width * column, this.letterDims.width * 2 * row)
                }
            }
        },
        paintFromFrameData () {
            for (let row = 0; row < this.frame.length; row++) {
                for (let column = 0; column < this.frame[row].length; column++) {
                    this.ctx.fillText( this.frame[row][column], this.letterDims.width * column, this.letterDims.width * 2 * row)
                }
            }
        },
        fillScreen (char) {
            this.paintToCanvas(char)
        },
        rampUpDown() {
            if (currentFrame < forward.length) {
                this.paintToCanvas(forward[currentFrame])
            } else if (currentFrame >= forward.length && currentFrame < forward.length + backward.length) {
                this.paintToCanvas(backward[currentFrame - backward.length])
            }
        },
        drawFrame () {
            setTimeout(() => {
                this.addFrameData()

                // Clear the canvas
                this.ctx.clearRect( 0, 0, canvas.width, canvas.height)
                
                // Reset frame counter
                if (currentFrame > fps) { currentFrame = 0 }
                
                // Debug
                // this.paintFromFrameData()
                // this.fillScreen('W')
                // this.rampUpDown()

                currentFrame++

                requestAnimationFrame(this.drawFrame)
            }, 1000 / fps);
        }
    },
    mounted () {
        this.setupCanvas()
        this.measureFrameSize()
        
        this.loaded = true
        requestAnimationFrame(this.drawFrame)
    }
}
</script>
 
 <style lang="sass">
 .animation-grid
    #test
        display: inline-block
        font-size: 10px
    .row
        display: flex
        .cell
            pointer-events: none
            min-width: 1em
            min-height: 1em
            text-align: center
 </style>
 