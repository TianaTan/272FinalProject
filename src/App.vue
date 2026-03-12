<template>
  <main class="app-shell">
    <header class="app-header">
      <h1>Do different communities systematically favor different genres?</h1>
      <p class="tagline">Anime is discussed and rated by communities across many online platforms, each with its own audience and cultural context. While these platforms often evaluate the same titles, their rating patterns do not always align.</p>
      <p class="tagline">In this project, we compare anime ratings from three major platforms: <a href="https://myanimelist.net/" target="_blank" class="platform-link">MyAnimeList</a>, <a href="https://www.imdb.com/" target="_blank" class="platform-link">IMDb</a>, and <a href="https://bgm.tv/" target="_blank" class="platform-link">Bangumi</a>. Rather than simply asking which platform gives higher scores, we investigate a deeper question: do different communities systematically favor different genres?</p>
      <p class="subtitle" v-if="globalState.activeStep !== null">
        <!-- <span class="step-indicator">Step {{ globalState.activeStep + 1 }}</span> -->
      </p>
    </header>

    <section class="scrolly-layout">
      <aside class="sticky-panel">
        <div class="chart-stack" aria-live="polite">
          <div
            v-for="(graph, index) in graphComponents"
            :key="graph.key"
            class="chart-layer"
            :style="getChartLayerStyle(index, activeStep, stepProgress, graphComponents.length)"
            :aria-hidden="index !== activeStep"
          >
            <component :is="graph.component" />
          </div>
        </div>
      </aside>

      <section class="story-panel" aria-label="Scrollytelling steps">
        <article
          v-for="(step, index) in steps"
          :key="step.id"
          class="story-step step"
          :class="{ 'is-active': index === activeStep }"
          :data-step="index"
        >
          <h2>{{ step.title }}</h2>
          <p>{{ step.body }}</p>
        </article>
      </section>
    </section>
  </main>
</template>

<script setup>
import { inject, ref } from 'vue';
import GraphOne from './components/graphs/GraphOne.vue';
import GraphTwo from './components/graphs/GraphTwo.vue';
import GraphThree from './components/graphs/GraphThree.vue';
import GraphFour from './components/graphs/GraphFour.vue';
import GraphFive from './components/graphs/GraphFive.vue';
import { useScrollama } from './composables/useScrollama';
import { getChartLayerStyle } from './utils/chartTransition';

const globalConfig = inject('globalConfig');
const globalState = inject('globalState');

// Placeholder story copy for each chart panel.
const steps = [
  {
    id: 'step-1',
    title: '🎬 Are rating patterns consistent across genres and platforms?',
    body: 'This heatmap shows the average percentile rank for each genre on each platform, revealing genre-platform affinity patterns. Each cell\'s color intensity indicates how highly a particular community rates a specific genre: warmer colors suggest the community rates that genre more favorably, while cooler colors indicate lower preference. The horizontal distribution patterns directly answer our central question—notice how some genres cluster in high percentiles on one platform but not others. This visual evidence confirms that different communities do indeed systematically favor different genres.'
  },
  {
    id: 'step-2',
    title: '📊 How do individual anime titles distribute within each platform\'s rating ecosystem?',
    body: 'While the heatmap shows aggregate genre patterns, the beeswarm plot reveals individual title distributions. Each point represents one anime, colored by its platform, with vertical position indicating its percentile rank. Comparing the three swarms side-by-side makes platform-specific rating patterns visible: you\'ll notice distinctive clustering and spread differences across platforms. This granular view lets you explore not just statistical trends but also recognize specific titles and understand how individual works fit into each community\'s overall rating distribution.'
  },
  {
    id: 'step-3',
    title: '🔍 Does popularity influence rating stability across communities?',
    body: 'The scatter plot explores a critical dimension: do anime with high popularity consistently receive similar ratings across platforms? The x-axis shows popularity (number of voters), while the y-axis represents rating stability—measured by variance in an anime\'s percentile ranks across the three platforms. The color gradient reveals a key pattern: more stable (green) vs. more divergent (red) ratings. Notice how certain popular titles achieve consensus (low variance, stable colors), while others show high divergence despite popularity. This visualization answers whether platform audiences agree on quality consistency or if their preferences diverge significantly.'
  },
  {
    id: 'step-4',
    title: '⭐ Which genres show systematic platform preference?',
    body: 'Radar charts compare how different platforms rate the same genre, with each platform\'s contribution shown as a dimension. The shape of the radar reflects the community\'s distinctive rating pattern for that genre: pointed radars indicate uneven preference (communities disagree), while rounded shapes indicate consensus. By filtering genres below, you can directly observe which specific genres generate the strongest platform differences. This view transforms numerical data into recognizable shapes, making it intuitive to identify genres where communities systematically diverge in their preferences.'
  },
  {
    id: 'step-5',
    title: '🎯 How do these genre preferences translate into different ranking structures?',
    body: 'The parallel coordinates plot visualizes ranking variance—when communities rank the same anime differently, their lines cross and diverge across platforms. Stable agreements show as horizontal lines (green), while strong disagreement creates steep slopes (red). This final visualization connects everything: the genre preferences we\'ve discovered throughout this journey directly cause the ranking structures visible here. By following the lines, you see how platform bias at the genre level manifests in individual title rankings, creating the systematic differences in how communities evaluate anime. This completes the analytical arc from macro patterns to micro rankings.'
  }
];

const graphComponents = [
  { key: 'graph-1', component: GraphOne },
  { key: 'graph-2', component: GraphTwo },
  { key: 'graph-3', component: GraphThree },
  { key: 'graph-4', component: GraphFour },
  { key: 'graph-5', component: GraphFive }
];

const activeStep = ref(0);
const stepProgress = ref(0);

globalState.activeStep = activeStep.value;

useScrollama({
  stepSelector: '.story-step',
  offset: globalConfig.scrollOffset,
  progress: true,
  onStepEnter: ({ index }) => {
    activeStep.value = index;
    stepProgress.value = 0;
    globalState.activeStep = index;
  },
  onStepProgress: ({ index, progress }) => {
    // Keep transition progress tied only to the currently active step.
    if (index === activeStep.value) {
      stepProgress.value = progress;
    }
  }
});
</script>

<style scoped>
.app-header h1 {
  font-size: 1.8rem;
  margin: 0 0 0.5rem 0;
}

.app-header .tagline {
  font-size: 1.1rem;
  margin-top: 0.5rem;
  opacity: 0.95;
}

.platform-link {
  color: #00D9FF;
  font-weight: 600;
  text-decoration: none;
  transition: opacity 0.3s ease;
}

.platform-link:hover {
  opacity: 0.8;
  text-decoration: underline;
}

.app-header .subtitle {
  margin-top: 1rem;
  font-size: 0.95rem;
}

.step-indicator {
  display: inline-block;
  background: rgba(255, 255, 255, 0.2);
  padding: 0.4rem 0.8rem;
  border-radius: 20px;
  font-weight: 600;
  backdrop-filter: blur(10px);
}
</style>
