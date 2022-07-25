<template>
<div>
    <h3>Player <span class="score">{{this.playerScore}}</span>X<span class="score">{{this.computerScore}}</span> Computers</h3>
    <hr>
</div>

</template>

<script>
    export default{
        props: ['playerScore','computerScore']
    }

</script>

<style lang="scss">
    span.score{
        border: 1px solid black;
        padding: 8px;
        margin: 5px;
    }
</style>