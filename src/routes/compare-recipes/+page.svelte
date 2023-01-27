
<div class="layout"> 

    <div class="leftBox">
        <div class="miniTitle">Found Recipes ({foundRecipesCount})</div>
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
            <button class="btn compareMatchingRecpies" on:click={() => {clickCompareRecipes()}}> Compare with Found Recipes</button>
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
    import { recipes as _recipes } from "../../data/recipes.svelte";
    import { trials, trials as _past_trials } from "../../data/trials.svelte";

    /**
     * 

     * 
    */

    /**
     * 
     * "recipes" = [
     *  "112233",
     *  "112244",
     *  "112255",
     * ]
     * 
     * 
    */

    let trialsText = "";
    let trialsPlaceholderText = `51A311\n41A311\n31A311`;
    let recipes:any = [];
    
    let outputList:{value:string, value2:string, charMatches:number}[] = [];
    let matchesCount = 0;

    // reacitiviy
    
    $: trialsEntriesCount = calculateNumberOfLines(trialsText);
    $: foundRecipesText = _recipes.join("\n");
    $: foundRecipesCount = _recipes.length;


    onMount( async () => {
        recipes = _recipes;
        //console.log(recipes);
    });




    /** Events */

    const clickCompareRecipes = () => {

        let _trials = trialsText.split("\n");
        let matches = compareRecipes(_trials, recipes);
        outputList = matches;

        console.log("matches", matches);

    }


    /** Functions */

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
     * compare both lists and check if 4 of 6 digits are matching position and value 
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
                    if (numberOfMatches >= 4) {
    
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
    

    .btn {
        width: 100%;
        height: 50px;
        background-color: #000;
        color: #fff;
        border: none;
        border-radius: 5px;
        cursor: pointer;

        /* style this button as a nice round blue submit button */
        background-color: #397ebf;
        color: white;
        padding: 14px 20px;
        margin: 8px 0;
        border: none;
        border-radius: 4px;
        cursor: pointer;

        margin-top: 10px;
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

</style>



