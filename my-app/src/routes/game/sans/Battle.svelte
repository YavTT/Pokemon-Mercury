

    <script lang="ts">
        import { onMount } from 'svelte';

        let canvas: HTMLCanvasElement;
        let ctx: CanvasRenderingContext2D | null = null;
        let cenario = new Image()
        cenario.src = "../../../images/BatalhaGrama.png"
        let salamence = new Image()
        salamence.src = "../../../images/papyrusboss.png"
        let mewtwo = new Image()
        mewtwo.src = "../../../images/mewtwo.png"
    
        // Função para carregar uma imagem e retorná-la como uma Promise
        function loadImage(src: string): Promise<HTMLImageElement> {
            return new Promise((resolve, reject) => {
                const image = new Image();
                image.onload = () => resolve(image);
                image.onerror = (event) => {
                    console.error(`Failed to load image at ${src}., event`);
                    reject(new Error(`Failed to load image at ${src}`));
                };
                image.src = src;
            });
        }
    
        // Desenhar tudo no canvas
        async function draw(): Promise<void> {
            if (!ctx || !cenario || !salamence || !mewtwo) {
                console.error("Contexto do canvas ou imagem não estão disponíveis.");
                return;
            }
    
            // Limpar o canvas antes de desenhar
            ctx.clearRect(0, 0, canvas.width, canvas.height);
    
            //Desenhar o mapa de fundo
            ctx.drawImage(cenario, 125, 0);

            //Desenhar o Heroi
            ctx.drawImage(mewtwo, 150, 67);
            
            // Desenhar o oponente
            const SalamanceW = 90
            const SalamanceH = 90
            ctx.drawImage(salamence, 280, 10, SalamanceW, SalamanceH);
        }
    
        // Montagem do componente
        onMount(async () => {
            ctx = canvas.getContext("2d");
    
            if (ctx) {
                try {
                    // Carregar a imagem
                    cenario = await loadImage("../../../images/BatalhaGrama.png");
                    salamence = await loadImage("../../../images/papyrusboss.png")
                    mewtwo = await loadImage("../../../images/mewtwo.png")
                    
                    // Desenhar a imagem
                    draw();
                } catch (error) {
                    console.error("Erro ao carregar a imagem:", error);
                }
            } else {
                console.error("Não foi possível obter o contexto do canvas.");
            }
        })
        
        </script>
        <canvas bind:this={canvas} class="game-canvas" width="500" height="300"></canvas>

