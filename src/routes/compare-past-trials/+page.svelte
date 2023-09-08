
<div class="layout"> 

    <div class="leftBox">
        <div class="miniTitle">Past Trials ({pastTrialsCount})</div>
        <div class="content">
            {pastTrialsText} 
        </div>
    </div>

    <div class="box">

        <h1>Compare Past Trials</h1>
    
        <div class="area1">
            <div class="miniTitle">
                <span> Trials </span>
                <span> ({trialsEntriesCount})</span>
            </div>
            <textarea name="trials" placeholder={trialsPlaceholderText} cols="30" rows="10" bind:value={trialsText} />
            <button class="btn compareDuplicateTrials" on:click={() => {clickComparePastTrials()}}> Compare with Past Trials</button>
        </div>
    
        <div class="area2">
            <div class="miniTitle">
                <span> Matches ({matchesCount}) </span>
            </div>
            <div class="outputBox">
                {#each outputList as item}

                    <div>
                        <span>{item.value}</span>
                        <span class="dd">(full-matches: <span class={`cc c${item.fullMatches.toString()}`}>{item.fullMatches.toString()}</span>)</span>
                    </div>
                {/each}
            </div>
        </div>
    
    </div>

</div>





<script lang="ts">

    import { onMount } from "svelte";
    import { recipes_6 as _recipes } from "../../data/recipes.svelte";
    import { trials as _past_trials } from "../../data/trials.svelte";

    // data

    let trialsText = "";
    let trialsPlaceholderText = `51A311\n41A311\n31A311`;
    let recipes:any = [];
    
    let outputList:{value:string, fullMatches:number}[] = [];
    let matchesCount = 0;


    // reacitiviy
    
    $: trialsEntriesCount = calculateNumberOfLines(trialsText);
    $: pastTrialsText = _past_trials.join("\n");
    $: pastTrialsCount = _past_trials.length;



    onMount( async () => {
        recipes = _recipes;
    });




    /** Events */

    const clickComparePastTrials = () => {

        let _trials = trialsText.split("\n");
        let matches = comparePastTrials(_trials, _past_trials);
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
     * Takes 2 lists "trials" and "trials-past", each list contains 6-digit-strings
     * compare both lists and check if they are the same
     * return a list of the trials that match
     */
    const comparePastTrials = (_trials:string[], _past_trials:string[]) =>  {
    
        let matches = [];
    
        loop1: 
            for (let i = 0; i < _trials.length; i++) {
                
            let matchesCount2 = 0;
                
            loop2: 
                for (let j = 0; j < _past_trials.length; j++) {
    
                    if (_past_trials[j] == _trials[i]) matchesCount2++;
   
                }

                matches.push({
                    value: _trials[i],
                    fullMatches: matchesCount2
                });

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
    .outputBox .dd {
        color:grey;
    }

    .miniTitle {
        font-weight: bold;
    margin-bottom: 5px;
    }

    .area1 {
        margin-top:20px;
    }

    .area2 {
        margin-top: 10px;
    }


    .area1, .area2 {
        width:100%;
    }

</style>



