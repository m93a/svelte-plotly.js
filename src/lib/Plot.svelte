<script context="module" lang="ts">
  import type {
    Data,
    Layout,
    Config,
    PlotlyHTMLElement,
    BeforePlotEvent,
    ClickAnnotationEvent,
    FrameAnimationEvent,
    LegendClickEvent,
    PlotMouseEvent,
    PlotHoverEvent,
    PlotRelayoutEvent,
    PlotRestyleEvent,
    PlotSelectionEvent,
    SliderEndEvent,
    SliderChangeEvent,
    SliderStartEvent,
    SunburstClickEvent
  } from 'plotly.js';

  export type {
    Data,
    Layout,
    Config,
    PlotlyHTMLElement,
    BeforePlotEvent,
    ClickAnnotationEvent,
    FrameAnimationEvent,
    LegendClickEvent,
    PlotMouseEvent,
    PlotHoverEvent,
    PlotRelayoutEvent,
    PlotRestyleEvent,
    PlotSelectionEvent,
    SliderChangeEvent,
    SliderStartEvent,
    SunburstClickEvent
  };

  export type FillParent = boolean | 'width' | 'height';
  import type { DebounceSettings } from 'lodash-es';

  export interface DebounceOptions extends DebounceSettings {
    wait: number;
  }

  export interface ButtonClickedEvent {
    menu: any;
    button: any;
    active: any;
  }

  export interface PlotUpdateEvent {
    data: Data;
    layout: Layout;
  }
</script>

