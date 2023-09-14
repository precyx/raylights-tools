<svelte:head>
    <title>Raylights Tools</title> 
</svelte:head>

<nav>
    <a href="/">Start</a>
    <a href="/compare-recipes">Compare Recipes</a>
    <a href="/compare-past-trials">Compare Past Trials</a>
  </nav>
  
  <slot></slot>


  <style>
    nav {
        display: flex;
        background: #ebebeb;
        padding: 8px 0;
    }

    a {
        font-family: arial;
        padding:8px 15px;
        text-decoration: none;
        color:#397ebf
    }
    a:hover {
        color:#000;
    }

    
  </style>