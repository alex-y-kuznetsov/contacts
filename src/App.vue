<template>
    <div class="container">
        <HomePage />
    </div>
</template>

<script>
import HomePage from '@/components/HomePage.vue'

export default {
    name: 'App',
    components: {
        HomePage
    }
}
</script>

<style lang="less">
@import url('https://fonts.googleapis.com/css2?family=Nunito:ital,wght@0,400;0,700;1,400&display=swap');

:root {
    --color-white: #ffffff;
    --color-grey: #1c1a1a;
    --color-bg: #0e2c40;
    --color-text: #c1a187;
    --color-text-light: #b2b1b0;
    --color-green: #148d8d;
    --color-yellow: #c1a1a7;
    --color-dark-green: #1a4a5a;
    --main-transition: 0.2s;
}

html,
body {
    height: 100%;

    &::-webkit-scrollbar {
        width: 6px;
        height: 6px;
        background-color: transparent;
    }

    &::-webkit-scrollbar-thumb {
        background-color: var(--color-text);
        border-radius: 6px;
        -webkit-transition: background-color 2s;
        transition: background-color 2s;
    }
}

body {
    margin: 0;
    background-color: var(--color-bg);
}

#app {
    height: 100%;
    font-family: Nunito, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: var(--color-text);
}

.container {
    max-width: 800px;
    margin: 0 auto;
    padding: 0 20px;
}
</style>
