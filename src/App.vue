<template>
    <div id="app">
        <div class="wrap">
            <div class="blocks">
            <div class="block blue" :class="{ activeBlue: active.first }" ref="1" id="1" @click="checkAnswer"></div>
            <div class="block orange" :class="{ activeOrange: active.second }" ref="2" id="2" @click="checkAnswer"></div>
            <div class="block yellow" :class="{ activeYellow: active.third }" ref="3" id="3" @click="checkAnswer"></div>
            <div class="block green" :class="{ activeGreen: active.fourth }" ref="4" id="4" @click="checkAnswer"></div>
            </div>
            <div class="infoGame">
                <h3>Round: {{ round }}</h3>
                <button class="btn" @click="step" :disabled="round > 0">Start</button>
                <p class="info" v-if="failed">Sorry, you loose</p>
                <p class="info" v-if="win">You WIN !!!</p>
                <h2>Level:</h2>
               <div class="form_radio">
                    <input id="radio-1" type="radio" name="radio" value="1500" v-model="level" checked>
                    <label for="radio-1">Light</label>
                </div>
                <div class="form_radio">
                    <input id="radio-2" type="radio" name="radio" value="1000" v-model="level">
                    <label for="radio-2">Normal</label>
                </div>
                <div class="form_radio">
                    <input id="radio-3" type="radio" name="radio" value="400" v-model="level">
                    <label for="radio-3">Hard</label>
                </div>
            </div>
        </div>
        <div class="title">
                <img src="./assets/img/simon.jpg" alt="" srcset="">
                <h3 class="text">Simon <span class="strike">cat</span> Game</h3>
            </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            round: 0,
            level: '1500',
            questionsArr: [],
            answersArr: [],
            currentNumber: null,
            blockIsActive: false,
            active: {
                first: false,
                second: false,
                third: false,
                fourth: false
            },
            audio: {
                first: new Audio(require('./assets/audio/1.mp3')),
                second: new Audio(require('./assets/audio/2.mp3')),
                third: new Audio(require('./assets/audio/3.mp3')),
                fourth: new Audio(require('./assets/audio/4.mp3'))
            },
            failed: false,
            win: false,
            makingStep: false,
            checkingAnswer: false,
        }
    },
    watch: {
        blockIsActive: function() {
            switch(this.currentNumber){
                case 1:
                    this.active.first = !this.active.first
                    if(this.active.first) {
                        this.audio.first.play()
                    }
                    break;
                case 2:
                    this.active.second = !this.active.second
                    if(this.active.second){
                        this.audio.second.play()
                    }
                    break;
                case 3:
                    this.active.third = !this.active.third
                    if(this.active.third) {
                        this.audio.third.play()
                    }
                    break;
                case 4:
                    this.active.fourth = !this.active.fourth
                    if(this.active.fourth) {
                        this.audio.fourth.play()
                    }
                    break;
            }
        }
    },
    methods: {
        step(){
            this.makingStep = true;
            this.round++
            if(this.round === 1) {
                this.failed = false,
                this.win = false
            }
            if(this.round > 10 && !this.failed){
                this.win = true;
                this.resetGame();
                return;
            }
            let num = this.generateNumber(1,4)
            this.questionsArr.push(num)
            this.questionsArr.map( (item, index) => {
                setTimeout(() => {
                    this.currentNumber = item
                    this.blockIsActive = true;
                    setTimeout(() => {
                        this.blockIsActive = false;
                        this.makingStep = false
                        }, this.level - 100)
                }, this.level*index )
                });  
        },
        checkAnswer(e){
            if(this.round === 0 || this.makingStep) {
                return;
            }
            this.currentNumber = +e.target.id
            this.blockIsActive = true;
            setTimeout(() => {
                    this.blockIsActive = false;
                    }, 200)
            setTimeout(() => {
                this.answersArr.push(+e.target.id)
                if(this.answersArr.length !== this.questionsArr.length) {
                    return;
                }
                for(let i = 0; i < this.answersArr.length; i++ ){
                    if(this.answersArr[i] !== this.questionsArr[i]){
                        this.endGame();
                        break;
                    }
                }
                if(!this.failed) {
                    this.answersArr = []
                    this.step()
                }
            }, this.level);
            
        }, 
        generateNumber(min,max){
           return Math.round(Math.random() * (max - min) + min);
        },
        resetGame(){
            this.round = 0
            this.questionsArr = []
            this.answersArr = []
            this.currentNumber = null
            this.makingStep = false
            this.active.first = false
            this.active.second = false
            this.active.third = false
            this.active.fourth = false     
        },
        endGame(){
            this.failed = true;
            this.resetGame()
        }
    },
};
</script>

<style scoped>
.wrap {
    margin: 0 auto;
    margin-top: 100px;
    display: flex;
    justify-content: space-evenly;
    max-width: 900px;
}
.blocks {
    width: 300px;
    height: 300px;
    border-radius: 50%;
    display: grid;
    grid-template-columns: 1fr 1fr;
    grid-template-rows: 1fr 1fr;
}
.block {
    cursor: pointer;
}
.blue {
    background-color: #87CEEB;
    border-top-left-radius: 100%;
}
.orange {
    background-color: #FFA07A;
    border-top-right-radius: 100%;
}
.yellow {
    background-color: #BDB76B;
    border-bottom-left-radius: 100%;
}
.green {
    background-color: #90EE90;
    border-bottom-right-radius: 100%;
}
.activeBlue {
    background-color: #191970;
}
.activeOrange {
    background-color: #FF4500;
}
.activeYellow {
    background-color: #FFFF00;
}
.activeGreen {
    background-color: #006400;
}
.form_radio {
	margin-bottom: 10px;
}
.form_radio input[type=radio] {
	display: none;
}
.form_radio label {
	display: inline-block;
	cursor: pointer;
	position: relative;
	padding-left: 25px;
	margin-right: 0;
	line-height: 18px;
	user-select: none;
}
.form_radio label:before {
	content: "";
	display: inline-block;
	width: 17px;
	height: 18px;
	position: absolute;
	left: 0;
	bottom: 1px;
	background: url(./assets/img/radio-1.png) 0 0 no-repeat;
}
.form_radio input[type=radio]:checked + label:before {
	background: url(./assets/img/radio-2.png) 0 0 no-repeat;
}
.form_radio label:hover:before {
	filter: brightness(120%);
}

.form_radio input[type=radio]:disabled + label:before {
	filter: grayscale(100%);
}
.btn {
    padding: 10px 15px;
    border: 1px solid teal;
    text-align: center;
    cursor: pointer;
    font-size: 15px;
    font-weight: bold;
}
.btn:hover {
    background-color: plum;
    color: white;
}
.info {
    font-size: 18px;
    font-weight: bold;
    color: red;
}
.infoGame {
    max-width: 125px;
    width: 100%;
}
.title {
    display: flex;
    justify-content: center;
}
.text {
   margin-top: 134px;
   font-size: 38px;
}
.strike {
    text-decoration: line-through;
}
</style>>

