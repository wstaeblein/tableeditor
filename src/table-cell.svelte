<script context="module">
    export let selRow;
    export let selCol;
    export function getSel() { return { row: selRow, col: selCol }; }
</script>

<script>
  	import { createEventDispatcher } from 'svelte';

    export let cell;
    export let configs;
    export let column;
    export let row;
    export let col;
    export let selected = false;

    const dispatch = createEventDispatcher();

    let editing = false;
    let div;

    function editCell(cell) { 
        editing = true;
        selRow = row;
        selCol = col;       
        setTimeout(() => { 
            cell = cell;   
            div.focus();
        }, 0);
   }

/*     function checkSelection(evt) { 
        if (evt.buttons == 1) { 
            //selected = true; 
            dispatch('selection', { drag: true, row: row, col: col, selected: true });
        }
    } */

    $: if (selRow > -1) console.log(row, col, selRow, selCol)

    function toggleSelection() {
        //selected = !selected;
        if (selRow == row && selCol == col) {
            selRow = null;
            selCol = null;
        } else {
            selRow = row;
            selCol = col;            
        }
        cell = cell;

        console.log('SEL: ' + selRow + ', ' + selCol)
        //dispatch('selection', { row: row, col: col, selected });
    }

    function render(c) {
        switch (typeof c) {
            case 'number':
                return c.toLocaleString();
            case 'date':
                let options = { year: "numeric", month: "2-digit", day: "2-digit" };
                return c.toLocaleDateString("en", options);
            case 'boolean':
                return c ? '✓' : '';

        }
        return c;
    }
</script>


{#if cell && cell.rowspan != 0 && cell.colspan != 0}
    <!-- svelte-ignore a11y-click-events-have-key-events -->
    <td on:dblclick={editCell.bind(this, cell)} on:click={toggleSelection}
        class="{cell.align || (column ? column.align : null) || 'lft'} {configs.nowrap ? 'nowrap' : ''} {$$restProps.class || ''} {selRow == row && selCol == col || editing ? 'selected' : ''}">
        {#if editing}
            <div contenteditable="{editing}" bind:this={div} on:blur={() => editing = false} class="{row < 2 ? 'down' : ''}">
                {cell.value}
            </div>
        {:else}
            {render(cell.value)}
        {/if}
    </td>
{/if}

<style>
    td {
        box-sizing: border-box;
        user-select: none;
        border: 1px solid transparent;
        position: relative;
    }

    .selected {
        background-color: rgb(238, 247, 255);
        box-shadow: inset 0 0 2px navy;
    }

    td.lft { text-align: left; }
    td.cnt { text-align: center; }
    td.rgt { text-align: right; }
    td.jus { text-align: justify; }

    td.tight, td.tight > div    { padding: 6px; }
    td.fit, td.fit > div        { padding: 10px; }
    td.loose, td.loose > div    { padding: 16px; }    

    div[contenteditable] {
        transition: all 0.4s ease;
        box-sizing: border-box;
        position: absolute;
        top: 0px;
        left: 0;
        width: fit-content;
        height: 100%;
        display: inline;
    }

    div[contenteditable=true] {
        background-color: #fff;
        z-index: 1;
        top: auto;
        bottom: 0;
        height: 120px;
        display: table-cell;
    }

    div[contenteditable=true].down {
        bottom: auto;
        top: 0;
    }

    div[contenteditable=false] {
        background-color: transparent;
    }
</style>