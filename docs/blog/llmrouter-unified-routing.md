---
title: Why LLM routing needs a shared foundation
hide:
  - navigation
---

<article class="llmr-article">
  <header class="llmr-article__hero">
    <p class="llmr-article__eyebrow">LLMRouter · Research note</p>
    <h1>Why LLM routing needs a shared foundation</h1>
    <p class="llmr-article__dek">A unified library, benchmark, and analysis framework for deciding which model should answer each request.</p>
    <p class="llmr-article__meta">August 2026 · LLMRouter team</p>
  </header>

  <div class="llmr-article__body">
    <p class="llmr-article__lead">The LLM ecosystem is heterogeneous by design. Frontier systems, open-weight alternatives, specialist models, and low-cost serving options each excel in different settings. The useful question is no longer “which model should we use?” but “which model should answer this request, under this budget, for this user?”</p>

    <p>That is the routing problem. In practice, a router must account for the request, the available model pool, price, task-specific quality, interaction history, and sometimes user preferences. Existing approaches span binary quality prediction, cost-aware cascades, graph-based ranking, multi-turn policies, and personalization.</p>

    <div class="llmr-article__problems">
      <article><span>01</span><h3>Fragmented methods</h3><p>Routers are released with incompatible interfaces, supervision, and candidate pools. Comparing two methods can inadvertently mean comparing two entire experimental stacks.</p></article>
      <article><span>02</span><h3>Incomplete evaluation</h3><p>Training and evaluating a router requires knowing how every candidate responds to every query, including both task quality and the cost of reaching an answer.</p></article>
    </div>

    <section class="llmr-work-closes-gap">
      <div class="llmr-work-closes-gap__mark"><img src="../../assets/logo.png" alt="LLMRouter logo"></div>
      <div>
        <p class="llmr-section-kicker">This work closes the gap</p>
        <ol>
          <li><strong>We introduce a unified routing formulation.</strong> Every router is specified through a context encoder, model encoder, scoring function, decision rule, and learning signal.</li>
          <li><strong>We build xRouteBench.</strong> An automated pipeline records candidate responses, task metrics, and token-level costs under one shared protocol.</li>
          <li><strong>We release LLMRouter.</strong> More than 16 representative routers share reusable data, training, inference, evaluation, and deployment infrastructure.</li>
        </ol>
      </div>
    </section>

    <h2>A unified formulation for routing</h2>

    <p>LLMRouter treats routing as a sequential decision process: maximize response quality while controlling inference cost. Under this formulation, every router is built from the same five components: a context encoder, a model encoder, a scoring function, a decision rule, and a learning signal.</p>

    <p>The formulation does not make all routers identical; it makes their differences explicit. A single-turn router can focus on the current query. A multi-turn or agentic router can incorporate history and intermediate evidence. A personalized router can condition on user signals. These choices become comparable design decisions rather than properties buried inside separate implementations.</p>

    <p>The same abstraction organizes three router families: <strong>single-turn</strong> methods route directly from a query; <strong>multi-turn and agentic</strong> methods reason over an evolving interaction state; and <strong>personalized</strong> methods condition decisions on information about the user.</p>

    <figure class="llmr-article__figure llmr-article__figure--framed">
      <img src="../../assets/blog/unified-routing.png" alt="Unified LLM routing formulation with single-turn, multi-turn, and personalized router families">
      <figcaption><strong>Unified routing formulation.</strong> The observed state determines which information a router sees; context and model encoders feed a shared routing decision, which can dispatch once or continue over multiple turns.</figcaption>
    </figure>

    <h2>LLMRouter: a reusable library</h2>

    <p>LLMRouter implements this formulation as a modular library. Adding a router requires only a routing method and a loss function; the surrounding workflow for data construction, training, inference, and evaluation is reused unchanged. Switching the router, candidate pool, or training objective becomes a configuration change—not a new codebase.</p>

    <pre class="llmr-code"><code>from llmrouter.models.meta_router import MetaRouter

