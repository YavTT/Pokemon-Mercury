

<script lang="ts">
  import { goto } from '$app/navigation';
  import { onMount, onDestroy } from 'svelte';

  // Definições de tipos para o contexto e elementos do canvas
  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D | null = null;

  // Cria uma nova imagem
let heroImage = new Image();
heroImage.src = '../../images/characters/people/NPC_198_Lucas.png';
let backgroundImage = new Image();
backgroundImage.src = '../../images/maps/cidade01_TESTE.png';
let SansImage = new Image();
SansImage.src = '../../images/characters/people/sansfront.png';
let dialogueImage = new Image();
dialogueImage.src = '../../images/dialogo.png';
let BossImage = new Image();
BossImage.src = '../../images/characters/people/boss.png'
let BalaoTexto = new Image();
BalaoTexto.src = '../../images/CaixaTexto.png'
 
// Array bidimensional que representa o mapa de tiles
const tileMap: number[][] = [
    [0, 1, 2, 0],
    [1, 2, 0, 1],
    [2, 0, 1, 2],
    [0, 1, 2, 0]
  ];

  // Coordenadas dos NPC
  let sansX: number = 12;
  let sansY: number = 11;

  //let bossX: number = 12;
  //let bossY: number = 11;

  // Variaveis para corte de NPC
  let CorteX: number = 0;
  let CorteY: number = 0

  // Coordenadas do herói
  let heroX: number = 20;
  let heroY: number = 5;
  const heroSpeed = 0.3; // Velocidade do herói
  const tileSize = 16; // Tamanho do tile em pixels
  const cutWidth = 64;
  const cutHeight = 64; 

  //variaveis para o corte da imagem
  let sourceX = 0
  let sourceY = 0

  // Imagens carregadas
  let mapImage: HTMLImageElement | null = null;

  let interactionMessage: string | null = null

  // Função para carregar uma imagem e retorná-la como uma Promise
  function loadImage(src: string): Promise<HTMLImageElement> {
    return new Promise((resolve, reject) => {
      const image = new Image();
      image.onload = () => resolve(image);
      image.onerror = () => reject(new Error(`Failed to load image at ${src}`));
      image.src = src;
    });
  }

  // Função para desenhar tudo no canvas
  async function draw(): Promise<void> {
    if (!ctx || !mapImage || !heroImage || !SansImage || !dialogueImage || !BossImage || !BalaoTexto) {
      console.error("Contexto do canvas ou imagens não estão disponíveis.");
      return;
    }

    // Limpar o canvas antes de desenhar
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Desenhar o fundo do mapa usando o array bidimensional
    for (let y = 0; y < tileMap.length; y++) {
      for (let x = 0; x < tileMap[y].length; x++) {
        let tile = tileMap[y][x];
        let tileX = x * tileSize;
        let tileY = y * tileSize;

        // Aqui, você pode usar a imagem de fundo como textura para os tiles
        ctx.drawImage(
              backgroundImage, 
              tileX, tileY, tileSize, tileSize, // Área do tile na imagem
              tileX, tileY, tileSize, tileSize // Posição no canvas
        )
      }
    // Desenhar o mapa de fundo
    ctx.drawImage(mapImage, -10, -10);

    // Desenhar NPCs
    ctx.drawImage(
      SansImage,
      CorteX, CorteY, 128, 128,
      sansX * tileSize -8, sansY * tileSize -18,
      32, 32
    )

    //tx.drawImage(
      //BossImage,
      //CorteX, CorteY, 62, 68,
      //bossX * tileSize - 8, bossY * tileSize -18,
      //32, 32
    //)
    
    
    // Desenhar o herói
    ctx.drawImage(
      heroImage,
      sourceX, sourceY, cutWidth, cutHeight, // Corte da imagem
      heroX * tileSize - 8, heroY * tileSize - 18, // Posição no canvas
      32, 32 // Tamanho
    );
    //Puxar evento se interação existir
    if (interactionMessage) {
      goto("/game/sans")
    }
    checkInteraction()
  }
}

// Função para atualizar a posição do herói com base na tecla presssionada
function handleKeyDown(event: KeyboardEvent): void {
  switch (event.key) {
    case 'd':
      heroX = Math.min(heroX + heroSpeed, Math.floor(canvas.width / tileSize) - 1); // Limitar dentro do canvas
      sourceY = 128 // Atualiza o corte Verticalmente
      sourceX += 64; // Atualiza o corte horizontalmente
        if(sourceX >= heroImage.width){
          sourceX = 0
        }
        break;
        case 'a':
          heroX = Math.max(heroX - heroSpeed, 0); // Limitar dentro do canvas
          sourceY = 64 // Atualiza o corte Veticalmente
          sourceX += 64; // Atualiza o corte horizontalmente
          if(sourceX >= heroImage.width){
            sourceX = 0
          }
          break;
          case 's':
            heroY = Math.min(heroY + heroSpeed, Math.floor(canvas.height / tileSize) - 1); // Limitar dentro do canvas
            sourceY = 0 //atualiza o Corte verticalmente
            sourceX += 64; // Atualiza o corte verticalmente
            if(sourceX >= heroImage.width){
              sourceX = 0
        }
        break;
        case 'w':
          heroY = Math.max(heroY - heroSpeed, 0); // Limitar dentro do canvas
        sourceY = 192; // Atualiza o corte verticalmente
        sourceX += 64 // Atualiza o corte Horizoltamente
        if(sourceX >= heroImage.width){
          sourceX = 0
        }
        break;
        case 'e':
          InteractWithSans();
          break;
        }
        draw();
      }
  // função para interagir com o Sans
  function checkInteraction(){
    const distanceX = Math.abs(heroX - sansX);
    const distanceY = Math.abs(heroY - sansY);
    
    if (distanceX < 3 && distanceY < 3) {
      interactionMessage = "Pressione 'e' para interagir com Sans";
    } else {
      interactionMessage = null;
    }
  }

  function InteractWithSans(){
    const distanceX = Math.abs(heroX - sansX);
    const distanceY = Math.abs(heroY - sansY);
    
    if (distanceX < 3 && distanceY < 3) {
      interactionMessage = "Esta um belo dia la fora.";
      setTimeout(() => interactionMessage = null, 3000)
    }
  }

  // Montagem do componente
  onMount(async () => {
    ctx = canvas.getContext("2d");
    if (ctx) {
      ctx
      try {
        // Carregar as imagens
        mapImage = await loadImage("../../images/maps/cidade01_TESTE.png");
        heroImage = await loadImage("../../images/characters/people/NPC_198_Lucas.png");
        SansImage = await loadImage("../../images/characters/people/sansfront.png")
        dialogueImage = await loadImage("../../images/dialogo.png")
        BossImage = await loadImage("../../images/characters/people/boss.png")
        BalaoTexto = await loadImage("../../images/CaixaTexto.png")
        
        draw(); // Desenha a primeira vez

        // Adicionar o listener de eventos de teclado
        window.addEventListener('keydown', handleKeyDown);
      } catch (error) {
        console.error("Erro ao carregar imagens:", error);
      }
    } else {
      console.error("Não foi possível obter o contexto do canvas.");
    }
  });

  // Limpeza ao desmontar o componente
  onDestroy(() => {
    window.removeEventListener('keydown', handleKeyDown);
  });
</script>

<canvas bind:this={canvas} class="game-canvas" width="500" height="300"></canvas>