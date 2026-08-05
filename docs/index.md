---
title: LLMRouter
hide:
  - navigation
---

<div class="tsr-home">
  <section class="tsr-hero">
    <div class="tsr-hero__inner">
      <div class="tsr-mark"><img src="assets/logo.png" alt="LLMRouter logo"></div>
      <h1><span class="tsr-wordmark">LLMRouter</span></h1>
      <p class="tsr-hero__title">A Unified Library, Evaluation, and Analysis for LLM Routing</p>
      <p class="tsr-authors">Tao Feng · Haozhen Zhang · Zijie Lei · Haodong Yue · Chongshan Lin · Jiaxuan You</p>
      <p class="tsr-affiliations">University of Illinois Urbana-Champaign · University of Maryland, College Park</p>
      <div class="tsr-links" aria-label="Project resources">
        <a href="https://github.com/ulab-uiuc/LLMRouter" target="_blank" rel="noopener"><span aria-hidden="true">⌘</span> Code</a>
        <a href="https://pypi.org/project/llmrouter/"><span aria-hidden="true">◈</span> PyPI</a>
        <a href="leaderboard/"><span aria-hidden="true">▤</span> Leaderboard</a>
        <a href="https://join.slack.com/t/llmrouteropen-ri04588/shared_invite/zt-3jz3cc6d1-ncwKEHvvWe0OczHx7K5c0g" target="_blank" rel="noopener"><span aria-hidden="true">#</span> Slack</a>
      </div>
    </div>
  </section>

  <section class="tsr-section tsr-overview" id="overview">
    <div class="tsr-container">
      <h2>Overview</h2>
      <div class="tsr-prose">
        <p>No single large language model is optimal across all queries and budget constraints. <strong>LLMRouter</strong> is an open-source foundation for selecting the right model for each request, making routing methods easier to develop, compare, and deploy under a shared quality–cost objective.</p>
        <p>It unifies routers that were previously implemented in incompatible stacks—from simple quality predictors and cost-aware cascades to graph-based, multi-turn, and personalized policies—and provides the shared infrastructure needed to evaluate them fairly.</p>
      </div>
      <figure class="tsr-figure">
        <img src="assets/llmrouter_.png" alt="LLMRouter overview">
        <figcaption><strong>LLMRouter</strong> joins data construction, router training, inference, and evaluation in one reusable workflow.</figcaption>
      </figure>
    </div>
  </section>

  <section class="tsr-section tsr-framework" id="framework">
    <div class="tsr-container">
      <h2>A Unified Routing Formulation</h2>
      <p class="tsr-lede">Every router is represented as a sequential decision process. A shared formulation turns different routing ideas into comparable design choices.</p>
      <div class="tsr-cards">
        <article><b>01</b><h3>Routing state</h3><p>Encode the query, optional user context, interaction history, and candidate-model information.</p></article>
        <article><b>02</b><h3>Score &amp; decide</h3><p>Estimate candidate compatibility, then dispatch, escalate, or stop according to the operating budget.</p></article>
        <article><b>03</b><h3>Learn &amp; evaluate</h3><p>Optimize a learning signal that balances task-specific response quality against inference cost.</p></article>
      </div>
      <div class="tsr-family-line"><span>Single-turn</span><i></i><span>Multi-turn &amp; agentic</span><i></i><span>Personalized</span></div>
    </div>
  </section>

  <section class="tsr-section tsr-benchmark" id="benchmark">
    <div class="tsr-container">
      <h2>xRouteBench</h2>
      <div class="tsr-prose">
        <p>LLMRouter constructs routing supervision by running a candidate pool across benchmarks, scoring each response with its task metric, and recording token-level cost. Every router then faces the same queries, models, metrics, and quality–cost protocol.</p>
        <p>The resulting benchmark spans generic LLM tasks, memory-augmented reasoning, image and video understanding, time-series, and personalized routing. This makes it possible to compare both performance and cost instead of optimizing one in isolation.</p>
      </div>
      <div class="tsr-track-grid">
        <span>Generic LLM tasks</span><span>Memory</span><span>Vision &amp; video</span><span>Time-series</span><span>Personalization</span>
      </div>
      <p class="tsr-center"><a class="tsr-inline-link" href="leaderboard/">Explore xRouteBench results →</a></p>
    </div>
  </section>

  <section class="tsr-section tsr-findings" id="findings">
    <div class="tsr-container">
      <h2>Key Findings</h2>
      <div class="tsr-finding-list">
        <p><strong>Learned routing improves over fixed-model baselines.</strong> The empirical study finds a 14.6% relative improvement over the strongest fixed-model baseline.</p>
        <p><strong>No router dominates every deployment.</strong> Rankings change across tasks and reverse as cost constraints become tighter, making the operating point a first-class decision.</p>
        <p><strong>User context changes the right answer.</strong> Personalized routing delivers consistent gains when preference and interaction history are available.</p>
      </div>
    </div>
  </section>

  <section class="tsr-section tsr-cite" id="citation">
    <div class="tsr-container">
      <h2>BibTeX</h2>
      <pre><code>@misc{feng2026llmrouter,
  title  = {LLMRouter: A Unified Library, Evaluation, and Analysis for LLM Routing},
  author = {Tao Feng and Haozhen Zhang and Zijie Lei and Haodong Yue and Chongshan Lin and Jiaxuan You},
  year   = {2026}
}</code></pre>
    </div>
  </section>
</div>
