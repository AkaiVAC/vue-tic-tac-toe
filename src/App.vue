<script setup lang="ts">
    import { ref } from 'vue';

    const gameOver = ref<boolean>();
    const currentPlayer = ref<'P1' | 'P2'>('P1');
    const boardButtons = ref<Array<HTMLButtonElement>>();
    const boardState = ref([
        ['', '', ''],
        ['', '', ''],
        ['', '', ''],
    ]);
    const isGameWon = () => {
        const boardSize = boardState.value.length;

        // Helper function to check if all values in an array are equal and non-empty
        const allEqualAndNonEmpty = (arr: Array<string>) =>
            arr.every((val) => val === arr[0] && val !== '');

        // Check rows
        for (let i = 0; i < boardSize; i++) {
            if (allEqualAndNonEmpty(boardState.value[i])) {
                return true;
            }
        }

        // Check columns
        for (let i = 0; i < boardSize; i++) {
            let column = [];
            for (let j = 0; j < boardSize; j++) {
                column.push(boardState.value[j][i]);
            }
            if (allEqualAndNonEmpty(column)) {
                return true;
            }
        }

        // Check diagonals
        let diagonal1 = [];
        let diagonal2 = [];
        for (let i = 0; i < boardSize; i++) {
            diagonal1.push(boardState.value[i][i]);
            diagonal2.push(boardState.value[boardSize - 1 - i][i]);
        }
        if (allEqualAndNonEmpty(diagonal1) || allEqualAndNonEmpty(diagonal2)) {
            return true;
        }

        // No winning condition found
        return false;
    };

    const setSymbol = (e: Event, i: number, j: number) => {
        if (isGameWon()) {
            return;
        }
        const currentButton = e.target as HTMLButtonElement;

        if (currentButton.innerText === '.') {
            currentButton.innerText = currentPlayer.value === 'P1' ? 'X' : 'O';
            boardState.value[i][j] = currentButton.innerText;
        }

        if (isGameWon()) {
            gameOver.value = true;
            return;
        }

        currentPlayer.value = currentPlayer.value === 'P1' ? 'P2' : 'P1';
    };

    const resetBoard = () => {
        boardButtons.value?.forEach((button) => (button.innerText = '.'));
        currentPlayer.value = 'P1';
        boardState.value = [
            ['', '', ''],
            ['', '', ''],
            ['', '', ''],
        ];
        gameOver.value = false;
    };
</script>

<template>
    <div class="container">
        <div class="info">
            {{ gameOver ? 'Winner:' : 'Current Turn:' }} {{ currentPlayer }}
        </div>
        <div class="board">
            <template v-for="(_, i) in Array(3)" :key="i">
                <template v-for="(_, j) in Array(3)" :key="i">
                    <button
                        ref="boardButtons"
                        @click="(e) => setSymbol(e, i, j)">
                        .
                    </button>
                </template>
            </template>
        </div>
        <div class="actions">
            <button @click="resetBoard">reset</button>
        </div>
    </div>
</template>

<style scoped>
    .container {
        width: 50vmin;
        margin: auto;
        padding: 1rem;

        border-radius: 0.35rem;
        background-color: #181818;

        display: grid;
        grid-template: 1fr auto 1fr / 1fr;

        gap: 1rem;
    }

    .board {
        height: 50vmin;
        width: 100%;
        display: grid;
        grid-template: repeat(3, 1fr) / repeat(3, 1fr);
    }
</style>
