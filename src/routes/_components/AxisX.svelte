<!--
  @component
  Generates an SVG x-axis. This component is also configured to detect if your x-scale is an ordinal scale. If so, it will place the markers in the middle of the bandwidth.
 -->
<script>
  import { getContext } from 'svelte';

  const { width, height, xScale, yRange } = getContext('LayerCake');

  /**
   * @typedef {Object} Props
   * @property {boolean} [tickMarks=false] - Show a vertical mark for each tick.
   * @property {boolean} [gridlines=true] - Show gridlines extending into the chart area.
   * @property {number} [tickMarkLength=6] - The length of the tick mark.
   * @property {boolean} [baseline=false] - Show a solid line at the bottom.
   * @property {boolean} [snapLabels=false] - Instead of centering the text labels on the first and the last items, align them to the edges of the chart.
   * @property {(d: any) => string} [format=d => d] - A function that passes the current tick value and expects a nicely formatted value in return.
   * @property {number|Array<any>|Function} [ticks] - If this is a number, it passes that along to the [d3Scale.ticks](https://github.com/d3/d3-scale) function. If this is an array, hardcodes the ticks to those values. If it's a function, passes along the default tick values and expects an array of tick values in return. If nothing, it uses the default ticks supplied by the D3 function.
   * @property {number} [tickGutter=0] - The amount of whitespace between the start of the tick and the chart drawing area (the yRange min).
   * @property {number} [dx=0] - Any optional value passed to the `dx` attribute on the text label.
   * @property {number} [dy=12] - Any optional value passed to the `dy` attribute on the text label.
   */

  /** @type {Props} */
  let {
    tickMarks = false,
    gridlines = true,
    tickMarkLength = 6,
    baseline = false,
    snapLabels = false,
    format = d => d+"%",
    ticks = undefined,
    tickGutter = 0,
    dx = 0,
    dy = 12
  } = $props();

  /** @param {number} i
   *  @param {boolean} sl */
  function textAnchor(i, sl) {
    if (sl === true) {
      if (i === 0) {
        return 'start';
      }
      if (i === tickVals.length - 1) {
        return 'end';
      }
    }
    return 'middle';
  }

  let tickLen = $derived(tickMarks === true ? (tickMarkLength ?? 6) : 0);

  let isBandwidth = $derived(typeof $xScale.bandwidth === 'function');

  /** @type {Array<any>} */
  let tickVals = $derived(
    Array.isArray(ticks)
      ? ticks
      : isBandwidth
        ? $xScale.domain()
        : typeof ticks === 'function'
          ? ticks($xScale.ticks())
          : $xScale.ticks(ticks)
  );

  let halfBand = $derived(isBandwidth ? $xScale.bandwidth() / 2 : 0);
</script>

<g class="axis x-axis" class:snapLabels>
  {#each tickVals as tick, i (tick)}
    {#if baseline === true}
      <line class="baseline" y1={$height} y2={$height} x1="0" x2={$width} />
    {/if}

    <g class="tick tick-{i}" transform="translate({$xScale(tick)},{Math.max(...$yRange)})">
      {#if gridlines === true}
        <line class="gridline" x1={halfBand} x2={halfBand} y1={-$height} y2="0" />
      {/if}
      {#if tickMarks === true}
        <line
          class="tick-mark"
          x1={halfBand}
          x2={halfBand}
          y1={tickGutter}
          y2={tickGutter + tickLen}
        />
      {/if}
      <text x={halfBand} y={tickGutter + tickLen} {dx} {dy} text-anchor={textAnchor(i, snapLabels)}
        >{format(tick)}</text
      >
    </g>
  {/each}
</g>

<style>
  .tick {
    font-size: 11px;
  }

  line,
  .tick line {
    stroke: #aaa;
    stroke-dasharray: 2;
  }

  .tick text {
    fill: #666;
  }

  .tick .tick-mark,
  .baseline {
    stroke-dasharray: 0;
  }
  /* This looks slightly better */
  .axis.snapLabels .tick:last-child text {
    transform: translateX(3px);
  }
  .axis.snapLabels .tick.tick-0 text {
    transform: translateX(-3px);
  }
</style>