<script lang="ts">
  import "../../../styles/style.css"
  import "../../../styles/game.css";
  import Battle from "./Battle.svelte";
  import "../../../styles/battle.css"
  import { goto } from "$app/navigation";
  

  let LifeOponente: any;
  let LifeHero: any;
  
// Batalha

  
  
  // Dados do heroi 
  let HeroName : string = "Mewtwo"
  let HeroHealth : number = 300
  let HeroAttack : number = 30
  let HeroDefense : number = 10
  
  //Dados do Oponente
  let VillainName : string = "Papyrus"   
  let VillainHealth : number = 300
  let VillainAttack : number = 27
  let VillainDefense : number = 5
  
  //variaveis para barra de vida
  let heroHealthWidth: number = HeroHealth;
  let villainHealthWidth: number = VillainHealth;

  //texto dos ataques
  let attackMensageHero: string = ""
  let attackMensageVillain: string = ""

  //Metodo de ataque
  function DanoHeroi(): void{
    const damage = Math.max(HeroAttack - VillainDefense,0)
        VillainHealth -= damage
        villainHealthWidth = VillainHealth;
        attackMensageHero =`${HeroName} attacks ${VillainName} for ${damage} damage.`
      }
      
  function DanoVillain(): void{
        const damage = Math.max(VillainAttack - HeroDefense,0)
        HeroHealth -= damage
        heroHealthWidth = HeroHealth;
        attackMensageVillain =`${VillainName} attacks ${HeroName} for ${damage} damage.`
    }
    
    // Verificar se está vivo
    function HeroIsAlive(): boolean{
    return HeroHealth > 0
  }
    
  function VillainIsAlive(): boolean{
        return VillainHealth > 0
      }
      
      //Batalha
      function battle(): void{
        if(HeroIsAlive() && !VillainIsAlive()){
            alert(`${HeroName} Wins!`)
            goto("/game")
          } else if (VillainIsAlive() && !HeroIsAlive()){
            alert(`${VillainName} Wins!`)
            goto("/game")
        }
      }
  
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

<div class ="battle-container">
  <Battle />
</div>
<div bind:this={LifeHero} class="LifeHero" style="width: {heroHealthWidth}px;">
</div>
<div bind:this={LifeOponente} class ="LifeOponente" style="width: {villainHealthWidth}px;">
</div>
<div class ="Hero">
  <p>{attackMensageHero}</p>
  <p>{attackMensageVillain}</p>
</div>
<button class ="Attack" on:click={() => DanoHeroi()} on:click={() => DanoVillain()} on:click={() => battle()}>
  <h1 class = "texto">ATTACK</h1>
</button>