<script lang="ts">
  import { format } from 'date-fns';

  import { BarChart, Tooltip } from 'layerchart';

  import Preview from '$lib/docs/Preview.svelte';
  import { createDateSeries } from '$lib/utils/genData.js';

  const data = createDateSeries({
    count: 30,
    min: 20,
    max: 100,
    value: 'integer',
    keys: ['value', 'baseline'],
  });
  const negativeData = createDateSeries({ count: 30, min: -20, max: 50, value: 'integer' });
</script>

<h1>Examples</h1>

<h2>Basic</h2>

<Preview {data}>
  <div class="w-[124px] h-[18px]">
    <BarChart
      {data}
      x="date"
      y="value"
      axis={false}
      grid={false}
      bandPadding={0.1}
      props={{ bars: { radius: 1, strokeWidth: 0 } }}
    />
  </div>
</Preview>

<h2>Basic within a paragraph</h2>
<Preview {data}>
  <div>
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Etiam pretium, ligula ac sollicitudin
      ullamcorper, leo justo pretium tellus, at gravida ex quam et orci.
      <span class="w-[124px] h-[18px] inline-block">
        <BarChart
          {data}
          x="date"
          y="value"
          axis={false}
          grid={false}
          bandPadding={0.1}
          props={{ bars: { radius: 1, strokeWidth: 0 } }}
        />
      </span> Sed ipsum justo, facilisis id tempor hendrerit, suscipit eu ipsum. Mauris ut sapien quis
      nibh volutpat venenatis. Ut viverra justo varius sapien convallis venenatis vel faucibus urna.
    </p>
  </div>
</Preview>

<h2>Basic negative data</h2>

<Preview data={negativeData}>
  <div class="w-[124px] h-[18px]">
    <BarChart
      data={negativeData}
      x="date"
      y="value"
      axis={false}
      grid={false}
      bandPadding={0.1}
      props={{ bars: { radius: 1, strokeWidth: 0 } }}
    />
  </div>
</Preview>

<h2>Fixed position tooltip</h2>

<Preview {data}>
  <div class="w-[124px] h-[18px]">
    <BarChart
      {data}
      x="date"
      y="value"
      axis={false}
      grid={false}
      bandPadding={0.1}
      props={{ bars: { radius: 1, strokeWidth: 0 } }}
    >
      <svelet:fragment slot="tooltip" let:width>
        <Tooltip.Root
          class="text-xs"
          contained={false}
          variant="none"
          y={-10}
          x={width + 8}
          let:data
        >
          <div class="whitespace-nowrap">
            {format(data.date, 'eee, MMM do')}
          </div>
          <div class="font-semibold">
            {data.value}
          </div>
        </Tooltip.Root>
      </svelet:fragment>
    </BarChart>
  </div>
</Preview>

<h2>Basic within a paragraph with Tooltip and Highlight</h2>
<Preview {data}>
  <div>
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Etiam pretium, ligula ac sollicitudin
      ullamcorper, leo justo pretium tellus, at gravida ex quam et orci.
      <span class="w-[124px] h-[18px] inline-block">
        <BarChart
          {data}
          x="date"
          y="value"
          axis={false}
          grid={false}
          bandPadding={0.1}
          props={{ bars: { radius: 1, strokeWidth: 0 } }}
        >
          <svelte:fragment slot="tooltip" let:height>
            <Tooltip.Root class="text-xs" contained={false} y={height + 4} xOffset={0} let:data>
              <Tooltip.Header>{format(data.date, 'eee, MMM do')}</Tooltip.Header>
              <Tooltip.List>
                <Tooltip.Item label="value" value={data.value} />
              </Tooltip.List>
            </Tooltip.Root>
          </svelte:fragment>
        </BarChart>
      </span> Sed ipsum justo, facilisis id tempor hendrerit, suscipit eu ipsum. Mauris ut sapien quis
      nibh volutpat venenatis. Ut viverra justo varius sapien convallis venenatis vel faucibus urna.
    </p>
  </div>
</Preview>
