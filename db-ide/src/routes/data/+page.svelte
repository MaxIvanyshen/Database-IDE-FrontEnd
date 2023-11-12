<script lang="ts">
    import * as config from "../config.json";
    let db: any = "postgres";
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
        }
            insertFormParams = Object.keys(schema);
        for(let i = 0; i < insertFormParams.length; i++) {
            insertFormValues.push("");
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
    
    async function loadForms(): Promise<void> {
        await loadFindForm(); 
        await loadInsertForm();
    }
</script>
<h1 class="">Welcome to Data Editing page</h1>
<main>
    <table class="">
    
        <thead class="">
          <tr>
            {#each params as param}
                <th scope="" class="">
                    {param}
                </th>
            {/each}
          </tr>
        </thead>
        <tbody>
        {#each data as el}
          <tr class="">
            
            {#each params as param}
               <td class="">{el[param]}</td> 
            {/each}
          </tr>  
        {/each}
          
        </tbody>
      </table>

    <div>
        <div class="">

        </div>
        <div class="">
            <div class="">
                <button class="" on:click = {loadFindForm}>Create Form</button>
                <div class="form">
                    <div class="">
                        {#each findFormParams as param, index}
                            <label class="" style="text-align: center;" for="username">
                              {param}
                            </label>
                            <input class="" id="username" type="text" bind:value={findFormValues[index]}>
                        {/each}
                    </div>
                 <button class="" on:click = {find}>Find</button>
                </div>
            </div>

        <div class="form">
        <div class="">
            {#each insertFormParams as param, index}
                    <label class="" style="text-align: center;" for="username">
                      {param}
                    </label>
                    <input class="" id="username" type="text" bind:value={insertFormValues[index]}>
            {/each}

                  </div>
                <button class="" on:click={loadInsertForm}>Add data</button>
        <button class="" on:click={insert}>Insert</button>
        </div>
               
            </div>
            </div>
</main>
