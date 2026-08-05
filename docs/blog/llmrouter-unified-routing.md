---
title: What makes LLM routing hard—and how a unified foundation helps
hide:
  - navigation
---

<article class="llmr-article">
  <header class="llmr-article__hero">
    <p class="llmr-article__eyebrow">LLMRouter · Research note</p>
    <h1>What makes LLM routing hard—and how a unified foundation helps</h1>
    <p class="llmr-article__dek">A unified library, benchmark, and analysis framework for deciding which model should answer each request.</p>
    <p class="llmr-article__meta">August 2026 · LLMRouter team</p>
  </header>

  <div class="llmr-article__body">
    <p class="llmr-article__lead">The LLM ecosystem is heterogeneous by design. Frontier models, open-weight alternatives, specialist models, and low-cost serving options all excel in different places. The useful question is no longer “which model should we use?” but “which model should answer this request, under this budget, for this user?”</p>

    <p>That is the routing problem. It sounds simple, but a router must account for the query, available models, price, task-specific quality, history, and sometimes user preferences. Existing methods approach this through binary quality prediction, cascades, graph-based ranking, multi-turn policies, or personalization.</p>

    <p>Systematically studying these approaches has been difficult for two reasons. First, router implementations use incompatible interfaces and assumptions, so comparing methods often means comparing entire experimental stacks. Second, routing supervision and evaluation require knowing how every candidate model performs on every query—both in response quality and inference cost.</p>

    <section class="llmr-work-closes-gap">
      <div class="llmr-work-closes-gap__mark"><img src="../../assets/logo.png" alt="LLMRouter logo"></div>
      <div>
        <p class="llmr-section-kicker">This work closes the gap</p>
        <ol>
          <li><strong>We introduce a unified routing formulation.</strong> A router is described by a context encoder, model encoder, scoring function, decision rule, and learning signal.</li>
          <li><strong>We build xRouteBench.</strong> An automated pipeline collects candidate responses, task metrics, and token-level costs under one shared evaluation protocol.</li>
          <li><strong>We release LLMRouter.</strong> More than 16 representative routers share the same data, training, inference, and deployment infrastructure.</li>
        </ol>
      </div>
    </section>

    <figure class="llmr-article__figure">
      <img src="../../assets/llmrouter_.png" alt="Overview of the LLMRouter workflow">
      <figcaption>LLMRouter shares the workflow from data construction to training, inference, and evaluation.</figcaption>
    </figure>

    <h2>A unified formulation for routing</h2>

    <p>LLMRouter describes routing as a sequential decision process that aims to maximize response quality while controlling inference cost. Under this view, every router is composed from the same five pieces: a context encoder, a model encoder, a scoring function, a decision rule, and a learning signal.</p>

    <p>This decomposition does not force all routers to look alike. Instead, it exposes the choices that make them different. A single-turn router can focus on the current query. A multi-turn or agentic router can include history and intermediate evidence. A personalized router can include user signals. The shared interface lets those choices be studied as design decisions rather than as isolated implementations.</p>

    <div class="llmr-routing-flow" role="img" aria-label="The five components of an LLMRouter routing decision">
      <span>Query, history &amp; user context</span><b>→</b><span>Context encoder</span><b>+</b><span>Model encoder</span><b>→</b><span>Score &amp; decision</span><b>→</b><span>Selected model</span>
    </div>

    <p>The same abstraction organizes three router families: <strong>single-turn</strong> methods route directly from a query; <strong>multi-turn and agentic</strong> methods reason over an evolving interaction state; and <strong>personalized</strong> methods condition decisions on information about the user.</p>

    <h2>LLMRouter: a reusable library</h2>

    <p>LLMRouter implements the formulation as a modular library. A new router needs only a routing method and a loss function; the surrounding workflow for data construction, training, inference, and evaluation is reused unchanged. Switching the router, candidate pool, or training objective becomes a configuration change rather than a new codebase.</p>

    <pre class="llmr-code"><code>from llmrouter.models.meta_router import MetaRouter

class MyRouter(MetaRouter):
    def route_single(self, query_input: dict) -&gt; dict:
        # score candidate models for this request
        return {"model_name": "best_candidate"}

    def route_batch(self, batch: list) -&gt; list:
        return [self.route_single(query) for query in batch]</code></pre>

    <p>The library includes more than 16 representative routers across single-turn, multi-turn, and personalized settings. It also provides a unified CLI, OpenAI-compatible serving, and a ComfyUI interface for visual prototyping—so the same routing logic can move from a reproducible experiment toward a deployed application.</p>

    <h2>Evaluation must include both quality and cost</h2>

    <p>Evaluating a router is more demanding than evaluating one model: the system needs to know how every candidate performs on every query. LLMRouter builds this supervision automatically by running a candidate pool across benchmarks, applying task-specific metrics, and recording token-level cost. The result is <strong>xRouteBench</strong>, which covers generic LLM tasks, memory, image and video understanding, time-series, and personalized routing.</p>

    <p>Every router is then tested against the same candidate pool, queries, metrics, and quality–cost protocol. Rather than reporting a single score, the evaluation sweeps operating points from performance-first to cost-sensitive. That makes a deployment trade-off visible instead of hiding it behind an average.</p>

    <div class="llmr-benchmark-stats" aria-label="xRouteBench at a glance">
      <div><strong>18</strong><span>candidate models</span></div>
      <div><strong>5</strong><span>routing tracks</span></div>
      <div><strong>16+</strong><span>implemented routers</span></div>
      <div><strong>5</strong><span>quality–cost settings</span></div>
    </div>

    <h2>What the study finds</h2>

    <div class="llmr-article__findings">
      <p><strong>Learned routing creates real headroom.</strong> Across the study, learned routers improve by 14.6% relative over the strongest fixed-model baseline.</p>
      <p><strong>There is no universally best router.</strong> The winning method changes across tasks and as cost pressure increases, so the operating point is part of the routing decision.</p>
      <p><strong>Extra rounds are not automatically better.</strong> Multi-turn methods can add redundant information and cost when a single well-chosen route already suffices.</p>
      <p><strong>Personalization matters.</strong> Conditioning on user context improves routing quality on preference-oriented settings, while the way that context is represented remains important.</p>
    </div>

    <h2>Explore the results</h2>

    <p>We release the library, benchmark, and experimental artifacts so that routing research can be compared on common ground—and so that practical deployments can choose the point on the quality–cost frontier that fits their needs.</p>

    <p class="llmr-article__cta"><a href="../../leaderboard/">Explore xRouteBench on the leaderboard <span aria-hidden="true">→</span></a> <a href="https://huggingface.co/datasets/ulab-ai/xRouteBench" target="_blank" rel="noopener">View xRouteBench on Hugging Face <span aria-hidden="true">→</span></a></p>
  </div>
</article>
