<script setup>
import { ref, watch } from 'vue';

const moveCount = ref(1);
const player = ref('First');
const gameGrid = ref([
					  [null, null, null], 
					  [null, null, null], 
					  [null, null, null]
]);
const pause = ref(false);

const markPoses = { x: [], o: []}
const winPoses = [[1,2,3], [4,5,6], [7,8,9], [1,4,7], [2,5,8], [3,6,9], [1,5,9], [3,5,7]];
const history = ref([]);

const endGame = () => {
	const fields = document.getElementsByClassName('field');
	for(let i = 0; i < 9; i++) {
		fields[i].classList.remove('x-mark', 'o-mark');
	}
	player.value = 'First';
	moveCount.value = 1;
	markPoses.x = [];
	markPoses.o = [];
	gameGrid.value = [[null, null, null], [null, null, null], [null, null, null]];
	pause.value = false;
}

let mas = { win: false, winner: null, response: 'Не понятно кто выиграл' };

const isWin = (mas) => {
	let x, o = false;
	for(let i = 0; i < winPoses.length; i++) {
		let positionChecker = (arr, target) => target.every(v => arr.includes(v));
		if(positionChecker(markPoses.x, winPoses[i])) {
			x = true;
		} else if(positionChecker(markPoses.o, winPoses[i])) { 
			o = true;
		}
	}
	if(x) {
		mas.response = 'X - winner';
		mas.win = true;
		mas.winner = 'X';
	} else if(o) {
		mas.response = 'O - winner';
		mas.win = true;
		mas.winner = 'O';
	}
	return mas;
}
					
const fieldClick = (e) => {
	if(e.target.classList.contains('x-mark') || e.target.classList.contains('o-mark') || pause.value ) {
		console.log('Символ уже есть в клетке!');
		return;
	} else {
		if(player.value === 'First') {
			e.target.classList.add('x-mark');
			let pos = 0;
			for(let i = 0; i < 3; i++) {
				for(let j = 0; j < 3; j++) {
					if(pos === Number(e.target.getAttribute('pos'))) {
						gameGrid.value[i][j] = 'x';
						markPoses.x.push(pos + 1);
					}
					pos++;
				}
			}
			player.value = 'Second';
		} 
		else if(player.value === 'Second') {
			e.target.classList.add('o-mark');
			let pos = 0;
			for(let i = 0; i < 3; i++) {
				for(let j = 0; j < 3; j++) {
					if(pos === Number(e.target.getAttribute('pos'))) {
						gameGrid.value[i][j] = 'o';
						markPoses.o.push(pos + 1);
					}
					pos++;
				}
			}
			player.value = 'First';
		}
		moveCount.value++;
		if(moveCount.value !== 10) {
			// console.log('Ход игрока: ' + player.value);
		} else {
			alert('TIE');
			pause.value = true;
			// endGame();
		}
		isWin(mas);
		if(mas.win === true) {
			alert(mas.winner + ' WINS');
			history.value.push(mas.winner);
			mas.win = false;
			pause.value = true;
			// endGame();
		}
	}
}
</script>

<template>
	<h1 class="text-4xl uppercase font-bold text-center mb-10 text-slate-500">Tic tac toe</h1>
	<!-- <h2 class="mb-2 text-center text-xl text-slate-500 uppercase translate-y-5 bg-white w-min m-0 ml-auto mr-auto p-1 pl-3 pr-3 rounded-lg">History</h2> -->
	<div class="flex items-start gap-2 mb-10 p-5 bg-slate-50 min-h-10 rounded-xl shadow-inner">
		<span v-if="history.length === 0" class="text-slate-400 m-0 ml-auto mr-auto text-lg">History is empty</span>
		<span v-for="item in history" :class="item === 'X' ? 'text-cyan-500' : 'text-orange-500'" class="text-lg">{{ item }}</span>
	</div>
	<p class="text-center mb-3 text-slate-500 text-lg">{{ player + ' player turn' }}</p>
	<div class="game-grid grid grid-cols-3 grid-rows-3 gap-3 mb-10">
		<div 
			@click="fieldClick($event)" 
			v-for="(item, index) in 9" 
			:key="index" 
			:pos="index" 
			:class="'field relative shadow-lg hover:shadow-none w-24 h-24 rounded-xl border-2 border-gray-200  bg-gray-100 hover:border-gray-100 cursor-pointer duration-100 pos-' + (index + 1)" 
		></div>
	</div>
	<button @click="endGame" class="border-2 border-red-300 w-full p-3 rounded-lg text-red-600">Restart</button>
</template>

<style scoped>
.x-mark::after, .o-mark::after {
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
	font-size: 50px;
}

.x-mark::after {
	content: 'X';
	color: rgb(31, 226, 226);
}

.o-mark::after {
	content: 'O';
	color: rgb(233, 161, 27);
}
</style>