<script lang="ts">
  import { onMount, onDestroy, createEventDispatcher } from 'svelte';
  import { debounce as debouncify } from 'lodash-es';
  const browser = typeof window === 'object';

  const nextFrame = browser ? requestAnimationFrame : () => void 0;

  async function loadPlotly() {
    if (!browser) return;

    if (libPlotly === undefined) {
      if (window.Plotly) {
        libPlotly = window.Plotly;
      } else {
        const p = await import('plotly.js-dist');
        if (libPlotly === undefined) libPlotly = 'default' in p ? p.default : p;
      }
    }
  }

  const DEFAULT_WIDTH = 500;
  const DEFAULT_HEIGHT = 300;

  // events
  interface Events {
    afterExport: undefined;
    afterPlot: undefined;
    animated: undefined;
    animating: undefined;
    animatingFrame: FrameAnimationEvent;
    animationInterrupted: undefined;
    autoSize: undefined;
    beforeExport: undefined;
    beforeHover: PlotMouseEvent;
    beforePlot: BeforePlotEvent;
    buttonClicked: ButtonClickedEvent;
    click: PlotMouseEvent;
    clickAnnotation: ClickAnnotationEvent;
    deselect: undefined;
    doubleClick: undefined;
    framework: undefined;
    hover: PlotHoverEvent;
    legendClick: LegendClickEvent;
    legendDoubleClick: LegendClickEvent;
    react: PlotUpdateEvent;
    redraw: undefined;
    relayout: PlotRelayoutEvent;
    relayouting: PlotRelayoutEvent;
    restyle: PlotRestyleEvent;
    selected: PlotSelectionEvent;
    selecting: PlotSelectionEvent;
    sliderChange: SliderChangeEvent;
    sliderEnd: SliderEndEvent;
    sliderStart: SliderStartEvent;
    sunburstClick: SunburstClickEvent;
    transitioned: undefined;
    transitioning: undefined;
    transitionInterrupted: undefined;
    unhover: PlotMouseEvent;
    update: PlotUpdateEvent;
    webGLContextLost: undefined;
  }
  const events = <const>{
    plotly_afterexport: 'afterExport',
    plotly_afterplot: 'afterPlot',
    plotly_animated: 'animated',
    plotly_animating: 'animating',
    plotly_animatingframe: 'animatingFrame',
    plotly_animationinterrupted: 'animationInterrupted',
    plotly_autosize: 'autoSize',
    plotly_beforeexport: 'beforeExport',
    plotly_beforehover: 'beforeHover',
    plotly_beforeplot: 'beforePlot',
    plotly_buttonclicked: 'buttonClicked',
    plotly_click: 'click',
    plotly_clickannotation: 'clickAnnotation',
    plotly_deselect: 'deselect',
    plotly_doubleclick: 'doubleClick',
    plotly_framework: 'framework',
    plotly_hover: 'hover',
    plotly_legendclick: 'legendClick',
    plotly_legenddoubleclick: 'legendDoubleClick',
    plotly_react: 'react',
    plotly_redraw: 'redraw',
    plotly_relayout: 'relayout',
    plotly_relayouting: 'relayouting',
    plotly_restyle: 'restyle',
    plotly_selected: 'selected',
    plotly_selecting: 'selecting',
    plotly_sliderchange: 'sliderChange',
    plotly_sliderend: 'sliderEnd',
    plotly_sliderstart: 'sliderStart',
    plotly_sunburstclick: 'sunburstClick',
    plotly_transitioned: 'transitioned',
    plotly_transitioning: 'transitioning',
    plotly_transitioninterrupted: 'transitionInterrupted',
    plotly_unhover: 'unhover',
    plotly_update: 'update',
    plotly_webglcontextlost: 'webGLContextLost'
    // TODO add all plotly_${traceType}click
  };
  const dispatch = createEventDispatcher<Events>();

  //
  // Bind Props
  /** The HTML element wrapping the plot. */
  export let element: HTMLDivElement | undefined = undefined;
  /** The inner HTML element containing the plot. */
  export let plot: PlotlyHTMLElement | undefined = undefined;

  //
  // Input Props

  /**
   * Alternative Plotly bundle to use. If `undefined`, it defaults to
   * the `plotly.js-dist` package; if null, no plot will be drawn and
   * no library will be downloaded.
   */
  export let libPlotly: typeof import('plotly.js-dist') | null | undefined = undefined;

  /**
   * Array of trace data, see https://plot.ly/javascript/reference/
   */
  export let data: Data[];

  /**
   * Layout of the plot, see https://plot.ly/javascript/reference/#layout
   */
  export let layout: Partial<Layout> | undefined = undefined;

  /**
   * Configuration, see https://plot.ly/javascript/configuration-options/
   */
  export let config: Partial<Config> | undefined = undefined;

  /**
   * Automatically resize the plot to fill the width and/or
   * height of its parent element.
   */
  export let fillParent: FillParent = false;

  /**
   * Debounce all changes to the plot.
   */
  export let debounce: number | DebounceOptions = 0;

  /**
   * Because of an [upstream bug](https://github.com/m93a/svelte-plotly.js/issues/10),
   * changes in the `config` prop don't always render.
   * This prop enables a walkaround, which makes sure
   * that changes in `config` just work.
   *
   * - `'static-plot'` – when changing `config`, briefly disable
   * chart interactivity to force update
   *
   * - `'none'` – disable the walkaround; updating mode bar
   * buttons might not work
   */
  export let configReactivityStrategy: 'none' | 'static-plot' = 'static-plot';

  /**
   * Class attribute that will be passed to the HTML element
   * wrapping the plot
   */
  let className = '';
  export { className as class };

  // set up
  onMount(async () => {
    (window as any).global = window;
    await loadPlotly();
  });

  // state props
  let datarevision = 0;
  let previousLib = libPlotly;
  let previousPlot = plot;
  let width: number = DEFAULT_WIDTH;
  let height: number = DEFAULT_HEIGHT;

  // updates
  $: debounceWait = typeof debounce === 'object' ? debounce.wait : (debounce ?? 0);
  $: debounceOptions = typeof debounce === 'object' ? debounce : {};
  $: data, (datarevision = (datarevision + 1) % 1000);
  $: layout_ = { datarevision, width, height, ...layout };
  $: config_ = { displaylogo: false, ...config };

  let configUpdated = false;
  $: config_, (configUpdated = true);

  $: {
    if (element && previousLib !== libPlotly) {
      previousLib?.purge(element);
      plot = undefined;
    }
    previousLib = libPlotly;
    loadPlotly();
  }
  $: if (previousPlot !== plot) {
    for (const [plotlyEvent, svelteEvent] of Object.entries(events)) {
      previousPlot?.removeAllListeners?.(plotlyEvent);
      plot?.on(plotlyEvent as any, (e: any) => dispatch(svelteEvent, e));
    }
    previousPlot = plot;
  }

  const drawUndebounced = async () => {
    if (!libPlotly || !element) return;

    if (configUpdated && configReactivityStrategy === 'static-plot') {
      configUpdated = false;
      await libPlotly.react(element, data, layout_, {
        ...config_,
        staticPlot: !config_.staticPlot
      });
    }

    plot = await libPlotly.react(element, data, layout_, config_);
  };

  $: draw = debouncify(drawUndebounced, debounceWait, debounceOptions);
  $: libPlotly, element, data, layout_, config_, configUpdated, draw();

  // destroy
  onDestroy(() => element && libPlotly?.purge(element));

  // autosizing
  $: fillParent, nextFrame(onResize);
  $: fillParentWidth = fillParent === true || fillParent === 'width';
  $: fillParentHeight = fillParent === true || fillParent === 'height';
  $: parent = element?.parentElement ?? undefined;
  let lastParent: typeof parent = undefined;
  $: {
    parentMounted(parent);
    parentUnmounted(lastParent);
    lastParent = parent;
  }

  let resizeObserver: ResizeObserver | undefined;
  onMount(() => (resizeObserver = new ResizeObserver(onResize)));
  const parentMounted = (parent: Element | undefined) => parent && resizeObserver?.observe(parent);
  const parentUnmounted = (parent: Element | undefined) =>
    parent && resizeObserver?.unobserve(parent);

  function onResize() {
    if (!parent || !element) return;

    const { offsetHeight, offsetWidth } = parent;
    const { paddingLeft, paddingTop, paddingRight, paddingBottom } =
      window.getComputedStyle(parent);

    const maxWidth = offsetWidth - parseInt(paddingLeft) - parseInt(paddingRight);
    const maxHeight = offsetHeight - parseInt(paddingTop) - parseInt(paddingBottom);

    width = fillParentWidth ? maxWidth : DEFAULT_WIDTH;
    height = fillParentHeight ? maxHeight : DEFAULT_HEIGHT;
  }
</script>

<div
  class={className}
  class:fillParent
  class:fillParentWidth
  class:fillParentHeight
  bind:this={element}
/>

<style lang="scss">
  .fillParent {
    overflow: visible;
  }
  .fillParentWidth {
    width: 0 !important;
    max-width: 0 !important;
  }
  .fillParentHeight {
    height: 0 !important;
    max-height: 0 !important;
  }
</style>
