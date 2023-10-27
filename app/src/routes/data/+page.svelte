<script lang="ts">
    import Select from 'svelte-select';
    import * as config from "../config.json";
    import "../app.css";
    let db: any = null;

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
<h1 class="text-3xl font-bold underline">Welcome to Data Editing page</h1>
<main>
    <table class="w-full text-sm text-left text-gray-500 dark:text-gray-400">
    
        <thead class="text-xs text-gray-700 uppercase bg-gray-50 light:bg-gray-700 dark:text-gray-400">
          <tr>
            {#each params as param}
                <th scope="col" class="px-6 py-3">
                    {param}
                </th>
            {/each}
          </tr>
        </thead>
        <tbody>
        {#each data as el}
          <tr class="bg-white border-b light:bg-gray-800 dark:border-gray-700">
            
            {#each params as param}
               <td class="px-6 py-3">{el[param]}</td> 
            {/each}
          </tr>  
        {/each}
          
        </tbody>
      </table>

    <div>
        <div class="container db_select">
            <Select items={config.databases} bind:justValue={db} on:change={loadFindForm}/>
        </div>
        <button class="bg-blue-500 hover:bg-blue-400 text-white font-bold py-2 px-4 border-b-4 border-blue-700 hover:border-blue-500 rounded" on:click = {loadFindForm}>Create Form</button>
        <div class="form">
                <div class="mb-4 container">
            {#each findFormParams as param, index}
                    <label class="block text-gray-700 text-sm font-bold mb-2" style="text-align: center;" for="username">
                      {param}
                    </label>
                    <input class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="username" type="text" bind:value={findFormValues[index]}>
            {/each}

                  </div>
        </div>
        <button class="bg-blue-500 hover:bg-blue-400 text-white font-bold py-2 px-4 border-b-4 border-blue-700 hover:border-blue-500 rounded" on:click = {find}>Find</button>
        <button class="bg-blue-500 hover:bg-blue-400 text-white font-bold py-2 px-4 border-b-4 border-blue-700 hover:border-blue-500 rounded" on:click={loadInsertForm}>Add data</button>
        <div class="form">
        <div class="mb-4 container">
            {#each insertFormParams as param, index}
                    <label class="block text-gray-700 text-sm font-bold mb-2" style="text-align: center;" for="username">
                      {param}
                    </label>
                    <input class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="username" type="text" bind:value={insertFormValues[index]}>
            {/each}

                  </div>
        </div>
               
        <button class="bg-blue-500 hover:bg-blue-400 text-white font-bold py-2 px-4 border-b-4 border-blue-700 hover:border-blue-500 rounded" on:click={insert}>Insert</button>
    </div>
</main>