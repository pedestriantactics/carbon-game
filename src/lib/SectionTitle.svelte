<script lang="ts">
    export let title: string;
    export let total: number;
    export let roundDigits: number = 0;
    export let suffix: string = "kg";
    export let useH2: boolean = false;
    export let useRule: boolean = true;
</script>

<div id="container">
    <div class="left">
        {#if useH2}
            <h2>{title}</h2>
        {:else}
            <h3>{title}</h3>
        {/if}
    </div>
    <div class="right">
        {#if useH2}
            <h2>{total.toFixed(roundDigits)}{suffix}</h2>
        {:else}
            <h3>{total.toFixed(roundDigits)}{suffix}</h3>
        {/if}
    </div>
</div>
{#if useRule}
    <hr />
{/if}

<style>
    #container {
        height: 2em;
        padding-top: 0.5em;
        padding-bottom: 0.5em;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }
    input {
        width: 100px;
    }
    .left,
    .right {
        font-weight: bold;
    }
    .left {
        float: left;
    }
    .right {
        float: right;
    }
</style>