class MyRouter(MetaRouter):
    def route_single(self, query_input: dict) -&gt; dict:
        # score candidate models for this request
        return {"model_name": "best_candidate"}

    def route_batch(self, batch: list) -&gt; list:
        return [self.route_single(query) for query in batch]</code></pre>

    <p>The library includes more than 16 representative routers across single-turn, multi-turn, and personalized settings. A unified CLI, OpenAI-compatible serving, and a ComfyUI interface for visual prototyping help carry the same routing logic from a reproducible experiment into a deployed application.</p>

    <figure class="llmr-article__figure llmr-article__figure--framed">
      <img src="../../assets/blog/library-overview.png" alt="LLMRouter library architecture with data engine, trainer, route engine, evaluation, and deployment">
      <figcaption><strong>One reusable workflow.</strong> Data collection, router training, route execution, evaluation, and deployment are separate modules that work across router families.</figcaption>
    </figure>

    <h2>Evaluation must include both quality and cost</h2>

    <p>Evaluating a router is more demanding than evaluating one model: the system must know how every candidate performs on every query. LLMRouter builds this supervision automatically by running a candidate pool across benchmarks, applying task-specific metrics, and recording token-level cost. The resulting <strong>xRouteBench</strong> spans generic LLM tasks, memory, image and video understanding, time-series, and personalized routing.</p>

    <p>Every router is then tested against the same candidate pool, queries, metrics, and quality–cost protocol. Instead of reducing performance to one aggregate score, the evaluation sweeps from performance-first to cost-sensitive operating points. The deployment trade-off is therefore visible rather than hidden behind an average.</p>

    <div class="llmr-benchmark-stats" aria-label="xRouteBench at a glance">
      <div><strong>18</strong><span>candidate models</span></div>
      <div><strong>5</strong><span>routing tracks</span></div>
      <div><strong>16+</strong><span>implemented routers</span></div>
      <div><strong>5</strong><span>quality–cost settings</span></div>
    </div>

    <div class="llmr-track-strip" aria-label="xRouteBench tracks">
      <span>Generic LLM tasks</span><span>Memory</span><span>Vision &amp; video</span><span>Time-series</span><span>Personalization</span>
    </div>

    <h2>What the study finds</h2>

    <div class="llmr-article__findings">
      <p><strong>Learned routing creates meaningful headroom.</strong> Across the study, learned routers improve by 14.6% relative over the strongest fixed-model baseline.</p>
      <p><strong>There is no universally best router.</strong> The leading method changes across tasks and as cost pressure increases, making the operating point part of the routing decision itself.</p>
      <p><strong>Extra rounds are not automatically better.</strong> Multi-turn methods can add redundant information and cost when one well-chosen route already suffices.</p>
      <p><strong>Personalization matters.</strong> Conditioning on user context improves routing quality in preference-oriented settings, while the representation of that context remains consequential.</p>
    </div>

    <section class="llmr-results-gallery">
      <figure>
        <img src="../../assets/blog/rank-heatmap.png" alt="Router rankings across cost weights">
        <figcaption><strong>Rankings move with the budget.</strong> As cost receives more weight, lightweight methods can overtake quality-first leaders.</figcaption>
      </figure>
      <figure>
        <img src="../../assets/blog/perf-vs-price.png" alt="Performance versus cost across LLM routing methods">
        <figcaption><strong>Routing exposes a frontier.</strong> Higher inference cost often improves quality, but the largest model is not a universally efficient choice.</figcaption>
      </figure>
    </section>

    <h2>Beyond offline benchmarks</h2>

    <p>LLMRouter also studies deployment settings where the routing decision is part of a real system rather than a static table. In a Slack deployment, 15 users contributed 40 sessions and 234 pairwise preference records. On held-out sessions, PersonalizedRouter reached <strong>83.05</strong>, showing both the promise of user-conditioned routing and the need to validate simulated results against real feedback.</p>

    <p>The same idea extends to multi-agent systems: each functional node can receive a model selected for its own prompt instead of sharing one base model. Across five coordination topologies, six of seven learned routers surpassed the largest-model baseline on average; MFRouter reached <strong>76.48</strong> versus <strong>71.48</strong> for always selecting the largest model.</p>

    <h2>Explore the results</h2>

    <p>We release the library, benchmark, and experimental artifacts so routing research can be compared on common ground—and so practical deployments can select the point on the quality–cost frontier that fits their needs.</p>

    <p class="llmr-article__cta"><a href="../../leaderboard/">Explore xRouteBench on the leaderboard <span aria-hidden="true">→</span></a> <a href="https://huggingface.co/datasets/ulab-ai/xRouteBench" target="_blank" rel="noopener">View xRouteBench on Hugging Face <span aria-hidden="true">→</span></a></p>
  </div>
</article>
