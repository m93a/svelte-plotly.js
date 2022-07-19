# Plotly.js for Svelte
This is an unofficial package that lets you efficiently use [plotly.js](https://plotly.com/javascript/) inside your Svelte or SvelteKit app.

## Usage

1. Install using `yarn add svelte-plotly.js` or `npm install svelte-plotly.js`.
2. Use in your app

```svelte
<script lang="ts">
  import Plot from 'svelte-plotly.js';

  const data = [
    {
      x: [1, 2, 3, 4, 5],
      y: [1, 2, 4, 8, 16]
    }
  ];
</script>

<Plot
  {data}
  layout={{
    margin: { t: 0 }
  }}
  fillParent='width'
  debounce={250}
/>
```

## Properties
| Prop      | Type        | Description
| --------- | ----------- | -------------
| **required `data`** | `Data[]`| array of trace data, see https://plot.ly/javascript/reference/
| `layout`  | `Partial<Layout>` | layout of the plot, see https://plot.ly/javascript/reference/#layout
| `config`  | `Partial<Config>` | configuration, see https://plot.ly/javascript/configuration-options/
| `class`   | `string`          | class that will be passed to the HTML element wrapping the plot
| `fillParent` | `boolean \| 'width' \| 'height'` | automatically resize the plot to fill the width and/or height of its parent element
| `debounce` | `number \| DebounceOptions` | debounce all changes to the plot
| `libPlotly` | `Plotly \| null \| undefined` | an alternative Plotly bundle to use; if undefined, it defaults to the `plotly.js-dist` package; if null, no plot will be drawn and no library will be downloaded
| `bind:element` | `HTMLDivElement` | the HTML element wrapping the plot
| `bind:plot` | `PlotlyHTMLElement` | the inner HTML element containing the plot
| `on:afterExport` | undefined | https://plotly.com/javascript/plotlyjs-events/
| `on:afterPlot` | undefined | https://plotly.com/javascript/plotlyjs-events/#afterplot-event
| `on:animated` | undefined | https://plotly.com/javascript/plotlyjs-events/#additional-events
| `on:animating` | undefined | https://plotly.com/javascript/plotlyjs-events/#additional-events
| `on:animatingFrame` | FrameAnimationEvent | https://plotly.com/javascript/plotlyjs-events/#additional-events
| `on:animationInterrupted` | undefined | https://plotly.com/javascript/plotlyjs-events/#additional-events
| `on:autoSize` | undefined | https://plotly.com/javascript/plotlyjs-events/#additional-events
| `on:beforeExport` | undefined | https://plotly.com/javascript/plotlyjs-events/
| `on:beforeHover` | PlotMouseEvent | https://plotly.com/javascript/plotlyjs-events/
| `on:beforePlot` | BeforePlotEvent | https://plotly.com/javascript/plotlyjs-events/
| `on:buttonClicked` | ButtonClickedEvent | https://plotly.com/javascript/plotlyjs-events/
| `on:click` | PlotMouseEvent | https://plotly.com/javascript/plotlyjs-events/#click-event
| `on:clickAnnotation` | ClickAnnotationEvent | https://plotly.com/javascript/plotlyjs-events/
| `on:deselect` | undefined | https://plotly.com/javascript/plotlyjs-events/#additional-events
| `on:doubleClick` | undefined | https://plotly.com/javascript/plotlyjs-events/#double-click-event
| `on:framework` | undefined | https://plotly.com/javascript/plotlyjs-events/
| `on:hover` | PlotMouseEvent | https://plotly.com/javascript/plotlyjs-events/#hover-event
| `on:legendClick` | LegendClickEvent | https://plotly.com/javascript/plotlyjs-events/#legend-click-events
| `on:legendDoubleClick` | LegendClickEvent | https://plotly.com/javascript/plotlyjs-events/#legend-click-events
| `on:react` | PlotUpdateEvent | https://plotly.com/javascript/plotlyjs-events/
| `on:redraw` | undefined | https://plotly.com/javascript/plotlyjs-events/#additional-events
| `on:relayout` | PlotRelayoutEvent | https://plotly.com/javascript/plotlyjs-events/#update-data
| `on:relayouting` | PlotRelayoutEvent | https://plotly.com/javascript/plotlyjs-events/#update-data
| `on:restyle` | PlotRestyleEvent | https://plotly.com/javascript/plotlyjs-events/#update-data
| `on:selected` | PlotSelectionEvent | https://plotly.com/javascript/plotlyjs-events/#select-event
| `on:selecting` | PlotSelectionEvent | https://plotly.com/javascript/plotlyjs-events/#select-event
| `on:sliderChange` | SliderChangeEvent | https://plotly.com/javascript/plotlyjs-events/
| `on:sliderEnd` | SliderEndEvent | https://plotly.com/javascript/plotlyjs-events/
| `on:sliderStart` | SliderStartEvent | https://plotly.com/javascript/plotlyjs-events/
| `on:sunburstClick` | SunburstClickEvent | https://plotly.com/javascript/plotlyjs-events/
| `on:transitioned` | undefined | https://plotly.com/javascript/plotlyjs-events/
| `on:transitioning` | undefined | https://plotly.com/javascript/plotlyjs-events/
| `on:transitionInterrupted` | undefined | https://plotly.com/javascript/plotlyjs-events/
| `on:unhover` | PlotMouseEvent | https://plotly.com/javascript/plotlyjs-events/
| `on:update` | PlotUpdateEvent | https://plotly.com/javascript/plotlyjs-events/
| `on:webGLContextLost` | undefined | https://plotly.com/javascript/plotlyjs-events/#additional-events
# Roadmap
 * [x] add autosizing
 * [x] add debouncing
 * [ ] add event redirecting
 * [ ] add unit tests
 * [ ] add SSR rendering to an image
