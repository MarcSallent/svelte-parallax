<script>
  import { Parallax, ParallaxLayer } from '../src/index.js';

  let sections = $state(3);
</script>

<main>
  <h1>Svelte 5 Parallax Test</h1>

  <Parallax {sections}>
    <ParallaxLayer rate={-0.5} offset={0}>
      <div class="layer background">
        <h2>Background Layer (rate: -0.5)</h2>
        <p>This moves slower than scroll</p>
      </div>
    </ParallaxLayer>

    <ParallaxLayer rate={0} offset={0}>
      <div class="layer normal">
        <h2>Normal Layer (rate: 0)</h2>
        <p>This moves at normal scroll speed</p>
      </div>
    </ParallaxLayer>

    <ParallaxLayer rate={0.5} offset={1}>
      <div class="layer fast">
        <h2>Fast Layer (rate: 0.5)</h2>
        <p>This moves faster than scroll</p>
      </div>
    </ParallaxLayer>

    <ParallaxLayer rate={-0.3} offset={1.5}>
      <div class="layer slow">
        <h2>Slow Layer (rate: -0.3)</h2>
        <p>This moves slower in the opposite direction</p>
      </div>
    </ParallaxLayer>

    <ParallaxLayer rate={0.2} offset={2}>
      <div class="layer final">
        <h2>Final Layer (rate: 0.2)</h2>
        <p>Final section with slight parallax</p>
      </div>
    </ParallaxLayer>
  </Parallax>
</main>

<style>
  :global(body) {
    margin: 0;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  }

  main {
    width: 100%;
  }

  h1 {
    position: fixed;
    top: 20px;
    left: 20px;
    color: white;
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
    z-index: 1000;
    margin: 0;
  }

  .layer {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    padding: 40px;
    box-sizing: border-box;
  }

  .background {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  }

  .normal {
    background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
  }

  .fast {
    background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
  }

  .slow {
    background: linear-gradient(135deg, #43e97b 0%, #38f9d7 100%);
  }

  .final {
    background: linear-gradient(135deg, #fa709a 0%, #fee140 100%);
  }

  .layer h2 {
    color: white;
    font-size: 3rem;
    margin: 0 0 1rem 0;
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
  }

  .layer p {
    color: white;
    font-size: 1.5rem;
    text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.3);
  }
</style>

