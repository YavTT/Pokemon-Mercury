<script>
  import "../../styles/style.css";
  import "../../styles/game.css";
  import Overworld from "./Overworld.svelte";
</script>

<header class="fundoheader">
  <div class="container">
    <h1>Pokémon Mercury</h1>

    <nav>
      <ul class="ul">
        <li><a href="/">HOME</a></li>
        <li><a href="/about">ABOUT</a></li>
      </ul>
    </nav>
  </div>
</header>

<div class="game-container">
<Overworld />
</div>
<div class ="HowToPlay">
  <h1>How To Play</h1>
  <p>W - Walk to Up</p>
  <p>S - Walk to Down</p>
  <p>D - Walk to Right</p>
  <p>A - Walk to Left</p>
  <p>Click - Select</p>
  <p>Get Close to have an Interction</p>
</div>
