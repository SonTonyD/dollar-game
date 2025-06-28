<script>
  import Freedom from './components/Freedom.svelte';
  import Game from './components/Game.svelte';
  import Simulator from './components/Simulator.svelte';
  let currentPage = "menu"; // 'menu' | 'game' | 'simulator'

  

  

</script>

{#if currentPage === 'menu'}
  <h1>🏦 Menu principal</h1>
  <button on:click={() => currentPage = 'game'}>🎮 Lancer le jeu éducatif</button>
  <button on:click={() => currentPage = 'simulator'}>📈 Simulateur d’épargne</button>
  <button on:click={() => currentPage = 'freedom'}>🗽 Liberté Financière</button>
{/if}

{#if currentPage === 'game'}
  <Game></Game>
{/if}

<!-- SIMULATEUR EPARGNE-->
{#if currentPage === 'simulator'}
  <Simulator></Simulator>
{/if}

{#if currentPage === 'freedom'}
  <Freedom></Freedom>
{/if}






