<script lang="ts">
  // Svelte somehow removes color on initial render
  // 👉🏽 https://github.com/sveltejs/kit/issues/10501

  import { type Oklch } from 'culori/fn';
  import { convertToOklch, formatToHex } from '../utils/colors.ts';
  import ColorSelector from './dev/ColorSelector.svelte';
  import ShadesLayout from './ShadesLayout.svelte';
  import SupportedColors from './SupportedColors.svelte';

  let color = '#ff7000';
  let colorHex = color;
  let colorOklch: Oklch;
  let errorColor: boolean = false;

  $: {
    color;

    const oklchColor = convertToOklch(color);

    if (oklchColor) {
      colorOklch = oklchColor;
      colorHex = formatToHex(oklchColor);
      errorColor = false;
    } else {
      errorColor = true;
    }
  }

  function handleChange(e: Event) {
    const target = e.target as HTMLInputElement;
    const ogColor = target.value;
    color = ogColor;
  }
</script>

<article>
  <SupportedColors />
  <ColorSelector bind:color={color} />

  <form action="">
    <label>
      Pick your color
      <input type="color" on:input={handleChange} bind:value={colorHex} />
    </label>

    <label>
      ... or write it
      <input class="text-input" type="text" on:input={handleChange} bind:value={color} />
    </label>
  </form>

  {#if !errorColor}
    <p style={`background-color: ${color};`}>Non-transformed selected color</p>
  {/if}

  {#if errorColor}
    <h1>This color is not valid</h1>
  {/if}

  {#if colorOklch}
    <ShadesLayout color={colorOklch} />
  {/if}
</article>

<style>
  article {
    display: flex;
    flex-direction: column;
    gap: var(--size-spacing-1);
    align-items: center;
  }

  form {
    display: flex;
    flex-direction: column;
    border: 2px solid #5f5f5f;
    padding: 1rem;
  }

  label {
    display: flex;
    align-items: center;
    gap: 0.8rem;
  }

  .text-input {
    width: 100px;
  }

  /* 
   - L (lightness): 0-1. Black to white
   - C (chroma): 0-0.5.Saturation
   - Hue angle: 0-360
   - a (opacity): 0-1
  */
</style>
