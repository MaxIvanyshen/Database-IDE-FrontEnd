<script lang="ts">
    import * as config from "../config.json";
    let db = "postgres";
    let data: any = [];
    let params: any = [];
    let formValues: any = [];
    let formParams: any = []; 
    let form: any = {};
    async function find(): Promise<void> {
        for(let i = 0; i < formParams.length; i++) {
            if(formValues[i] != "")
                form[formParams[i]] = formValues[i];
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

                params = Object.keys(data[0]);
                console.log(data);
            })
    }
    
    async function createForm(): Promise<void> {
        let schema = await fetch(config.url + "/" + db + "/schema").then(resp => resp.json());
        formParams = Object.keys(schema);
        for(let i = 0; i < formParams.length; i++) {
            formValues.push("");
        }
    }
</script>

<h1>Welcome to Data Editing page</h1>

<main>
    <div>
        <div class="db_select">
            <select name="" id="" bind:value={db} >
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
        <button on:click={createForm}>Create Form</button>            
        <div class="form">
            {#each formParams as param, index}
                <div class="param">
                    <label for="param">{param}</label>
                    <input type="text" name="" id="param" bind:value={formValues[index]}> 
                </div>
            {/each}
        </div>
        <button on:click = {find}>find</button>
    </div>
</main>