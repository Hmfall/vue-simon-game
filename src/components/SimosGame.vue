<template>
    <div class="content">
        <div class="content-inner">
            <h1>Simos Says</h1>
            <div class="body">
                <ul class="colors">
                    <li
                        v-for="(color, index) in colors"
                        :key="color"
                        class="colors-item"
                        :class="{ current: sequenceElement === index, pointer: !idle }"
                        :style="{ backgroundColor: color }"
                        @click="handleClick(index)"
                    />
                </ul>
                <div class="menu">
                    <div>
                        <h2>Round: {{ round }}</h2>
                        <button
                            class="button"
                            @click="start"
                        >
                            Start
                        </button>
                    </div>
                    <div v-show="failed">Sorry, you lost after {{ failed }} rounds.</div>
                    <div>
                        <h2>Game options:</h2>
                        <ul
                            class="options"
                            :class="{ 'disable-events': round && idle }"
                        >
                            <li class="options-item">
                                <input
                                    type="radio"
                                    id="ease"
                                    :value="1500"
                                    v-model="timeout"
                                />
                                <label for="ease">Ease</label>
                            </li>
                            <li class="options-item">
                                <input
                                    type="radio"
                                    id="normal"
                                    :value="1000"
                                    v-model="timeout"
                                />
                                <label for="normal">Normal</label>
                            </li>
                            <li class="options-item">
                                <input
                                    type="radio"
                                    id="hard"
                                    :value="400"
                                    v-model="timeout"
                                />
                                <label for="hard">Hard</label>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { delay, randomNumber } from '@/utils';
import blueAudio from '@/assets/audio/blue.ogg';
import redAudio from '@/assets/audio/red.ogg';
import greenAudio from '@/assets/audio/green.ogg';
import goldAudio from '@/assets/audio/gold.ogg';

const audiofiles = [blueAudio, redAudio, goldAudio, greenAudio];

export default {
    name: 'SimosGame',
    data() {
        return {
            colors: ['blue', 'red', 'gold', 'green'],
            round: 0,
            idle: true,
            failed: null,
            sequence: [],
            sequenceElement: null,
            timeout: 1500,
        };
    },
    methods: {
        start() {
            this.round = 0;
            this.idle = true;
            this.failed = null;
            this.sequence = [];
            this.newRound();
        },
        async newRound() {
            this.idle = true;
            this.round += 1;
            await delay(600);

            for (let i = 0; i < this.round; i++) {
                this.sequence.push(randomNumber(0, 3));
            }

            for (let i = 0; i < this.sequence.length; i++) {
                this.sequenceElement = this.sequence[i];
                this.audio(this.sequenceElement);

                await delay(300);

                this.sequenceElement = null;

                if (i !== this.sequence.length - 1) {
                    await delay(this.timeout);
                }
            }

            this.idle = false;
        },
        handleClick(index) {
            if (!this.idle) {
                this.audio(index);
                this.checkSequence(index);
            }
        },
        checkSequence(index) {
            const current = this.sequence.shift();

            if (current !== index) {
                this.gameOver();
            } else if (this.sequence.length === 0) {
                this.newRound();
            }
        },
        audio(index) {
            const audio = new Audio(audiofiles[index]);
            audio.play();
        },
        gameOver() {
            this.idle = true;
            this.failed = this.round;
            this.round = 0;
        },
    },
};
</script>

<style scoped lang="scss">
.content {
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    &-inner {
        width: 570px;
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 14px;
        margin: 60px 0;
    }
}

.body {
    width: 100%;
    display: flex;
    flex-direction: row;
    gap: 60px;
}

.colors {
    display: grid;
    grid-template: repeat(2, 140px) / repeat(2, 140px);
    &-item {
        opacity: 0.5;
        border-style: solid;
        transition: opacity 0.4s ease;
    }
    :nth-child(1) {
        border-radius: 100% 0 0 0;
        &:hover {
            border-width: 3px 0 0 3px;
        }
    }
    :nth-child(2) {
        border-radius: 0 100% 0 0;
        &:hover {
            border-width: 3px 3px 0 0;
        }
    }
    :nth-child(3) {
        border-radius: 0 0 0 100%;
        &:hover {
            border-width: 0px 0px 3px 3px;
        }
    }
    :nth-child(4) {
        border-radius: 0 0 100% 0;
        &:hover {
            border-width: 0 3px 3px 0;
        }
    }
}

.menu {
    width: 100%;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
}

.options {
    &-item {
        display: flex;
        align-items: center;
        margin-bottom: 8px;
        input {
            margin-right: 5px;
        }
    }
}

.button {
    background: #6dabe8;
    padding: 8px 28px;
    border-radius: 8px;
    &:hover {
        opacity: 0.9;
    }
    &:active {
        opacity: 1;
    }
}

.disable-events {
    pointer-events: none;
}

.pointer {
    cursor: pointer;
    &:active {
        opacity: 1;
    }
}

.current {
    opacity: 0.9;
}
</style>
