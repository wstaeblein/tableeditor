<script>
    import { onMount } from 'svelte';
    import Tablecell, { getSel } from "./table-cell.svelte"; 
    export let data = null;
    export let configs = {}
    export let width = '100%';
    export let padding = 'fit';

    //let selGrid = [];
    //let flag = false;
    let hasFooter = false;
    let selected = null;
    
    $: hasFooter = !!data.columns.find((c) => !!c.footer);
    $: hasSub = !!data.columns.find((c) => !!c.sub);
    
    onMount(() => {
        if (data) {

        }
        console.log(data.columns)
    });
    
    $: if (data && data.columns) {
        let hasData = data.rows && data.rows.length;
        data.columns.forEach((col, cind) => { 
            let sample = hasData ? data.rows[0][cind].value || null : null;
            if (!col.type) { col.type = getType(sample); } 
        });
    }

    function selection(evt) {
        let opt = evt.detail;
        if (opt.selected) {
            selected = { row: opt.row, col: opt.col }
        } else {
            selected = null;
        }
        
    }

    function clearSelection() {
        selGrid.forEach((row, rind) => {
            row.forEach((cell, cind) => selGrid[rind][cind] = false);
        });
    }

    function cmd(op) { alert(op)
        let sel = getSel();
        let row = sel.row ?? 0;
        let col = sel.col ?? 0;
        let sample = data.rows[row][col].value || null;
        let line = makeNewRow(data.columns, sample);
        switch(op) {
            case 'rowabove':
                data.rows.splice(row, 0, line);
                break;
            case 'rowbelow':
                data.rows.splice(row + 1, 0, line);
                break;
            case 'colbefore':
                createCol(col, 'Teste');
                break;
        }
        console.log(data.columns)
        console.log(data.rows)
        data.rows = data.rows;
    }

    function createCol(index, title, type, sub, footer, align) {
        let newcol = {title, sub, footer, align, type: type || getType(title)};
        let newcell = { value: properDefault(newcol.type) }

        Object.keys(newcol).forEach((k) => newcol[k] == null && delete newcol[k]);

        data.columns.splice(index, 0, newcol);
        data.rows.forEach((row) => {
            row.splice(index, 0, newcell);
        });

        data = data;
    }

    function makeNewRow(cols, sample) {
        let newLine = [];
        cols.forEach((c) => {
            newLine.push({ value: properDefault(cols.type || sample) });
        });
        return newLine;
    }

    function getType(sample) {
        switch(typeof sample) {
            case 'string':
                let mailRe = new RegExp(/^[A-Za-z0-9_!#$%&'*+\/=?`{|}~^.-]+@[A-Za-z0-9.-]+$/, "g");
                let urlRe = new RegExp(/(https|http|ftp):\/\/(www\.)?[-a-zA-Z0-9@:%._\+~#=]{2,256}\.[a-z]{2,4}\b([-a-zA-Z0-9@:%_\+.~#?&//=]*)/, 'g');
                if (mailRe.test(sample)) { return 'email'; }
                if (urlRe.test(sample)) { return 'url'; }
                return 'text';
            case 'number': return 'number';
            case 'date': return 'date';
            case 'boolean': return 'bool';
            default: return 'text';
        }
    }

    function properDefault(type) {
        switch(type) {
            case 'number': return 0;
            case 'date': return null;
            case 'bool': return false;
            default: return '';
        }
    }
/*
    Controls

    Insert row above
    Insert row below
    Insert column before
    Insert column after
    Delete row
    Delete Column
    Empty Table
*/
</script>

<nav>
    <!-- svelte-ignore a11y-click-events-have-key-events -->
    <span on:click={cmd.bind(this, 'rowabove')}>  <!-- Insert row above -->
        <svg viewBox="0 0 26.406 26.406"><g stroke="#1a1a1a"><g fill="none" stroke-linejoin="round" stroke-width=".2"><path d="M.1 17.571h8.735v8.735H.1z"/><path d="M8.835 17.571h8.735v8.735H8.835z"/><path d="M17.57 17.571h8.735v8.735H17.57z"/></g><g fill="#1a1a1a" fill-rule="evenodd" stroke-linejoin="round" stroke-width=".2"><path d="M.1 8.835h8.735v8.735H.1z"/><path d="M8.835 8.835h8.735v8.735H8.835z"/><path d="M17.57 8.835h8.735v8.735H17.57z"/></g></g><path d="M13.144.721h.118q.578 0 .706.745v2.196h2.078q.902.078.902.784-.147.745-.745.745v.039l-.196-.039h-2.039v2.235q-.137.745-.745.745h-.039q-.745-.147-.745-.667V5.191h-2l-.235.039q-.588 0-.745-.823v-.039q0-.529.784-.706h2.196V1.427q.137-.706.706-.706z"/><g fill="none" stroke="#1a1a1a" stroke-linejoin="round" stroke-width=".2"><path d="M.1.1h8.735v8.735H.1z"/><path d="M8.835.1h8.735v8.735H8.835z"/><path d="M17.57.1h8.735v8.735H17.57z"/></g></svg>
    </span>
    <!-- svelte-ignore a11y-click-events-have-key-events -->
    <span on:click={cmd.bind(this, 'rowbelow')}>  <!-- Insert row below -->
        <svg viewBox="0 0 26.406 26.406"><g stroke="#1a1a1a"><g fill="none" stroke-linejoin="round" stroke-width=".2"><path d="M.104 8.838h8.735V.103H.104z"/><path d="M8.839 8.838h8.735V.103H8.839z"/><path d="M17.574 8.838h8.735V.103h-8.735z"/></g><g fill="#1a1a1a" fill-rule="evenodd" stroke-linejoin="round" stroke-width=".2"><path d="M.104 17.574h8.735V8.839H.104z"/><path d="M8.839 17.574h8.735V8.839H8.839z"/><path d="M17.574 17.574h8.735V8.839h-8.735z"/></g></g><path d="M13.15 25.682h.118q.578 0 .706-.745v-2.196h2.078q.902-.078.902-.784-.147-.745-.745-.745v-.039l-.196.039h-2.039v-2.235q-.137-.745-.745-.745h-.039q-.745.147-.745.667v2.314h-2l-.235-.039q-.588 0-.745.823v.039q0 .529.784.706h2.196v2.235q.137.706.706.706z"/><g fill="none" stroke="#1a1a1a" stroke-linejoin="round" stroke-width=".2"><path d="M.105 17.571H8.84v8.735H.105z"/><path d="M8.84 17.571h8.735v8.735H8.84z"/><path d="M17.575 17.571h8.735v8.735h-8.735z"/></g></svg>
    </span>
    <!-- svelte-ignore a11y-click-events-have-key-events -->
    <span on:click={cmd.bind(this, 'colbefore')}>   <!-- Insert column before -->
        <svg viewBox="0 0 26.406 26.406"><g transform="matrix(0 1 1 0 -79.295031 -29.988849)" fill="none" stroke="#1a1a1a" stroke-width=".2" stroke-linejoin="round"><use xlink:href="#B"/><use xlink:href="#B" x="8.735"/><use xlink:href="#B" x="17.47"/></g><g transform="matrix(0 1 1 0 -88.03043 -29.988848)" fill="#1a1a1a" fill-rule="evenodd" stroke="#1a1a1a" stroke-width=".2" stroke-linejoin="round"><use xlink:href="#B"/><use xlink:href="#B" x="8.735"/><use xlink:href="#B" x="17.47"/></g><path d="M4.499 9.478h-.118q-.578 0-.706.745v2.196H1.598q-.902.078-.902.784.147.745.745.745v.039l.196-.039h2.039v2.235q.137.745.745.745h.039q.745-.147.745-.667v-2.314h2l.235.039q.588 0 .745-.823v-.039q0-.529-.784-.706H5.205v-2.235q-.137-.706-.706-.706z"/><g transform="matrix(0 1 -1 0 105.701 -29.989)" fill="none" stroke="#1a1a1a" stroke-width=".2" stroke-linejoin="round"><use xlink:href="#B"/><use xlink:href="#B" x="8.735"/><use xlink:href="#B" x="17.47"/></g><defs ><path id="B" d="M30.089 96.866h8.735v8.735h-8.735z"/></defs></svg>
    </span>
    <!-- svelte-ignore a11y-click-events-have-key-events -->
    <span on:click={cmd.bind(this, 'colafter')}>  <!-- Insert column after -->
        <svg viewBox="0 0 26.406 26.406"><g stroke="#1a1a1a"><g stroke-width=".2" stroke-linejoin="round" transform="rotate(90)" fill="none"><path d="M.101-8.835h8.735V-.1H.101z"/><path d="M8.835-8.835h8.735V-.1H8.835z"/><path d="M17.571-8.835h8.735V-.1h-8.735z"/></g><g stroke-width=".2" stroke-linejoin="round" transform="rotate(90)" fill="#1a1a1a" fill-rule="evenodd"><path d="M.101-17.57h8.735v8.735H.101z"/><path d="M8.835-17.57h8.735v8.735H8.835z"/><path d="M17.571-17.57h8.735v8.735h-8.735z"/></g></g><path d="M21.907 9.48h.118q.578 0 .706.745v2.196h2.078q.902.078.902.784-.147.745-.745.745v.039l-.196-.039H22.73v2.235q-.137.745-.745.745h-.039q-.745-.147-.745-.667V13.95h-2l-.235.039q-.588 0-.745-.823v-.039q0-.529.784-.706h2.196v-2.235q.137-.706.706-.706z"/><g stroke-width=".2" stroke-linejoin="round" transform="rotate(90)" fill="none" stroke="#1a1a1a"><path d="M.1-26.306h8.735v8.735H.1z"/><path d="M8.835-26.306h8.735v8.735H8.835z"/><path d="M17.57-26.306h8.735v8.735H17.57z"/></g></svg>
    </span>
    <span>
        <svg viewBox="0 0 26.406 26.406"><g stroke="#1a1a1a" stroke-width=".2" stroke-linejoin="round"><path d="M17.569 8.832H8.834v8.735h8.735zm-1.477 6.408c-.008.166-.081.355-.221.567l-.028.028c-.284.289-.635.292-1.053.009l-1.594-1.567-1.43 1.454-.11.167-.028-.027c-.28.284-.631.287-1.054.009-.336-.33-.311-.727.073-1.193l1.458-1.482-1.566-1.539c-.295-.409-.307-.751-.037-1.026l.083-.084c.266-.27.599-.273.998-.008l1.594 1.567 1.539-1.566c.45-.29.802-.312 1.053-.065l.028.028c.318.46.34.829.065 1.108l-.193.14-1.402 1.426 1.649 1.622c.123.121.181.265.174.431z" fill="#1a1a1a" fill-rule="evenodd"/><g fill="none"><path d="M26.301 26.304h-8.735v-8.735h8.735z"/><path d="M17.566 26.304H8.831v-8.735h8.735z"/><path d="M8.831 26.304H.096v-8.735h8.735z"/></g><path d="M26.3 17.569h-8.735V8.834H26.3zm-17.47 0H.095V8.834H8.83z" fill="#1a1a1a" fill-rule="evenodd"/><g fill="none"><path d="M26.306 8.829h-8.735V.094h8.735z"/><path d="M17.571 8.829H8.836V.094h8.735z"/><path d="M8.836 8.829H.101V.094h8.735z"/></g></g></svg>
    </span>
    <span>
        <svg viewBox="0 0 26.406 26.406"><g stroke="#1a1a1a" stroke-width=".2" stroke-linejoin="round"><path d="M8.833 8.836v8.735h8.735V8.836zm6.408 1.477c.166.008.355.081.567.221l.028.028c.289.284.292.635.009 1.053l-1.567 1.594 1.454 1.43.167.11-.027.028c.284.28.287.631.009 1.054-.33.336-.727.311-1.193-.073l-1.482-1.458-1.539 1.566c-.409.295-.751.307-1.026.037l-.084-.083c-.27-.266-.273-.599-.008-.998l1.567-1.594-1.566-1.539c-.29-.45-.312-.802-.065-1.053l.028-.028c.46-.318.829-.34 1.108-.065l.14.193 1.426 1.402 1.622-1.649c.121-.123.265-.181.431-.174z" fill="#1a1a1a" fill-rule="evenodd"/><g transform="rotate(90)" fill="none"><path d="M.104-26.305h8.735v8.735H.104z"/><path d="M8.839-26.305h8.735v8.735H8.839z"/><path d="M17.574-26.305h8.735v8.735h-8.735z"/></g><path d="M17.57.105V8.84H8.835V.105zm0 17.47v8.735H8.835v-8.735z" fill="#1a1a1a" fill-rule="evenodd"/><g transform="rotate(90)" fill="none"><path d="M.099-8.83h8.735v8.735H.099z"/><path d="M8.834-8.83h8.735v8.735H8.834z"/><path d="M17.569-8.83h8.735v8.735h-8.735z"/></g></g></svg>
    </span>
    <span>
        <svg viewBox="0 0 26.405 26.406" fill="none" stroke="#1a1a1a" stroke-linejoin="round" stroke-width=".2"><path d="M.1.1h8.735v8.735H.1z"/><path d="M8.835.1h8.735v8.735H8.835z"/><path d="M17.57.1h8.735v8.735H17.57zM.1 8.835h8.735v8.735H.1z"/><path d="M8.835 8.835h8.735v8.735H8.835z"/><path d="M17.57 8.835h8.735v8.735H17.57zM.1 17.57h8.735v8.735H.1z"/><path d="M8.835 17.57h8.735v8.735H8.835z"/><path d="M17.57 17.57h8.735v8.735H17.57z"/></svg>
    </span>    
</nav>
<section>
    <table style="width: {width}" class="{padding} {configs.align || 'lft'}" cellspacing="0">

        {#if data}
            <thead>
                <tr>
                    <td></td>
                    {#if configs.numbered}<td class="index"></td>{/if}
                    {#each data.columns as col}
                        <td class="{col.align || 'lft'} {configs.nowrap ? 'nowrap' : ''}" colspan="{col.colspan || 1}" rowspan="{col.rowspan || 1}">
                            <div class="title">{col.title}</div>
                            <div class="sub">{col.sub || ' '}</div>
                        </td>
                    {/each}
                </tr>
            </thead>
            <tbody>
                {#each data.rows as row, rind}
                <tr>
                    <td class="handler"></td>
                    {#if configs.numbered}<td class="index">{rind + 1}</td>{/if}                    
                    {#each row as cell, ind}
                        <!-- <Tablecell bind:cell bind:configs bind:column={data.columns[ind]} row={rind} col={ind} bind:selected={selGrid[rind][ind]} class="{padding}" on:selection></Tablecell> -->
                        <!-- <Tablecell bind:cell bind:configs bind:column={data.columns[ind]} row={rind} col={ind} class="{padding}" on:selection={selection}></Tablecell> -->
                        <Tablecell bind:cell bind:configs bind:column={data.columns[ind]} row={rind} col={ind} class="{padding}"></Tablecell>
                    {/each}
                </tr>
                {/each}
            </tbody>

            {#if hasFooter}
                <tfoot>
                    <tr>
                        <td></td>
                        {#each data.columns as col, ind}
                            <td class="{col.align || 'lft'} {configs.nowrap ? 'nowrap' : ''}" colspan="{configs.numbered && ind == 0 ? '2' : '1'}">
                                {#if col.footer}
                                    {#if col.footer.op == 'count'}
                                        {(col.footer.label || '$$').replace('$$', data.rows.length)}
                                    {:else if col.footer.op == 'sum'}
                                        {(col.footer.label || '$$').replace('$$', data.rows.reduce((acc, row) => acc + (parseFloat(row[ind].value) || 0), 0))}
                                    {/if}
                                {/if}
                            </td>
                        {/each}
                    </tr>
                </tfoot>
            {/if}
        {/if}
    
    </table>
</section>


<style>
    .handler {
        width: -1px;
        height: 100%;
        background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAwAAAAMCAYAAABWdVznAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsMAAA7DAcdvqGQAAAAlSURBVChTYyALNDQ0/AdhKBcvnwksMvgAPjeDADJ/1A+EAQMDAOUfLPUVcOE4AAAAAElFTkSuQmCC');
        background-size: 40%;
        opacity: 0.3;
        background-repeat: repeat-y;
        cursor: grab;
        padding-right: 0;
    }

    nav {
        background-color: #fff;
        padding: 5px 10px;
        display: flex;
        gap: 15px;
        margin-bottom: 10px;
    }

    nav > span {
        width: 18px;
        height: 18px;
        display: inline-block;
        cursor: pointer;
    }

    section {
        width: 100%;
        height: calc(100% - 37px);
        overflow: auto;
    }

    table {
        width: 100%;
        height: 100%;
        border-collapse: collapse;
        border: 1px solid #ddd;
        border-spacing: 0 5px;
    }

    table td {
        user-select: none;
    }

    table.lft {  margin: 0 auto 0 0; }
    table.cnt {  margin: 0 auto; }
    table.rgt {  margin: 0 0 0 auto; }

    table > thead,
    table > tfoot {
        border-bottom: 1px solid #eee;
        position: sticky;
        top: 0px; 
        background: rgb(247, 247, 247);
    }

    table > thead {
        inset-block-start: 0;
        z-index: 1;
    }

    table head > tr > td {
        border-bottom: 1px solid #eee;
    }

    td.index {
        white-space: nowrap;
        width: 10px;
    }

    table tfoot {
        inset-block-end: 0; 
    }

    table tfoot > tr > td {
        padding-top: 4px !important;
        padding-bottom: 4px !important;
        font-size: 90%;
        font-weight: bold;
        border-top: 1px solid #eee;

    }

    table td.nowrap { white-space: nowrap !important; }

    table > thead td > div.title {
        font-weight: bold;
        text-transform: uppercase;
    }

    table > thead td > div.sub {
        font-size: 75%;
        color: #aaa;
    }

    td.lft { text-align: left; }
    td.cnt { text-align: center; }
    td.rgt { text-align: right; }
    td.jus { text-align: justify; }

    table.tight td { padding: 6px; }
    table.fit td { padding: 10px; }
    table.loose td { padding: 16px; }
</style>