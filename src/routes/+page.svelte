<script>
  import { LayerCake, Svg } from 'layercake';
  import { scaleBand } from 'd3-scale';

  import Bar from './_components/Bar.svelte';
  import AxisX from './_components/AxisX.svelte';
  import AxisY from './_components/AxisY.svelte';

  // This example loads csv data as json using @rollup/plugin-dsv
  import data from './_data/groups.csv';

  const xKey_1 = 'Refusal_1';
  const yKey_1 = 'Nationality_1';
  const xKey_2 = 'Refusal_2';
  const yKey_2 = 'Nationality_2';

  data
  .forEach(d => {
    d[xKey_1] = +d[xKey_1];
    d[xKey_2] = +d[xKey_2];
  })


</script>

<div class="chart-wrapper">
<div class="chart-container">
  <h1 style="padding: 10px 70px;">Nations Most Likely Refused</h1>
  <LayerCake
    padding={{ bottom: 20, top: 20, left: 120, right: 120 }}
    x={xKey_1}
    y={yKey_1}
    yScale={scaleBand().paddingInner(0.05)}
    xDomain={[0, 100]}
    {data}
  >
    <Svg>
      <AxisX tickMarks baseline snapLabels />
      <AxisY tickMarks gridlines={false} />
      <Bar />
    </Svg>
  </LayerCake>
</div> 

  <div class="chart-container">
    <div class="chart-container">
  <h1 style="padding: 10px 70px;">Nations Least Likely Refused</h1>
  <LayerCake
    padding={{ bottom: 20, top: 20, left: 120, right: 120 }}
    x={xKey_2}
    y={yKey_2}
    yScale={scaleBand().paddingInner(0.05)}
    xDomain={[0, 100]}
    {data}
  >
    <Svg>
      <AxisX tickMarks baseline snapLabels />
      <AxisY tickMarks gridlines={false} />
      <Bar />
    </Svg>
  </LayerCake>
</div>
  </div>
</div>





<style>
  /*
    The wrapper div needs to have an explicit width and height in CSS.
    It can also be a flexbox child or CSS grid element.
    The point being it needs dimensions since the <LayerCake> element will
    expand to fill it.
  */
  .chart-container {
    width: 100%;
    height: 250px;
  }
</style>