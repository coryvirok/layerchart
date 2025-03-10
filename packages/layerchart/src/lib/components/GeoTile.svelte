<script lang="ts">
  import { onDestroy } from 'svelte';
  // @ts-expect-error
  import { tile as d3Tile } from 'd3-tile';

  import { getRenderContext } from './Chart.svelte';
  import { chartContext } from './ChartContext.svelte';
  import { getCanvasContext } from './layout/Canvas.svelte';
  import { geoContext } from './GeoContext.svelte';
  import Group from './Group.svelte';
  import TileImage from './TileImage.svelte';

  export let url: (x: number, y: number, z: number) => string;
  export let zoomDelta = 0;
  export let tileSize = 256;
  export let disableCache = false;
  export let debug = false;

  const { containerWidth, containerHeight, padding } = chartContext();
  const geo = geoContext();

  $: center = $geo([0, 0]) ?? [0, 0];

  $: tile = d3Tile()
    .size([$containerWidth, $containerHeight])
    .translate([center[0] + $padding.left, center[1] + $padding.top])
    .scale($geo.scale() * 2 * Math.PI)
    .tileSize(tileSize)
    .zoomDelta(zoomDelta);

  $: tiles = tile();
  $: ({
    translate: [tx, ty],
    scale,
  } = tiles);

  const renderContext = getRenderContext();
  const canvasContext = getCanvasContext();

  function render(ctx: CanvasRenderingContext2D) {
    tiles.forEach(([x, y, z]: number[]) => {
      const image = new Image();
      image.onload = () => {
        ctx.drawImage(image, (x + tx) * scale, (y + ty) * scale, scale, scale);
      };
      image.src = url(x, y, z);
    });
  }

  $: if (renderContext === 'canvas') {
    tile;
    canvasContext.invalidate();
  }

  let canvasUnregister: ReturnType<typeof canvasContext.register>;
  $: if (renderContext === 'canvas') {
    canvasUnregister = canvasContext.register({ name: 'GeoTile', render });
  }

  onDestroy(() => {
    if (renderContext === 'canvas') {
      canvasUnregister();
    }
  });
</script>

{#if renderContext === 'svg' && url}
  <slot {tiles}>
    <Group x={-$padding.left} y={-$padding.top}>
      {#each tiles as [x, y, z] (url(x, y, z))}
        <TileImage {url} {x} {y} {z} {tx} {ty} {scale} {disableCache} {debug} />
      {/each}
    </Group>
  </slot>
{/if}
