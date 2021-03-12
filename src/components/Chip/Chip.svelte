<script>
  import { createEventDispatcher } from "svelte";
  import { scale } from "svelte/transition";
  import createRipple from "../Ripple/ripple.js";
  import utils, { ClassBuilder, filterProps } from "../../utils/classes.js";

  import Icon from "../Icon";



  export let removable = false;
  export let icon = "";
  export let outlined = false;
  export let selected = false;
  export let selectable = true;
  export let color = "primary";
  export let remove = "";
  export let add = "";
  export let replace = {};

  $: ripple = createRipple(color);

  let value = true;

  const dispatch = createEventDispatcher();

  function close() {
    dispatch("close");
    value = false;
  }

  function select() {
    if (!selectable) return;
    selected = true;
  }

  function deSelect() {
    if (!selectable) return;
    selected = false;
  }

  const { bg, txt, border } = utils(color);

  const cb = new ClassBuilder();

  $: classes = cb
    .flush()
    .add('relative overflow-hidden flex items-center rounded-full px-2 py-1')
    .add('bg-transparent border', outlined)
    .add('border-gray-400 border-solid hover:bg-gray-50 dark-hover:bg-dark-400 bg-gray-300 dark:bg-dark-600', !selected)
    .add(`${border()} dark:${border(800)} ${txt()} ${bg(100)} hover:${bg(50)}`, selected)
    .remove(remove)
    .replace(replace)
    .add(add)
    .get();

  const props = filterProps([
    'removable',
    'icon',
    'outlined',
    'selected',
    'selectable',
    'color',
  ], $$props);

  $: iconClass = selected ? `hover:${bg(300)} ${bg(400)}` : "hover:bg-gray-400 bg-gray-500 dark:bg-gray-800";

   $: c = cb
      .flush()
      .add($$props.class)
      .get();
</script>

<style>
  .p-1\/2 {
    padding: 0.125rem;
  }
  .move-icon {
    top: 50%;
    transform: translate(-50%, -50%);
  }
</style>

{#if value}
  <span class="{c} mx-1 inline-block relative" out:scale={{ duration: 100 }}>
    <button
      class={classes}
      on:click
      use:ripple
      {...props}
      on:click={select} on:blur={deSelect}>
      {#if icon}
        <Icon small class={selected ? txt(400) : 'text-gray-600'}>
          {icon}
        </Icon>
      {/if}
      <span class={removable ? 'pl-2 pr-6 text-sm' : 'px-2 text-sm'}>
        <slot />
      </span>
    </button>
    {#if removable}
      <span
        class="absolute right-0 move-icon rounded-full p-1/2 inline-flex items-center cursor-pointer {iconClass}"
        on:click|stopPropagation={close}>
        <Icon class="text-white dark:text-white" xs>clear</Icon>
      </span>
    {/if}
  </span>
{/if}
