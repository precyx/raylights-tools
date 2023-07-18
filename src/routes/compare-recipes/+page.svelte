
<div class="layout"> 

    <div class="leftBox">
        <div class="miniTitle">Found Recipes ({foundRecipesCount})</div> 
        <div>
            {#each settings_recipeTypes as item}
                <button class="tabButton" class:active={settings_recipeType==item} on:click={() => setSettingsRecipeType(item)}>
                    {item}
                </button>
            {/each}                    
        </div>
        <div class="content">
            {foundRecipesText}
        </div>
    </div>

    <div class="box">

        <h1>Compare Found Recipes</h1>

        <div class="area1">
            <div class="miniTitle">
                <span> Trials </span>
                <span> ({trialsEntriesCount})</span>
            </div>
            <textarea name="trials" placeholder={trialsPlaceholderText} cols="30" rows="10" bind:value={trialsText} />
            <div class="settingsBox">
                <label for="numberOfMatches">Number of Matches</label>            
                <div>
                    {#each settings_numberOfMatchesOptions as item}
                        <button class="tabButton" class:active={settings_numberOfMatches==item} on:click={() => setSettingsNumberOfMatches(item)}>
                            {item}
                        </button>
                    {/each}                    
                </div>
            </div>
            <div class="buttonBox">
                <button class="btn compareMatchingRecpies" on:click={() => {clickCompareRecipes()}}> Compare Recipes</button>
            </div>
        </div>

        <div class="area2">
            <div class="miniTitle">
                <span> Matches ({matchesCount}) </span>
            </div>
            <div class="outputBox">
                {#each outputList as item}
                    <div>
                        <span class="">{item.value} </span>
                        <span class="value2">{item.value2}</span>
                        <span class="match">(char-matches: <span class={`cc c${item.charMatches.toString()}`}>{item.charMatches.toString()}</span>) </span>
                    </div>
                {/each}
            </div>
        </div>

    </div>

</div>



<script lang="ts">

    import { onMount } from "svelte";
    import { recipes_6, recipes_5, recipes_4, recipes_3 } from "../../data/recipes.svelte";
    import { trials, trials as _past_trials } from "../../data/trials.svelte";

    // data

    let trialsText = "";
    let trialsPlaceholderText = `51A311\n41A311\n31A311`;
    let recipes:any = [];
    
    let outputList:{value:string, value2:string, charMatches:number}[] = [];
    let matchesCount = 0;

    
    let settings_numberOfMatchesOptions = ["456", "3", "4", "5", "6", "3456", "23", "2"];
    let settings_numberOfMatches:string = "456";
    $: settings_numberOfMatchesArray = settings_numberOfMatches.split("").map((item) => parseInt(item));
    let settings_recipeTypes = ["3", "4", "5", "6"]
    let settings_recipeType = "6";
    let settings_recipeArrays = [recipes_3, recipes_4, recipes_5, recipes_6];

    // reacitiviy
    
    $: trialsEntriesCount = calculateNumberOfLines(trialsText);
    $: foundRecipesText = recipes.join("\n");
    $: foundRecipesCount = recipes.length;


    onMount( async () => {
        recipes = recipes_6;
    });




    /** Events */

    const clickCompareRecipes = () => {

        let _trials = trialsText.split("\n");
        let matches = compareRecipes(_trials, recipes);
        outputList = matches;

        console.log("matches", matches);

    }


    /** Functions */

    const setSettingsNumberOfMatches = (value:string) => {
        settings_numberOfMatches = value;
    }

    const setSettingsRecipeType = (value:string) => {
        settings_recipeType = value;
        recipes = settings_recipeArrays[settings_recipeTypes.indexOf(value)];
    }

    /**
     * calculate the number of lines in a text
     */
    const calculateNumberOfLines = (text:string) => {
        // exclude the first empty line
        if(text === "") return 0;
        return text.split("\n").length;
    }
    
    /**
    * compare 2 codes and return the number of matching digits
     */
    const compareCodes = (code1:string, code2:string) => {

        let matches = 0;
        for (let i = 0; i < code1.length; i++) {
            if (code1[i] === code2[i]) {
                matches++;
            }
        }

        return matches;
    }




    /**
     * take 2 lists "recipes" and "trials", each list contains 6-digit-strings
     * compare both lists and check if X of 6 digits are matching position and value 
     * return a list of strings with the matching trials
     */
    const compareRecipes = (_trials:string[], _recipes:string[]) => {

        matchesCount = 0;
            
        let matches = [];
        let isMatch = false;
    
        loop1: 
            for (let i = 0; i < _trials.length; i++) {
                
            isMatch = false;
                
            loop2: 
                for (let j = 0; j < _recipes.length; j++) {
    
                    if(isMatch) break;

                    let numberOfMatches = compareCodes(_trials[i], _recipes[j]);
                    if (settings_numberOfMatchesArray.includes(numberOfMatches)) {
    
                        // output the trials that match
                        matches.push({
                            value: _trials[i],
                            value2: _recipes[j],
                            charMatches: numberOfMatches
                        });
                        matchesCount++;
                        isMatch = true;

                    }
    
                }

                // output the trials that don't match
                if(!isMatch) {
                    matches.push({
                        value: _trials[i],
                        value2: "",
                        charMatches: 0
                    });
                }
            }
    
            return matches;
        
    }


</script>




<style>

    


    * {
        font-family: Arial;
    }

    h1 {
        margin:0;
    }

    label {
        color: grey;
        font-size: 12px;
    }

    .settingsBox {
        margin:3px 0;
        background: #f2f2f2;
        padding: 8px;
        border-radius: 6px;
    }
    .settingsBox label {
        display: block;
        margin-bottom: 5px;
    }

    .leftBox {
        width: 250px;
        margin-left: -270px;
        padding-right:20px;
    }

    .leftBox .content {
        font-family: monospace;
        height:85vh;
        overflow-y:scroll;
        white-space: pre-wrap;
        margin-top:5px;
    }
    

    .layout {
        display: flex;
        flex-direction:row;
        align-items: start;
        justify-content: center;

        padding-top:10px;
        
        width:950px;
        margin:0 auto;
        margin-top:20px;
        
    }

    .box {
        width:500px;
        display: flex;
        flex-flow: column;
        align-items: center;
        justify-content: center;
    }

    textarea {
        width: 100%;
        height: 100%;
        padding:5px;
        box-sizing: border-box;
        font-family: monospace;
    }
    
    .buttonBox {
        margin-top:10px;
        display:flex;
    }

    .btn {
        height: 45px;
        background-color: #000;
        color: #fff;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        background-color: #397ebf;
        color: white;
        padding: 14px 32px;
        margin: 8px 0;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        box-sizing: bo;
        margin: 0 auto;
        border-radius: 50px;
        font-size: 14px;
    }
    .btn:hover {
        background:#3497f4;
    }

    .outputBox {
        margin-top:15px;
        height:200px;
        white-space: pre-wrap;
        font-family: monospace;
    }
    .outputBox * {
        font-family: monospace;
    }
    .outputBox .cc {
        font-weight: bold;;
        color:rgb(223, 65, 104);
    }
    .outputBox .cc.c0 {
        color:green;
    }
    .outputBox .match {
        color:grey;
    }
    .outputBox .value2 {
        display: inline-block;
        width:60px;
    }

    .miniTitle {
        font-weight: bold;
    margin-bottom: 5px;
    }

    .area2 {
        margin-top: 10px;
    }

    .area1 {
        margin-top:20px;
    }


    .area1, .area2 {
        width:100%;
    }

    textarea {
        border: 1px solid #acacac;
        border-radius: 6px;
    }

    .tabButton {
        /* style as a small pressable button */
        /* use a soft secondary color */
        background-color: #f2f2f2;
        color: #000;
        padding: 5px 10px;
        margin-right: 8px;
        border: none;
        border-radius: 4px;
        cursor: pointer;
    }
    .tabButton:hover {
        background-color: #ddd;
    }
    .tabButton.active {
        background-color: #ccc;
    }


</style>



