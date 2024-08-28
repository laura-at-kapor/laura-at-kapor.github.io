<script>
    export let row;
    export let copyToClipboard;
</script>


<button on:click={() => copyToClipboard(row)}>
    {row.ncessch}
</button>
