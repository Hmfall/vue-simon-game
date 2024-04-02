<template>
    <div id="app">
        <SimosGame />
    </div>
</template>

<script>
import SimosGame from '@/components/SimosGame.vue';
import '@/style.scss';

export default {
    name: 'App',
    components: {
        SimosGame,
    },
};
</script>
