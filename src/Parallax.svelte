<script>
  import { setContext, onMount } from 'svelte';
  import { spring } from 'svelte/motion';
  import { derived } from 'svelte/store';
  import { quadInOut } from 'svelte/easing';
  import { writableSet, contextKey, clamp } from './utils';
  import { scrollTo as svelteScrollTo } from './scroll-fork/svelteScrollTo.js';
  import 'focus-options-polyfill';

  // bind:this
  let container = $state();
  // bind:innerHeight
  let innerHeight = $state();

  // Create a state for scrollY
  let scrollY = $state(0);

  /** the number of sections the container spans */
  let { sections = 1,
    /** the height of a section, defaults to window.innerHeight */
    sectionHeight = undefined,
    /** spring config object */
    config = { stiffness: 0.017, damping: 0.26 },
    /** threshold of effect start/end when container enters/exits viewport */
    threshold = { top: 1, bottom: 1 },
    /** a function that receives a progress object: `{ progress: float, section: number }` */
    onProgress = undefined,
    /** a function that receives "scrollTop" -- the number of pixels scrolled between each threshold */
    onScroll = undefined,
    /** disable parallax effect, layers will be frozen at target position */
    disabled = false,
    ...restProps
  } = $props();

  // Create reactive values using $derived
  let top = $state(0);
  let height = $state(0);

  // spring store to hold scroll progress
  const progress = spring(undefined, { ...config, precision: 0.001 });

  // fake intersection observer - derived from reactive state
  const scrollTop = $derived.by(() => {
    const dy = scrollY - top;
    const min = 0 - height + height * threshold.top;
    const max = height * sections - height * threshold.bottom;
    return clamp(dy, min, max);
  });

  $effect(() => {
    if (onScroll) onScroll(scrollTop);
  });

  $effect(() => {
    if (onProgress) setProgress(scrollTop, height);
  });

  $effect(() => {
    if (onProgress) onProgress($progress ?? 0);
  });

  const setProgress = (scrollTop, height) => {
    if (height === 0) {
      progress.set(0);
      return;
    }
    const scrollHeight = height * sections - height;
    progress.set(clamp(scrollTop / scrollHeight, 0, 1));
  };

  // eventually filled with ParallaxLayer objects
  const layers = writableSet(new Set());

  // update ParallaxLayers from parent
  $effect(() => {
    $layers.forEach((layer) => {
      layer.setHeight(height);
    });
  });

  $effect(() => {
    $layers.forEach((layer) => {
      layer.setPosition(scrollTop, height, disabled);
    });
  });

  $effect(() => {
    if (height !== 0) setDimensions();
  });

  setContext(contextKey, {
    config,
    addLayer: (layer) => {
      layers.add(layer);
    },
    removeLayer: (layer) => {
      layers.delete(layer);
    },
  });

  onMount(() => {
    setDimensions();
  });

  const setDimensions = () => {
    height = sectionHeight ? sectionHeight : innerHeight;
    if (container) {
      top = container.getBoundingClientRect().top + window.pageYOffset;
    }
  };

  export function scrollTo(section, { selector = '', duration = 500, easing = quadInOut } = {}) {
    const scrollTarget = top + height * (section - 1);

    const focusTarget = () => {
      document.querySelector(selector).focus({ preventScroll: true });
    };
    // don't animate scroll if disabled
    if (disabled) {
      window.scrollTo({ top: scrollTarget });
      selector && focusTarget();
      return;
    }

    svelteScrollTo({
      y: scrollTarget,
      duration,
      easing,
      onDone: selector ? focusTarget : () => {},
    });
  }
</script>

<svelte:window
  bind:scrollY
  bind:innerHeight
  onresize={() => setTimeout(setDimensions, 0)}
/>

<div
  {...restProps}
  class="parallax-container {restProps.class ? restProps.class : ''}"
  style="
    height: {height * sections}px;
    {restProps.style ? restProps.style : ''};
  "
  bind:this={container}
>
  <slot />
</div>

<style>
  .parallax-container {
    position: relative;
    overflow: hidden;
    box-sizing: border-box;
  }
</style>

