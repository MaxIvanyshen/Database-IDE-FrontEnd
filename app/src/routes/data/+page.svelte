<script lang="ts">
    import * as config from "../config.json";
    let db = "postgres";
    let data: any = [];
    let params: any = [];
    let findFormValues: any = [];
    let findFormParams: any = []; 
    let insertFormParams: any = []; 
    let insertFormValues: any = []; 
    let form: any = {};
    let schema: any = null;
    let insertionObject: any = {}; 
    async function find(): Promise<void> {
        for(let i = 0; i < findFormParams.length; i++) {
            if(findFormValues[i] != "")
                form[findFormParams[i]] = findFormValues[i];
        }
        await fetch(config.url + "/" + db + "/find_many", {
            method: "POST",
            mode: "cors",
            headers: {
                "Content-Type": "application/json",
            },
            body: JSON.stringify(form),
        })
            .then(async (resp) => {
                data = await resp.json();
                console.log(await data[0]);
                params = await Object.keys(data[0]);
                console.log(data);
            });
            
        for(let i = 0; i < findFormParams.length; i++) {
            delete form[findFormParams[i]];
        }
    }
    
    async function loadFindForm(): Promise<void> {
        schema = await fetch(config.url + "/" + db + "/schema").then(resp => resp.json());
        findFormParams = Object.keys(schema);
        let newForm: boolean = true;
        for(let i = 0; i < findFormParams.length; i++) {
            if(findFormValues[i] != "")
                newForm = false;
        }
        for(let i = 0; i < findFormParams.length; i++) {
            if(newForm)
                findFormValues.push("");
            else {
                findFormValues[i] = "";
            }
        }
    }
    
    async function loadInsertForm(): Promise<void> {
        if(schema == null) {
            schema = await fetch(config.url + "/" + db + "/schema").then(resp => resp.json());
            insertFormParams = Object.keys(schema);
        }
        for(let i = 0; i < findFormParams.length; i++) {
            insertFormValues[i] = "";
        }

    }
    
    async function insert(): Promise<void> {
        for(let i = 0; i < insertFormParams.length; i++) {
            insertionObject[insertFormParams[i]] = insertFormValues[i];
        }     

        await fetch(config.url + "/" + db + "/write", {
            method: "POST",
            mode: "cors",
            headers: {
                "Content-Type": "application/json",
            },
            body: JSON.stringify(insertionObject),
        })
            .then(async (resp) => {
                console.log(await resp.json());
            });
    }
</script>

<h1>Welcome to Data Editing page</h1>

<main>
    <div>
        <div class="db_select">
            <select name="" id="" bind:value={db} on:change={loadFindForm}>
                {#each config.databases as database}
                    <option value="{database}">{database}</option>
                {/each}
            </select>
        </div>
        <div class="data">
            <ul>
                {#each data as el}
                    <li>
                        {#each params as key}
                            <p>{el[key]}</p>
                        {/each}
                    </li>
                {/each}               
            </ul>
        </div>
        <button on:click={loadFindForm}>Create Form</button>            
        <div class="form">
            {#each findFormParams as param, index}
                <div class="param">
                    <label for="param">{param}</label>
                    <input type="text" name="" id="param" bind:value={findFormValues[index]}> 
                </div>
            {/each}
        </div>
        <button on:click = {find}>find</button>
        <button on:click={loadInsertForm}>Add data</button>
        <div class="form">
            {#each insertFormParams as param, index}
                <div class="param">
                    <label for="param">{param}</label>
                    <input type="text" name="" id="param" bind:value={insertFormValues[index]}> 
                </div>
            {/each}
        </div>
        <button on:click={insert}>insert</button>
    </div>
</main>