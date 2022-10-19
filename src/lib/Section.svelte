<script lang="ts">
    export let title: string;
    export let total: number;
</script>

<table>
    <thead>
        <tr
            ><th><b>{title}</b></th><td /><td><b>{total.toFixed(0)}kg</b></td
            ></tr
        >
    </thead>
    <tbody>
        <slot />
    </tbody>
</table>
